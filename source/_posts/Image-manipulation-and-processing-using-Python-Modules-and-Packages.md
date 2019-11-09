---
title: Image manipulation and processing using Python Modules and Packages
top: false
cover: false
toc: true
mathjax: false
date: 2019-10-14 19:47:00
password:
summary: 在使用Python对图像进行处理的时候，我们会用到许多的Python模块、包、或者库， 本文特此总结，用到啥，记录一下
tags:
- CV
- Python
categories:
- CV
---

## 混杂知识点

### 1. 包，模块，库的区分

- package：就是个带 __init__.py 的文件夹，并不在乎里面有什么，不过一般来讲会包含一些 packages/modules

- module：一个 .py 文件就是个 module

- lib：抽象概念，和另外两个不是一类，只要你喜欢，什么都是 lib，就算只有个 hello world, scrapy、flask、Django、numpy、scipy、NLTK、jieba，一般都被认为是 lib，因为关注点不是他们的代码是怎么组织的。

### 2. 图片格式转换

#### Numpy.array <=> PIL.Image

[source](https://blog.csdn.net/liuweizj12/article/details/80221537)

- Numpy.array => PIL.Image : img = Image.fromarray(img)
- PIL.Image => Numpy.array : img = np.array(img)


#### PIL.Image => Bytes:
[source](https://stackoverflow.com/a/56055505/12214101)
```python
from PIL import Image
import io

def image_to_byte_array(image:Image):
  imgByteArr = io.BytesIO()
  image.save(imgByteArr, format=image.format)
  imgByteArr = imgByteArr.getvalue()
  return imgByteArr
```

#### Bytes => Numpy.array

```python
import requests
import matplotlib.pyplot as plt
img = requests.get(url).content
img = plt.imread(BytesIO(img), "jpg")
```

#### GIF ==> PNG

```python
def iter_frames(gif):
    try:
        i= 0
        while 1:
            gif.seek(i)
            imframe = gif.copy()
            if i == 0:
                palette = imframe.getpalette()
            else:
                imframe.putpalette(palette)
            yield imframe
            i += 1
    except EOFError:
        pass
# http://cn.voidcc.com/question/p-ecfkyjoi-bks.html
# Bytes to GIF
# url = 'xxx'
# gif = Image.open(BytesIO(requests.get(url).content)))

gif = Image.open('captcha.gif')
for i, frame in enumerate(iter_frames(gif)):
  pass
```

#### 3. 分离文件名与扩展名

[source](https://blog.csdn.net/u011509971/article/details/70244688)

```python
import os
path_01='D:/User/wgy/workplace/data/notMNIST_large.tar.png'
path_02='D:/User/wgy/workplace/data/notMNIST_large'
root_01=os.path.splitext(path_01)
root_02=os.path.splitext(path_02)
print(root_01)
print(root_02)
```

结果：
```
('D:/User/wgy/workplace/data/notMNIST_large.tar', '.png')
('D:/User/wgy/workplace/data/notMNIST_large', '')
```


## Numpy

## PIL

## Scipy

## Open-cv
