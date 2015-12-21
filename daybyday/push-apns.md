# APNS

## certificates

[APNS远程推送证书的申请和制作——详细解析](http://my.oschina.net/u/1245365/blog/297913#OSC_h1_7)

证书的申请流程  
1. 申请本地证书
2. 在开发者网站上申请证书 \*.cer dev证书只用于指定测试设备
3. x.cer -> y.p12 这里我就可以用了y.p12
4. 制作pem证书 x.cer -> x-cer.pem; y.p12 -nocer-> y-key.pem; x-cer.pem + y-key.pem -> push.pem

> 验证 `openssl s_client -connect gateway.sandbox.push.apple.com:2195 -cert x-cer.pem -key y-key.pem`

所需证书申请：
1. 申请本地证书  **CSR**
2. 在开发者网站上申请证书 **Apple Push Notifications service SSL Certificates**
3. 申请描述文件（开发和发布描述文件） **.CER**
4. 为服务器制作证书 **.pem + 密码**
  * .p12  `openssl x509 -in 证书名字.cer -inform der -out push_developer_cer.pem`
  * pushKey.pem `openssl pkcs12 -nocerts -out pushKey.pem -in p12名字.p12`
  * 最终.pem `cat 下载证书生成的.pem p12生成的.pem > 最终证书的名字.pem`
5. 测试证书是否有效
  * developer `openssl s_client -connect gateway.sandbox.push.apple.com:2195 -cert 下载证书生成的.pem -key p12生成的.pem`
  * production `openssl s_client -connect gateway.push.apple.com:2195 -cert 下载证书生成的.pem -key p12生成的.pem`

> pem -> p12
> `openssl pkcs12 -export -in apns_dev.pem -out paypal.p12`

## apns Payload

- [APNS推送通知消息负载内容和本地格式字符串](http://www.cnblogs.com/taintain1984/p/3727440.html)

## 开源学习

- [java-apns](https://github.com/notnoop/java-apns) 一个国外人的项目，目前仍在不断更新
- [dbay-apns-for-java](https://github.com/RamosLi/dbay-apns-for-java) 国人的一个基于`java-apns`的一个项目

## 问题

* IOS 如何推送到指定用户设备上？

### badge 数值如何更新

http://stackoverflow.com/questions/8734078/ios-badge-number-live-update
http://stackoverflow.com/questions/1942605/push-notification-badge-auto-increment
http://stackoverflow.com/questions/11153631/increment-the-push-notification-badge-iphone?rq=1
http://stackoverflow.com/questions/7793529/notification-badge-number-increment?rq=1

## Q&A

### 证书问题

要使用 Flash CS5 开发 iPhone 应用程序，则必须使用 P12 证书文件。基于从 Apple 收到的 Apple iPhone 开发人员证书文件生成此证书。
将从 Apple 收到的开发人员证书文件转换成 PEM 证书文件。从 OpenSSL bin 目录运行以下命令行语句：

`openssl x509 -in developer_identity.cer -inform DER -out developer_identity.pem -outform PEM`

如果您使用的是 Mac 计算机上钥匙串中的私钥，则将其转换成 PEM 密钥：

`openssl pkcs12 -nocerts -in mykey.p12 -out mykey.pem`

现在，您可以基于密钥和 PEM 版本的 iPhone 开发人员证书生成有效的 P12 文件：

`openssl pkcs12 -export -inkey mykey.pem -in developer_identity.pem -out iphone_dev.p12`

如果您使用的是 Mac OS 钥匙串中的密钥，则使用上一步骤中生成的 PEM 版本。否则搜索，请使用以前生成的 OpenSSL 密钥（位于 Windows 上）。
