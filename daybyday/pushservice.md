Push Notification Service
=============


## 推送实现

* 使用GCM服务（Google Cloud Messaging）
  * androidpn
  * C2DM/GCM
* 使用XMPP协议（Openfire + Spark + Smack）
* 使用MQTT协议（MQTT: MQ Telemetry Transport）
* 使用HTTP轮循方式


## 调研目前现存的PNS

* [Amazon Simple Notification Service](http://docs.aws.amazon.com/zh_cn/sns/latest/dg/welcome.html)
*


### Amazon SNS

Amazon Simple Notification Service (Amazon SNS) 是一项 Web 服务，用于协调和管理向订阅终端节点或客户交付或发送消息的过程。 在 Amazon SNS 中有两种类型的客户端：发布者和订阅者，也称为生产者和消费者。发布者通过创建消息并将消息发送至主题与订阅者进行异步交流，主题是一个逻辑访问点和通信渠道。订阅者（即 Web 服务器、电子邮件地址、Amazon SQS 队列）在其订阅主题后通过受支持协议（即 Amazon SQS、HTTP/S、电子邮件、SMS）中的一种使用或接收消息或通知。

![](http://docs.aws.amazon.com/zh_cn/sns/latest/dg/images/sns-how-works.png)

使用 Amazon SNS 时，您（作为所有者）可通过定义确定哪些发布者和订阅者能就主题进行交流的策略来创建主题和控制对主题的访问权。发布者会发送消息至他们创建的主题或他们有权发布的主题。发布者可用发送消息至主题来代替在每个消息中包括特定目标地址。Amazon SNS 使主题与订阅了该主题的一列订阅者相匹配，并将消息发送给这些订阅者中的每一个。每个主题都有一个独特的名称，用户为发布者识别 Amazon SNS 终端节点，从而发布消息和订阅者以注册通知。订阅者接收所有发布至他们所订阅主题的消息，且一个主题的所有订阅者收到的消息都相同。

![](http://docs.aws.amazon.com/zh_cn/sns/latest/dg/images/sns-mobile-subscribe.png)


### Baidu PNS

### APNS

iOS应用则会在被切换到后台一段时间后停止运行，这时候如果想要达到信息的获取则只能依靠APNS（Apple Push Notification Service），由服务器主动进行推送。

由于APNS最高只支持256个字节的消息体，并且推送消息中大量重复的内容也会造成流量成本，这时可以使用本地化字符串。本地化字符串是在客户端中的Localizable.strings文件中定义一对键值对，服务器只需要提供指定字符串的键和动态生成的参数即可生成一条完整的推送消息。

并不是每条消息都绝对会推送成功。例如当用户卸载掉该应用时，所对应的token就已经失效了，这时可以通过service（java-apns）的getInactiveDevices方法获得失效的token并作出处理。


## 推送相关第三方包

* [java-apns](https://github.com/notnoop/java-apns)
* [dbay-apns](https://github.com/RamosLi/dbay-apns-for-java) 作者在分析java-apns后写出的

### java-apns


## TODO

* 消息队列
* publish/subscribe model
	* redis RMQ
	* JMS
* GCM & APNS
