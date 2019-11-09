---
title: Apache Kafka Notes
top: false
cover: false
toc: true
mathjax: false
date: 2019-11-03 11:52:37
img: https://i.loli.net/2019/11/03/6sQFDPoNqngkBTH.jpg
password:
summary:
tags:
- Kafka
categories:
- Big Data
---

![](https://i.loli.net/2019/11/03/KztPYLn7qFTNegB.jpg)

## 一、概述
****
> 在大数据技术中主要有两个挑战：收集数据和分析并处理数据，消息系统将会让这些事情变得更简单。
> Appache Kafka 最初由Linkedin公司开发，Linkedin于2010年贡献给了Apache基金会并成为顶级开源项目。Kafka是一种快速、可扩展的、设计内在就是分布式的，分区的和可复制的提交日志服务。

### 1.1 消息系统

> 消息系统负责从一个程序到另一个程序，是程序可以专注于数据而不用关心数据的传递和共享。
> 分布式消息传递基于可靠消息队列, 消息在客户端应用程序和消息传递系统之间异步传递消息

两种类型消息系统
  + 点对点
    消息保存在队列，一个或者多个消费者同时消耗消息，一个消息最多一个消费者消费

  + 发布-订阅（pub-sub)
    消息保存在主题中，消费者可以订阅一个或者多个主题的消息，发布者和订阅者

