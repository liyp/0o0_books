RabbitMQ
============

## AMQP - Advanced Message Queuing Protocol

- RabbitMQ protocol [[-]](https://www.rabbitmq.com/protocol.html)

```
# AMQ model
                               Server
                  +----------------------------+
                  |        Virtual host        |
                  |    +-------------------+   |
                  |    |      Exchange     |   |
 +-------------+  |    |     +-------+     |   |
 | Publisher   | ----------> |       |     |   |
 | application |  |    |     +---+---+     |   |
 +-------------+  |    |         |         |   |
                  |    |      Message      |   |
                  |    |      Queue        |   |
 +-------------+  |    |     +-------+     |   |
 | Consumer    | <---------- +-------+     |   |
 | application |  |    |     +-------+     |   |
 +-------------+  |    |     +-------+     |   |
                  |    +-------------------+   |
                  +----------------------------+
```

- Producer :  消息生产者，其将消息发送到交换机(exchange)中。
- Exchange :  交换机， 其最先接收到待处理消息，并根据RouterKey的值，将消息转发到指定队列里。
- RouterKey : 路由关键字，交换机通过其进行路由转发。
- Queue:    消息队列
- Consumer : 消息消费者，其直接监听处理队列中的消息。

> 高级消息队列协议（AMQP）是一个异步消息传递所使用的应用层协议规范。作为线路层协议，而不是API（例如JMS），AMQP客户端能够无视消息的来源任意发送和接受信息。现在，已经有相当一部分不同平台的服务器和客户端可以投入使用。[[-]](http://www.infoq.com/cn/articles/AMQP-RabbitMQ)

## Port Access

- 4369 (epmd), 25672 (Erlang distribution)
- 5672, 5671 (AMQP 0-9-1 without and with TLS)
- 15672 (if management plugin is enabled)
- 61613, 61614 (if STOMP is enabled)
- 1883, 8883 (if MQTT is enabled)

## Exchange

| Name             | Default pre-declared names
| ---------------  | ----------------------- |
| Direct exchange  | (Empty string) and amq.direct
| Fanout exchange  | amq.fanout
| Topic exchange   | amq.topic
| Headers exchange | amq.match (and amq.headers in RabbitMQ)

## Queue

1. 持久化(durable)
   可设置队列持久化：队列在服务重启后，仍然存在。
   可设置消息持久化：未消费的消息在服务重启后，仍存在于队列中。
2. ACK机制
   可设置发送应答(confirm)：同步发送每个消息，得到ACK后，确认发送成功。
   可设置接收应答(ack)：消息在成功消费后，发送ACK，服务才销毁掉该消息。
3. 负载均衡
   对同一个队列，可有任意多个消费端，其会采用RR轮询方式分发消息。
4. 集群
   rabbitmq服务的集群设置比较方便，使用命令行工具rabbitmqctl, 执行命令 rabbitmqctl join_cluster rabbit@ip-xx-x-x-xx , 即可组成一个集群。其集群是一个去中心化的集群。任意一台断网或者挂掉，不影响其他机器的运行。
    队列可建立在任意一台机器上，消费方可连接任意一台机器， 利用erlang语言特性的进程通信机制，消息完成集群之间转发，最终可从生产者到达消费者。例如这样一个流程：
    发布消息 -> server A -> 转发 -> serverB -> 消费该消息
5. 镜像队列（实现队列主备）
   在集群之间，可建立队列镜像，当其中一台rabbitmq服务器断网或者挂掉，另一台的队列镜像依然存在，消费方可重连到其上，继续侦听该队列，完成主备切换。

## 使用&监控

### 使用

- erlang环境
- rabbitmq-server

```
创建队列
rabbitmqadmin declare queue name=xxx durable=true|false

删除队列
rabbitmqadmin delete queue name=xxx

查询队列状态(公式：rabbitmqadmin list queues {栏位1}{栏位2}...)
rabbitmqadmin list queues vhost name node messages message_stats.publish_details.rate

创建用户
rabbitmqctl add_user [用户名] [密码]
rabbitmqctl set_user_tags [usr] administrator
rabbitmqctl set_permissions [usr]  ".*"  ".*"  ".*"

创建vhost
rabbitmqctl add_vhost [虚拟主机名]

配置用户访问权限
set_permissions [-p vhost] <user> <conf> <write> <read>
```

### 监控

`sudo rabbitmq-plugins enable rabbitmq_management`

The web UI is located at: http://server-name:15672/ guest:guest 用户默认localhost登陆
