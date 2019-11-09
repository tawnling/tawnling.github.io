---
title: >-
  The ways to convert Jupyter(.ipynb) to Python(.py), HTML(.html), Markdown(.md)
  or PDF(.pdf)
top: false
cover: false
toc: true
mathjax: false
date: 2019-10-18 14:38:53
img: http://img1.imgtn.bdimg.com/it/u=2743268655,1778161208&fm=26&gp=0.jpg
password:
summary: 将ipynb转换为py,html，md，pdf等格式
tags:
- Jupyter Notebook
- tools
categories:
- Python
---

## nbconvert

> 首先我们需要安装ipython 和 nbconvert 库

+ pip install ipython
+ pip install nbconvert

## ipynb -> py

single file:
+ ipython nbconvert — to script test.ipynb

multi files:
+ ipython nbconvert — to script test1.ipynb test2.ipynb

## ipynb -> html

+ jupyter nbconvert --to html notebook.ipynb
or
+ jupyter nbconvert --to html --template basic/full notebook.ipynb

## ipynb -> md

+ jupyter nbconvert --to md notebook.ipynb

## ipynb -> pdf

+ jupyter nbconvert --to pdf notebook.ipynb

> 如果错了可以试一试安装下面这个包，大小1G多，所以还是算了吧

~~ + sudo apt-get install texlive-full ~~

## graphical interface operation

<img src="https://i.loli.net/2019/10/18/zaBhRow1D7X4JgW.png" width="50%" height="50%">
<!-- ![](https://i.loli.net/2019/10/18/zaBhRow1D7X4JgW.png) -->

## References

>1. [The easiest way to convert Jupyter(.ipynb) to Python(.py)](https://medium.com/@researchplex/the-easiest-way-to-convert-jupyter-ipynb-to-python-py-912e39f16917)
>2. [如何将ipynb转换为html，md，pdf等格式](https://blog.csdn.net/red_stone1/article/details/73380517)