![点对点](https://i.loli.net/2019/11/03/3DyicoCmYGgAa5x.png)![发布-订阅](https://i.loli.net/2019/11/03/V9BcRuNbTqarZ6X.png)


### 1.2 Kafka

> + Kafka 是一个分布式发布 - 订阅消息系统,
> + Kafka 可以处理大量的数据，并使您能够将消息从一个端点传递到另一个端点。
> + Kafka 适合离线和在线消息消费。
> + kafka 非常快，执行200万写/秒
> + Kafka 消息保留在磁盘上，并在群集内复制以防止数据丢失。

用例：
+ 指标： Kafka通常用于操作监控数据。 这涉及聚合来自分布式应用程序的统计信息，以产生操作数据的集中馈送。

+ 日志聚合解决方案：  Kafka可用于跨组织从多个服务收集日志，并使它们以标准格式提供给多个服务器。

+ 流处理：流式的框架(如Storm和Spark Streaming)从主题中读取数据，对其进行处理，并将处理后的数据写入新主题，供用户和应用程序使用。 Kafka的强耐久性在流处理的上下文中也非常有用。

## 二、基础
****

### 2.1 优点

> 解耦、冗余、扩展性、灵活性和峰值处理能力、可恢复性、缓冲、异步通信

+ **解耦** : 在处理过程插入了一个隐含的、基于数据的接口层，两边的应用程序都只需要实现这个接口，不用关心数据的传递。两边的程序可以独立的扩展或修改处理过程，只需要保证接口约束。

+ **冗余** : 消息队列会将数据持久化到处理系统明确数据完全处理完毕，防止处理数据过程失败导致数据丢失。

+ **扩展性** : 由于解耦了处理过程，易于调节消息入队和处理的频率，只需增加处理过程，不需要改变代码和参数。

+ **灵活性和峰值处理能力** : 有利于访问量剧增的情况，将消息存下来，防止应用崩溃。

+ **可恢复性** : 降低了耦合度，部分组件的崩溃不会影响到整个系统，会在系统恢复后继续处理

+ **顺序保证** : Kafka 保证一个分区内消息的有序性

+ **缓冲** : 消息队列通过一个缓冲层来帮助任务最高效率的执行

+ **异步通信** : 消息放入不用立即取，需要时再取出来

### 2.2 术语

![Kafka数据流程图](https://i.loli.net/2019/11/03/9gZActqbVJmlG6R.jpg)

> Producer、Consumer、Consumer Group、Kafka Cluster、Broker、Topic、Partition、Partition offset、 Replicas of partition、Leader、Follower

+ **Producer** : 生产者，数据发布者。

+ **Consumer** : 消费者，数据订阅者。

+ **Consumer Group** : 消费者分组，同一分组内的消费者共同消费消息，特定消息只会被分组内任一消费者消费一次。

+ **Kafka Cluster** : Kafka 集群 Kafka有多个服务器被称为Kafka 可以扩展Kafka集群，无需停机。 这些集群用于管理消息数据的持久性和复制。

+ **Broker** : Broker，即Kafka的服务器，负责存储消息，Kafa集群中的一台或多台服务器统称为 Broker。

+ **Topic** : 主题，由用户定义并配置在Kafka服务器，用于建立生产者和消息者之间的订阅关系：生产者发送消息到指定的Topic下，消息者从这个Topic下消费消息。

+ **Partition** : 分区，Partition，一个Topic下面会分为很多分区。Topic物理上的分组，一个topic可以分为多个partition保存在一个或者多个Broker，每个 partition 是一个有序的队列。partition中的每条消息都会被分配一个有序的 id（offset）

+ **Partition offset** : 分区偏移，offset 为一个 long 型的数字，是每个分区的消息的唯一序列标识。

+ **Replicas of partition** : 分区备份，副本只是一个分区的备份。 副本从不读取或写入数据。 它们用于防止数据丢失。如果一个topic的副本数为3，那么Kafka将在集群中为每个partition创建3个相同的副本。集群中的每个broker存储一个或多个partition。

+ **Leader** : 领导者，负责给定分区的所有读取和写入的服务器节点。 每个分区都有一个服务器充当Leader

+ **Follower** : 追随者，跟随领导者指令的节点被称为Follower。 如果领导失败，一个追随者将自动成为新的领导者。 跟随者作为正常消费者，拉取消息并更新其自己的数据存储。

**数据均衡，水平扩展**

> + 如果在一个Topic中有N个Partition，Kafka Cluster有N个Broker，则每个Broker存储一个Partiction。
> + 如果在一个Topic中有N个Partition，Kafka Cluster有N+M个Broker，则其中N个Broker各存储一个Partition，剩下M个Broker 不存储该Topic的 Particition
> + 如果在一个Topic中有N个Partition，Kafka Cluster有少于N个Broker, 则一个Broker存储该Topic的一个或多个Partition，但容易导致Kafka集群数据不均衡。


## 三、架构
***

### 3.1 架构图

![基于 Kafka-ZooKeeper 的分布式消息队列系统总体架构](https://i.loli.net/2019/11/03/jWnGoPUxHJ1g5wV.jpg)

>如上图所示，一个典型的 Kafka 体系架构包括若干 Producer（可以是服务器日志，业务数据，页面前端产生的 page view 等等），若干 Broker（Kafka 支持水平扩展，一般 Broker 数量越多，集群吞吐率越高），若干 Consumer (Group)，以及一个 Zookeeper 集群。Kafka 通过 Zookeeper 管理集群配置，选举 Leader，以及在 Consumer Group 发生变化时进行 rebalance。Producer 使用 push(推) 模式将消息发布到 broker，Consumer 使用 pull(拉) 模式从 Broker 订阅并消费消息。


### 3.2 组件

 + **Broker**
 Kafka集群通常由多个Broker组成以保持负载平衡。 KafkaBroker是无状态的，所以他们使用ZooKeeper来维护它们的集群状态。 一个KafkaBroker实例可以每秒处理数十万次读取和写入，每个Broker可以处理TB的消息，而没有性能影响。 Kafka Broker 的 Leader 选举可以由ZooKeeper完成
 + **ZooKeeper**
ZooKeeper用于管理和协调Kafka Broker。 ZooKeeper服务主要用于通知生产者和消费者Kafka系统中存在任何新Broker或Kafka系统中Broker失败。 根据Zookeeper接收到关于Broker的存在或失败的通知，然后生产者和消费者采取决定并开始与某些其他Broker协调他们的任务。Apache Zookeeper，它是一个分布式配置和同步服务。 Zookeeper是Kafka代理和消费者之间的协调接口。 Kafka服务器通过Zookeeper集群共享信息。 Kafka在Zookeeper中存储基本元数据，例如关于 **主题，代理，消费者偏移(队列读取器)** 等的信息。由于所有关键信息存储在Zookeeper中，并且它通常在其整体上复制此数据，因此Kafka代理/ Zookeeper的故障不会影响Kafka集群的状态。 一旦Zookeeper重新启动，Kafka将恢复状态。 这为Kafka带来了零停机时间
 + **Producer**
生产者将数据推送给Broker。 当新Broker启动时，所有生产者搜索它并自动向该新Broker发送消息。 Kafka生产者不等待来自Broker的确认，并且发送消息的速度与Broker可以处理的一样快
 + **Consumer**
因为KafkaBroker是无状态的，这意味着消费者必须通过使用分区偏移来维护已经消耗了多少消息。 如果消费者确认特定的消息偏移，则意味着消费者已经消费了所有先前的消息。 消费者向Broker发出异步拉取请求，以具有准备好消耗的字节缓冲区。 消费者可以简单地通过提供偏移值来快退或跳到分区中的任何点。 消费者偏移值由ZooKeeper通知。


## 四、流程

### 4.1 发布订阅消息传递的工作流

+ 生产者定期向主题发送消息。
+ Kafka代理存储为该特定主题配置的分区中的所有消息。 它确保消息在分区之间平等共享。 如果生产者发送两个消息并且有两个分区，Kafka将在第一分区中存储一个消息，在第二分区中存储第二消息。
+ 消费者订阅特定主题。
+ 一旦消费者订阅主题，Kafka将向消费者提供主题的当前偏移，并且还将偏移保存在Zookeeper系综中。
+ 消费者将定期请求Kafka(如100 Ms)新消息。
+ 一旦Kafka收到来自生产者的消息，它将这些消息转发给消费者。
+ 消费者将收到消息并进行处理。
+ 一旦消息被处理，消费者将向Kafka Broker发送确认。
+ 一旦Kafka收到确认，它将偏移更改为新值，并在Zookeeper中更新它。 由于偏移在Zookeeper中维护，消费者可以正确地读取下一个消息，即使在服务器暴力期间。
+ 以上流程将重复，直到消费者停止请求。
+ 消费者可以随时回退/跳到所需的主题偏移量，并阅读所有后续消息

### 4.2 队列消息/用户组的工作流

在队列消息传递系统而不是单个消费者中，具有相同组ID 的一组消费者将订阅主题。 简单来说，订阅具有相同 Group ID 的主题的消费者被认为是单个组，并且消息在它们之间共享。 让我们检查这个系统的实际工作流程。

+ 生产者定期向某个主题发送消息。
+ Kafka将所有消息存储在为该特定主题配置的分区中。
+ 单个消费者订阅特定主题，假设 主题为Topic-01， Group ID 为 Group-1 。
+ Kafka以与发布 - 订阅消息相同的方式与消费者交互，直到新消费者以相同的组ID Group-1 订阅相同主题 Topic-01 。
+ 一旦新消费者到达，Kafka将其操作切换到共享模式，并在两个消费者之间共享数据。 此共享将继续，直到用户数达到为该特定主题配置的分区数。
+ 一旦消费者的数量超过分区的数量，新消费者将不会接收任何进一步的消息，直到现有消费者取消订阅任何一个消费者。 出现这种情况是因为 **Kafka中的每个消费者将被分配至少一个分区，并且一旦所有分区被分配给现有消费者，新消费者将必须等待。**
这个功能被称为 **消费群**。 以同样的方式，Kafka将以非常简单和有效的方式提供这两个系统。



## 五、安装

### Mac 环境

1. JDK8 环境安装

  ```bash
  brew cask install homebrew/cask-versions/adoptopenjdk8
  ```
2. kafka 安装
  ```bash
  brew install kafka
  ```
  安装会依赖zookeeper。
  安装目录：/usr/local/Cellar/kafka/2.3.1

3. 配置文件位置：/usr/local/etc/kafka
  + /usr/local/etc/kafka/server.properties
  + /usr/local/etc/kafka/zookeeper.properties

4. 启动zookeeper:
  ```bash
  zookeeper-server-start /usr/local/etc/kafka/zookeeper.properties
  ```
5. 启动kafka:
  ```bash
  kafka-server-start /usr/local/etc/kafka/server.properties
  ```

  **可以zookeeper和kafka一起启动**
  ```bash
  zookeeper-server-start /usr/local/etc/kafka/zookeeper.properties & kafka-server-start /usr/local/etc/kafka/server.properties
  ```

6. 本地测试
>  启动zookeeper 和 kafka 后新开一个terminal window

  + 创建topic : 使用单个分区和只有一个副本创建一个名为“test”的主题

  ```bash
  kafka-topics --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
  ```

  + 查看创建的topic

  ```bash
  kafka-topics --list --zookeeper localhost:2181
  ```

  + 生产消息

  ```bash
  kafka-console-producer --broker-list localhost:9092 --topic test
  ```

  + 消费消息

  新开一个terminal window 输入下方命令，然后再生产窗口输入消息
  ```bash
  kafka-console-consumer --bootstrap-server localhost:9092 --topic test --from-beginning
  ```

  ![生产-消费](https://i.loli.net/2019/11/04/A9mjxtc7sGUL8Sh.png)

## 六、使用

### PyKafka

  [官方文档](https://pykafka.readthedocs.io/en/latest/#)
  [python通过Pykafka库来连接kafka并收发消息](https://my.oschina.net/u/2263272/blog/788006)

**[参考资料]**
+ [w3cschool·Apache Kafka教程](https://www.w3cschool.cn/apache_kafka/)
+ [Kafka学习之路（一）Kafka的简介](https://www.cnblogs.com/qingyunzong/p/9004509.html)
+ [Kafka教程(一)Kafka入门教程](https://blog.csdn.net/yuan_xw/article/details/51210954)
+ [Kafka基本架构和原理（学习点）](https://juejin.im/post/5d315daee51d4556bb4cd45a)
+ [mac 安装kafka](https://www.jianshu.com/p/1f6387d18989)
+ [PyKafka官方文档](https://pykafka.readthedocs.io/en/latest/#)
**[声明]**
资源源自网络，侵删！
