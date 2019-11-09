---
title: Some operation for Aliyun oss
top: false
cover: false
toc: true
mathjax: false
date: 2019-10-08 12:36:57
password:
summary: 对象存储服务（Object Storage Service，OSS）是一种海量、安全、低成本、高可靠的云存储服务，适合存放任意类型的文件。容量和处理能力弹性扩展，多种存储类型供选择，全面优化存储成本。
tags:
- Scripts
- oss
categories:
- Scripts
---

## 一、官方接口文档

[aliyun oss](https://help.aliyun.com/product/31815.html)

## 二、实例操作

### 1. 获取特定文件夹下的文件数量

```python
# -*- coding: utf-8 -*-
import oss2

# 阿里云主账号AccessKey拥有所有API的访问权限，风险很高。强烈建议您创建并使用RAM账号进行API访问或日常运维，请登录 https://ram.console.aliyun.com 创建RAM账号。
auth = oss2.Auth('keyid', 'secret')
# Endpoint以杭州为例，其它Region请按实际情况填写。
bucket = oss2.Bucket(auth, 'endpoint', 'bucket名称')

# 列举包含指定前缀的文件。默认列举100个文件。
res = 0
for obj in oss2.ObjectIterator(bucket, prefix = '文件名'):
  #  print(obj.key)
    res += 1
print(res)
```

### 2. 获取特定文件夹的大小，平均文件大小

```python
# -*- coding: utf-8 -*-
import oss2

# 阿里云主账号AccessKey拥有所有API的访问权限，风险很高。强烈建议您创建并使用RAM账号进行API访问或日常运维，请登录 https://ram.console.aliyun.com 创建RAM账号。
auth = oss2.Auth('keyid', 'secret')
# Endpoint以杭州为例，其它Region请按实际情况填写。
bucket = oss2.Bucket(auth, 'endpoint', 'bucket名称')

# 列举包含指定前缀的文件。默认列举100个文件。
res = 0
length = 0
for obj in oss2.ObjectIterator(bucket, prefix = '文件名'):
    # print(obj.key)
    res += 1
    length += obj.size
print('directory: 文件名' + '  length:' + str(length / 1024 / 1024) + "MB")
print('the number of file: ' + str(res))
print('average length: ' + str(length/res/1024/1024) + "MB")
```
