# Push Notification Service

## 推送实现

* 使用GCM服务
  * C2DM/GCM \(Google Cloud Messaging\)
* 使用XMPP协议
  * Openfire + Spark + Smack
  * [androidpn](http://sourceforge.net/projects/androidpn/)
* 使用MQTT协议（MQTT: Message Queuing Telemetry Transport）
  * Really Small Message Broker \(RSMB\) 一个简单的MQTT代理
* 使用HTTP轮循方式

REF

* [Android实现推送方式解决方案](http://www.cnblogs.com/hanyonglu/archive/2012/03/04/2378971.html)
* [netty-million-level-push-service-design-points](http://www.infoq.com/cn/articles/netty-million-level-push-service-design-points/)

## 调研目前现存的PNS

* [Amazon Simple Notification Service](http://docs.aws.amazon.com/zh_cn/sns/latest/dg/welcome.html)
* [LeanCloud](https://leancloud.cn/docs/push_guide.html)
* [魔方](http://www.imofan.com/docs/doc_ios_push.jsp)
* [GCM](https://developers.google.com/cloud-messaging/)
* [Baidu Push](http://push.baidu.com/doc/restapi/restapi)
  * [baidu android push](http://www.infoq.com/cn/articles/baidu-android-cloud-push/)

### Amazon SNS

Amazon Simple Notification Service \(Amazon SNS\) 是一项 Web 服务，用于协调和管理向订阅终端节点或客户交付或发送消息的过程。 在 Amazon SNS 中有两种类型的客户端：发布者和订阅者，也称为生产者和消费者。发布者通过创建消息并将消息发送至主题与订阅者进行异步交流，主题是一个逻辑访问点和通信渠道。订阅者（即 Web 服务器、电子邮件地址、Amazon SQS 队列）在其订阅主题后通过受支持协议（即 Amazon SQS、HTTP/S、电子邮件、SMS）中的一种使用或接收消息或通知。

![](http://docs.aws.amazon.com/zh_cn/sns/latest/dg/images/sns-how-works.png)

使用 Amazon SNS 时，您（作为所有者）可通过定义确定哪些发布者和订阅者能就主题进行交流的策略来创建主题和控制对主题的访问权。发布者会发送消息至他们创建的主题或他们有权发布的主题。发布者可用发送消息至主题来代替在每个消息中包括特定目标地址。Amazon SNS 使主题与订阅了该主题的一列订阅者相匹配，并将消息发送给这些订阅者中的每一个。每个主题都有一个独特的名称，用户为发布者识别 Amazon SNS 终端节点，从而发布消息和订阅者以注册通知。订阅者接收所有发布至他们所订阅主题的消息，且一个主题的所有订阅者收到的消息都相同。

![](http://docs.aws.amazon.com/zh_cn/sns/latest/dg/images/sns-mobile-subscribe.png)

### Baidu PNS

### APNS

> 详见 `push-apns.md`

iOS应用则会在被切换到后台一段时间后停止运行，这时候如果想要达到信息的获取则只能依靠APNS（Apple Push Notification Service），由服务器主动进行推送。

由于APNS最高只支持256个字节的消息体，并且推送消息中大量重复的内容也会造成流量成本，这时可以使用本地化字符串。 本地化字符串是在客户端中的Localizable.strings文件中定义一对键值对，服务器只需要提供指定字符串的键和动态生成的参数即可生成一条完整的推送消息。

并不是每条消息都绝对会推送成功。例如当用户卸载掉该应用时，所对应的token就已经失效了，这时可以通过service（java-apns）的getInactiveDevices方法获得失效的token并作出处理。

### GCM

### JDPushService

### MI-pushservice

* [xiaomi-pushservice](http://www.infoq.com/cn/news/2014/11/xiaomi-1111-pushservice)

![](http://cdn1.infoqstatic.com/statics_s2_20150922-0305/resource/news/2014/11/xiaomi-1111-pushservice/zh/resources/1.png)

## 第三方包分析

APNS 接入

* [java-apns](https://github.com/notnoop/java-apns)
* [dbay-apns](https://github.com/RamosLi/dbay-apns-for-java) 作者在分析java-apns后写出的

XMPP

* [androidpn](http://sourceforge.net/projects/androidpn/)

MQTT

* moquette
* paho.mqtt.java

### androidpn

基于**XMPP**协议。

[源码分析](http://www.riaos.com/ria/10559)

* Server部分结构

| package | introduction |
| :--- | :--- |
| `org.androidpn.server.dao`, `org.androidpn.server.model`, `org.androidpn.server.service` | 使用hibernate链接数据库并实现简单的用户登录认证，开发中可以用我们自己的认证模块替换。 |
| `org.androidpn.server.util` | 包中的类用来加载resources中的配置文件，在配置文件中可指定监听端口和ssl证书目录等属性。 |
| `org.androidpn.server.xmpp` | 包里面定义了一些异常类型，主要是包含有入口类XmppServer，这个类用来启动和停止server程序。 |
| `org.androidpn.server.xmpp.auth` | 包里面是认证的一些类，我们自己的认证模块可以在这里与androidpn进行结合。 |
| `org.androidpn.server.xmpp.codec` | 是XMPP协议的XML文件解析包，server收到和发送的消息都要通过这个包来进行xmpp协议编码和解码。 |
| `org.androidpn.server.xmpp.handler` | 包主要是对消息的处理，我们可以针对不同的消息类型定义自己的handler， |
| `org.androidpn.server.xmpp.net` | 包负责维护与client之间的持久连接，并实现了一些传输方式供发送xmpp消息时使用。 |
| `org.androidpn.server.xmpp.presence` | 里面只包含PresenceManager类，用来维护client的在线状态。 |
| `org.androidpn.server.xmpp.push` | 包里面的NotificationManager类包含有向client发送消息的接口。 |
| `org.androidpn.server.xmpp.router` | 包负责将收到的信息包发送到相应的handler进行处理，是一个路由包。 |
| `org.androidpn.server.xmpp.session` | 包定义了用来表示持久链接的session，每个session包含一条连接的状态信息。 |
| `org.androidpn.server.xmpp.ssl` | 是对连接进行ssl认证的工具包。 |

### moquette

[MQTT协议之moquette 安装使用](http://blog.csdn.net/zhu_tianwei/article/details/42983867)

### APNS

java-apns

### GCM

ref

* [http://www.cnblogs.com/zhwl/p/3370426.html](http://www.cnblogs.com/zhwl/p/3370426.html)
* [http://www.cnblogs.com/tamer/archive/2012/06/28/2568739.html](http://www.cnblogs.com/tamer/archive/2012/06/28/2568739.html)
* [https://github.com/avram](https://github.com/avram)

1. 注册得到 **API key** 1. [google apis console](https://code.google.com/apis/console) 创建项目 2. 打开GCM服务 3. 获取一个API key
   * project id: 714418754597 \(GMsender ID\)
   * api key: AIzaSyDG6dNQV6jkOuNFJ0ke6mxNp0bfAib\_dk0
2. 服务端
   * 下载 [google engine sdk](https://cloud.google.com/appengine/downloads?hl=zh-CN#Google_App_Engine_SDK_for_Java)
   * 服务端代码
3. Android端

## 消息队列 MQ

* [taobao mq](http://jm-blog.aliapp.com/?p=3483)
  * notify
  * MetaQ
* Kafka
* JeroMQ \(ZMQ\)

## 接口设计

### REST API 接口

* [Baidu Push rest API](http://push.baidu.com/doc/restapi/restapi)

API 列表

* 推送消息到但太设备/APP `/rest/0.1/push/single_[device|app]`
* 推送广播消息 `/rest/0.1/push/all_[device|app]`
* 推送消息到批量设备/APP `/rest/0.1/push/batch_[device|app]`

## TODO

* 消息队列
* publish/subscribe model
  * redis RMQ
  * JMS
* GCM & APNS

