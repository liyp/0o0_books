Kafka
=====

## 1. apache kafka

Kafka是一种快速、可扩展的、设计内在就是分布式的，分区的和可复制的提交日志服务。

Apache Kafka与传统消息系统相比，有以下不同：

1. 它被设计为一个分布式系统，易于向外扩展；
2. 它同时为发布和订阅提供高吞吐量；
3. 它支持多订阅者，当失败时能自动平衡消费者；
4. 它将消息持久化到磁盘，因此可用于批量消费，例如ETL，以及实时应用程序。

### 1.1 基本概念

介绍一下Kafka的基本概念。它的架构包括以下组件：

1. 话题（Topic）是特定类型的消息流。消息是字节的有效负载（Payload），话题是消息的分类名或种子（Feed）名。
2. 生产者（Producer）是能够发布消息到话题的任何对象。
3. 已发布的消息保存在一组服务器中，它们被称为代理（Broker）或Kafka集群。
4. 消费者可以订阅一个或多个话题，并从Broker拉数据，从而消费这些已发布的消息。

## 部署和运行

### kafka on docker

- [wurstmeister_kafka-docker](http://wurstmeister.github.io/kafka-docker/)
