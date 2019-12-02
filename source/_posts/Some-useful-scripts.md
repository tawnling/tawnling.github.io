---
title: Some useful scripts
top: false
cover: false
toc: true
mathjax: false
date: 2019-11-28 13:51:26
img: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1574930614907&di=f0d18ab32225005611b9982858ce41e7&imgtype=0&src=http%3A%2F%2Fimg.juimg.com%2Ftuku%2Fyulantu%2F121124%2F235013-1211241RA017.jpg
password:
summary: 一些有用的脚本
tags:
- Scripts
- json
- MongoDB
categories:
- Scripts
---

## 利用json进行MongoDB文件转移

```python
import pymongo
import json

class Transform:
    def __init__(self):
        self.MONGO_URI = 'mongodb://127.0.0.1:27017'
        self.client = pymongo.MongoClient(self.MONGO_URI)
        self.database = self.client['test']
        self.collection = self.database['your_collection_name']

    def write(self, st, num, file_path):
        cur = self.collection.find().skip(st).limit(num)
        with open(file_path.format(), "w") as f:
            for item in cur:
                del item['_id']
                json.dump(item, f, ensure_ascii=False)
                f.write('\n')
            print("{}条数据加载入{}文件完成...".format(num, file_path))

    def read(self, file_path):
        with open(file_path,'r') as load_f:
            while True:
                line = load_f.readline()
                if line:
                    r = json.loads(line)
                    print(r)
                else:
                    break

    def count(self):
        print(collection.count())

if __name__ == '__main__':
    transform = Transform()
#     transform.count()
#     transform.read("json/record_1.json")
    for i in range(0, 85):
        transform.write(i*10000, 10000, "json/record_%03d.json" % i)
```

## python 多进程的简单使用
```python
import pymongo
import json   
import multiprocessing
import sys

def worker(st, num):
    # 注意父子进程不能共用同一MongoDB connection
    MONGO_URI = 'mongodb://127.0.0.1:27017'
    client = pymongo.MongoClient(MONGO_URI)
    database = client['test']
    col1 = database['your_collection_name1']
    col2 = database['your_collection_name2']
    cur = col1.find().skip(st).limit(num)
    for item in cur:
        del item['_id']
        raw_key = item['raw_key'] = item['year'] + item['area'] + item['college'] + item['major']
        col2.update_many({'raw_key': raw_key}, {'$set': dict(item)}, upsert=True)

if __name__ == '__main__':
    st = int(sys.argv[1])
    ed = int(sys.argv[2])
    process_num = int(sys.argv[3])
    x = int((ed-st)/process_num)
    for i in range(0, process_num):
        multiprocessing.Process(target=worker, args=(st+x*i, x)).start()
```
