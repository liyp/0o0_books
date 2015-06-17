# APNS

## certificates

[APNS远程推送证书的申请和制作——详细解析](http://my.oschina.net/u/1245365/blog/297913#OSC_h1_7)

证书的申请流程  
1. 申请本地证书
2. 在开发者网站上申请证书 *.cer dev证书只用于指定测试设备
3. x.cer -> y.p12 这里我就可以用了y.p12
4. 制作pem证书 x.cer -> x-cer.pem; y.p12 -nocer-> y-key.pem; x-cer.pem + y-key.pem -> push.pem

> 验证 `openssl s_client -connect gateway.sandbox.push.apple.com:2195 -cert x-cer.pem -key y-key.pem`

## apns Payload

- [APNS推送通知消息负载内容和本地格式字符串](http://www.cnblogs.com/taintain1984/p/3727440.html)



## 开源学习

- [java-apns](https://github.com/notnoop/java-apns) 一个国外人的项目，目前仍在不断更新
- [dbay-apns-for-java](https://github.com/RamosLi/dbay-apns-for-java) 国人的一个基于`java-apns`的一个项目

## 合

* IOS 如何推送到指定用户设备上？

## 问题

### badge 数值如何更新

http://stackoverflow.com/questions/8734078/ios-badge-number-live-update
http://stackoverflow.com/questions/1942605/push-notification-badge-auto-increment
http://stackoverflow.com/questions/11153631/increment-the-push-notification-badge-iphone?rq=1
http://stackoverflow.com/questions/7793529/notification-badge-number-increment?rq=1


