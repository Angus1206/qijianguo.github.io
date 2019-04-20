---
title: Kafka总结
categories: Kafka
tags: kafka
date: 2018-04-07 12:57:09
---

### 什么是Kafka
由LinkedIn在11年开源，还有例如：分布式数据同步系统Databus、高性能计算引擎Cubert、Java异步处理框架ParSeq。 
<!-- more -->
![* streaming platform has three key capabilities:
	* Publish and subscribe to streams of records, similar to a message queue or enterprise messaging system.
	* Store streams of records in a fault-tolerant durable way.
	* Process streams of records as they occur.](https://img-blog.csdnimg.cn/20190331203053858.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190331203206419.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)
## Kafka基本概念
* Producer：消息和数据的生产者，向Kafka的一个topic发布消息的进程/代码/服务
* Consumer：消息和数据的消费者，订阅数据（Topic）并且处理其发布的消息的进程/代码/服务。
* Consumer Group：逻辑概念，对于同一个topic，会广播给不同的group，一个group中，只有一个consumer可以消费该消息。
* Broker：物理概念，Kafka集群中的每个Kafka节点。
* Topic：逻辑概念，Kafka消息的类别，对数据进行区分、隔离。
* Partition：物理概念，Kafka下数据存储的基本单元。一个Topic数据会被分散存储到多个Partition，每个Partition是有序的。
* Replication：同一个Partition可能会有多个Replica，多个Replica之间数据是一样的。
* Replication Leader：一个Partition的多个Replica上，需要个Leader负责该Partition上与Producer和Consumer交互，Leader有且只有一个。
* ReplicaManager：负责管理当前broker所有分区和副本的信息，处理KafkaController发起的一些请求，副本状态的切换、添加/读取消息等。

## Kafka基本结构

![ ](https://img-blog.csdnimg.cn/20190331210155617.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190331210315693.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)

## Kafka消息结构

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190331210426387.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)
## Kafka的特点

分布式
* 多分区
* 多副本
* 多订阅者
* 基于Zookeeper消息调度
 
高性能
* 高吞吐量
* 低延迟
* 高并发
* 时间复杂度为O(1)

持久性与扩展性
* 数据可持久化
* 容错性
* 支持在线水平扩展
* 消息自动平衡

## Kafka应用场景

* 消息队列
* 行为跟踪
* 元信息监控
* 日志收集
* 流处理
* 事件源
* 持久性日志（commit log）

## Kafka应用案例

## Kafka代码案例

## Kafka高级特性之消息事务

## Kafka高级特性之零拷贝
