---
title: Scrapy Introduction
top: true
cover: true
toc: true
mathjax: false
img: http://img1.imgtn.bdimg.com/it/u=3010015630,3017077777&fm=26&gp=0.jpg
date: 2019-10-03 19:18:34
password:
summary: Scrapy（/ˈskreɪpi/) 是一个Python编写的开源网络爬虫框架。它是一个被设计用于爬取网络数据、提取结构性数据的程序框架。
tags:
- Scrapy
- Web Crawler

categories:
- Web Crawler
---

Scrapy（/ˈskreɪpi/) 是一个Python编写的开源网络爬虫框架。它是一个被设计用于爬取网络数据、提取结构性数据的程序框架。

使用Scrapy框架可以很方便地爬取网站。

## 一、Scrapy入门

[菜鸟教程Scrapy入门](https://www.runoob.com/w3cnote/scrapy-detail.html)
[Scrapy官网入门教程](http://kaito-kidd.com/2016/11/01/scrapy-code-analyze-architecture/)

 1. 使用pip安装Scrapy。
 2. 使用 `scrapy startproject xxx` 新建一个Scrapy 项目。
 3. 创建一个Item类，继承scrapy.Item，明确抓取的目标。
 4. 使用 `scrapy genspider xxx "url"` 新建一个spider.
 5. 完善各个代码文件内容，详细见上方教程
 6. 使用 `scrapy crawl xxx` 开始爬取

文件目录如下图：
![scrpy实例文件目录](https://img-blog.csdnimg.cn/20190924150315205.png)
## 二、Scrapy架构图
![Scrapy架构图](https://img-blog.csdnimg.cn/20190924143708393.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1Rhd24wMDAw,size_10,color_FFFFFF,t_70)
## 三、Scrapy核心组件
如架构图中所示，Scrapy 总共有五个重要的组件和两个中间件组件：

 - **Scrapy Engine(引擎)：** 负责各个组件之间的通讯，信号、数据传递等。
 - **Scheduler(调度器)：** 接收从引擎发送过来的Requests请求并整理暂存请求，当引擎需要时交还给引擎。
 - **Downloader（下载器）：** 负责下载Scrapy 引擎发送的所有Requests请求，并将其获取到的Responses交还给Scrapy Engine(引擎)，由引擎交给Spider来处理。
 - **Spider（爬虫）:** 负责处理所有Responses,从中分析提取数据，获取Item字段需要的数据，并提交Requests给引擎。
 - **Item Pipeline(管道)：** 负责处理Spider中获取到的Item，并进行进行后期处理（详细分析、过滤、存储等）的地方。

***中间件的功能如架构图所示***

 - **Downloader Middlewares（下载中间件）：** 自定义扩展下载功能的组件。
 - **Spider Middlewares（Spider中间件）：** 自定扩展和操作引擎和Spider中间通信的功能组件（比如进入Spider的Responses;和从Spider出去的Requests）


## 四、Scrapy 运行流程
Scrapy 数据流转流程，结合架构图。
 1. **Spider** 生成 Requests 发送到 Engine, 中间经过 SpiderMiddleware处理。
 2. **Engine** 将从 Spider 处获得的 Requests 发送给 Schedule, 并向其获取待下载请求（异步）。Schedule 将Requests 整理，按一定顺序入队。
 3. **Schedule**  将处理好的需要下载的请求发送给Engine。
 4. **Engine** 得到需下载的请求后，将其发送到Downloader，中间经过DownloadMiddleware, 例如代理中间件，UA中间件等。
 5. **Downloader** 将请求下载后，生成响应Responses, 送到Engine, 中间经过DownloadMiddleware。
 6. **Engine** 将Responses 返回到Spider进行处理。中间经过一系列SpiderMiddleware。
 7. **Spider** 提取Responses 中信息构造Item 和生成新的Requests, 送到Engine，中间经过一系列SpiderMiddleware。
 8. **Engine** 将Item 送到Item Pipelines进行处理，将Requests 送到Schedule进行处理。
 9. 循环往复，直至结束。

 【声明】：本文部分内容源自Scrapy[官网](https://scrapy-chs.readthedocs.io/zh_CN/0.24/intro/tutorial.html) 和[菜鸟教程](https://www.runoob.com/w3cnote/scrapy-detail.html)，侵删。
