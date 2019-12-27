---
title: Massive text de duplication based on simhash algorithm
top: true
cover: true
toc: true
mathjax: false
img: https://i.loli.net/2019/12/27/1GYxHfAwTPEyLoj.jpg
date: 2019-10-10 14:59:25
password:
summary: SimHash是一种局部敏感hash，它也是Google公司进行海量网页去重使用的主要算法，本文利用simhash来解决海量文本去重实例问题。
tags:
- Algorithm
- NLP
categories:
- Algorithm
- NLP
---

## 一、参考资料

[使用SimHash进行海量文本去重](https://www.cnblogs.com/maybe2030/p/5203186.html) by Poll的笔记

1. simhash主要流程是：
  - 分词
  - 加权
  - 合并
  - 降维

2. 去重的主要思想是：
  - 仿照hashmap的思想重构一个适合simhash的数据结构
  - 将64bit位hash分割成多段，例如4段16位，每段作为一个map_key,并使用链表作为map_value, 存储文本hash值

接下来针对一个实例需求来具体实现。

## 二、实例

### 实例场景

  - 当前有五千万文本数据存储在mongoDB中，里面有许多重复或者大面积相似的文本，现在需要进行去重。（大面积相似的文本也需要去掉）

  - 利用simhash解决这个问题刚好， 我主要使用了jieba分词和TF-IDF进行关键词提取并加权，然后利用dict和list自己构建一个符合simhash特点的hashmap.

  - jieba分词使用参考资料:
  [Python的jieba分词及TF-IDF和TextRank 算法提取关键字](https://blog.csdn.net/sinat_34022298/article/details/75943272)


  优化tips：
    - 数据插入mongo可采用批量插入(insert_many)提高速度
    - 对于短文本的数据效果不太好，可能会导致map中key=0的链表长度过长，可以进行一下特判
    - 可以采用两个进程并发进行hash去重和数据持久化
    - 数据实在太大并且冗余度过高的情况下可以批量跑，等数据量降下来在全量

  一个不是很确定且暂未实现的优化：
    在将64位的hash值划分成4段时，建立四个hash_map, 然后依次对应存储由各自段对应所得的hash_value链表

### 实例代码

- **simhash**

```python
# -*- coding: utf-8 -*-
from jieba import lcut, analyse

def count_diff_str(str1, str2):
    res = 0
    for i in range(min(len(str1),len(str2))):
        if str1[i] != str2[i]:
            res += 1
    return res

def count_diff_int(value1, value2):
    res = 0
    value = value1 ^ value2
    x = 1
    for i in range(64):
        if x & value != 0:
            res += 1
        x <<= 1
    return res

def simhash_(sentence):
    keywords = analyse.extract_tags(sentence, topK=30, withWeight=True)
    value_list = [0 for i in range(64)]
    for item in keywords:
        # print(item[0],item[1])
        value = hash(item[0])
        x = 1
        for i in range(64):
            if x & value == 0:
                value_list[i] -= item[1]
            else:
                value_list[i] += item[1]
            x <<= 1
    value_str = ''
    value_int = 0
    x = 1
    for item in value_list:
        if item > 0:
            value_str += '1'
            value_int |= x
        else:
            value_str += '0'
        x <<= 1

    return value_str, value_int

```

+ **去重**

```python
import pymongo

MONGO_CONNECT = 'xxx' # mongo uri
MONGO_DBS = 'xxx' # mongo database name
MONGO_SOURCE_COLLECTION = 'xxx' # mongo source collectiton name
MONGO_TARGET_COLLECTION = 'xxx' # mongo target collectiton name
THRESHOLD = 3  # 局部敏感 hash 阈值， 小于这个值视为相同或者大面积重复

# 初步清洗一下文本，不用清洗可以略过
def clean_ques(ques_txt):
    # 初步清理从MongoDB上download的题目信息，主要是去掉一些css/html样式
    # txt = txt.decode('utf-8')
    tmp = 0
    res = ""
    for ch in ques_txt:
        if ch == '<':
            tmp = tmp + 1
            continue
        elif ch == '>':
            tmp = tmp - 1
            continue
        elif tmp == 0:
            res = res + ch
    res = res.replace("&nbsp;", "")
    res = res.replace("\r\n", "")
    res = res[0: res.find("收录：")]
    return res

# 二进制的str转十进制的int
def str2int(data):
    res = 0
    x = 1
    for i in range(len(data)):
        if data[i] == '1':
            res |= x
        x <<= 1
    return res

# 十进制的int转二进制的str
def int2str(data):
    res = ''
    x = 1
    for i in range(64):
        if x & data == 0:
            res += '0'
        else:
            res += '1'
        x <<= 1
    return res


hash_map = dict()

if __name__ == '__main__':

    repeated_count = 0
    count = 0
    client = pymongo.MongoClient(MONGO_CONNECT)
    db = client[MONGO_DBS]
    source_col = db[MONGO_SOURCE_COLLECTION]
    target_col = db[MONGO_TARGET_COLLECTION]
    sum_count = source_col.count()
    # 小规模测试时加limit, 正式时去掉limit
    source_cursor = source_col.find().limit(1000)

    # print(source_col.count())
    for item in source_cursor:
        count += 1
        if count % 100000 == 0:
            print("count: ", count)
            print("rate of progress: ", "%.2f%%" % (count/sum_count*100))
        ques = clean_ques(item['data'])
        # print(ques)
        old_id = item['_id']
        hash_value_str, hash_value_int = simhash_(ques)
        flag = True
        for i in range(4):
            key = str2int(hash_value_str[i*16:i*(i+1)*16])
            content_list = hash_map.get(key, None)
            if not content_list:
                new_list = [hash_value_int]
                hash_map[key] = new_list
            elif not flag:
                content_list.append(hash_value_int)
                hash_map[key] = content_list
            else:
                for item in content_list:
                    if count_diff_int(item, hash_value_int) < THRESHOLD:
                        flag = False
                        break
                if not flag:
                    content_list.append(hash_value_int)
                    hash_map[key] = content_list
        if flag:
            data = {'ques': ques, 'old_id': old_id, 'hash_value': str(hash_value_int)}
            target_col.insert(data)
        else:
            repeated_count += 1

    print("total documents: ", sum_count)
    print("total count: ", count)
    print("repeated count: ", repeated_count)
    print("valid count: ", count - repeated_count)
    print("valid rate: ", 1 - repeated_count/count)
    print("repeated rate: ", repeated_count/count)
```

+ **simhash test**

```python
sentence1 = "I don't like the first one．I like _________．（　　）A．twoB．secondC．the second "
sentence2 = 'To stay awake， he coffee and ordered _______．（　　）A．．otherC．the othersD．another           '

str1, _ = simhash_(sentence1)
str2, _ = simhash_(sentence2)
print(str1)
print(str2)

print(count_diff_str(str1, str2))
```
