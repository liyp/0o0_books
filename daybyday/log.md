# LOG

## 简述+扯淡

* [最佳日志实践](http://www.bitstech.net/2014/01/07/log-best-practice/)

对于 **日志级别** 的分类，有以下参考：

* FATAL — 表示需要立即被处理的系统级错误。当该错误发生时，表示服务已经出现了某种程度的不可用，系统管理员需要立即介入。这属于最严重的日志级别，因此该日志级别必须慎用，如果这种级别的日志经常出现，则该日志也失去了意义。通常情况下，一个进程的生命周期中应该只记录一次FATAL级别的日志，即该进程遇到无法恢复的错误而退出时。当然，如果某个系统的子系统遇到了不可恢复的错误，那该子系统的调用方也可以记入FATAL级别日志，以便通过日志报警提醒系统管理员修复；
* ERROR — 该级别的错误也需要马上被处理，但是紧急程度要低于FATAL级别。当ERROR错误发生时，已经影响了用户的正常访问。从该意义上来说，实际上ERROR错误和FATAL错误对用户的影响是相当的。FATAL相当于服务已经挂了，而ERROR相当于好死不如赖活着，然而活着却无法提供正常的服务，只能不断地打印ERROR日志。特别需要注意的是，ERROR和FATAL都属于服务器自己的异常，是需要马上得到人工介入并处理的。而对于用户自己操作不当，如请求参数错误等等，是绝对不应该记为ERROR日志的；
* WARN — 该日志表示系统可能出现问题，也可能没有，这种情况如网络的波动等。对于那些目前还不是错误，然而不及时处理也会变为错误的情况，也可以记为WARN日志，例如一个存储系统的磁盘使用量超过阀值，或者系统中某个用户的存储配额快用完等等。对于WARN级别的日志，虽然不需要系统管理员马上处理，也是需要及时查看并处理的。因此此种级别的日志也不应太多，能不打WARN级别的日志，就尽量不要打；
* INFO — 该种日志记录系统的正常运行状态，例如某个子系统的初始化，某个请求的成功执行等等。通过查看INFO级别的日志，可以很快地对系统中出现的WARN,ERROR,FATAL错误进行定位。INFO日志不宜过多，通常情况下，INFO级别的日志应该不大于TRACE日志的10%；
* DEBUG or TRACE — 这两种日志具体的规范应该由项目组自己定义，该级别日志的主要作用是对系统每一步的运行状态进行精确的记录。通过该种日志，可以查看某一个操作每一步的执行过程，可以准确定位是何种操作，何种参数，何种顺序导致了某种错误的发生。可以保证在不重现错误的情况下，也可以通过DEBUG（或TRACE）级别的日志对问题进行诊断。需要注意的是，DEBUG日志也需要规范日志格式，应该保证除了记录日志的开发人员自己外，其他的如运维，测试人员等也可以通过DEBUG（或TRACE）日志来定位问题；

**Java日志框架**

* Log4j 或 Log4j 2 - Apache的开源项目，通过使用Log4j，我们可以控制日志信息输送的目的地是控制台、文件、GUI组件、甚至是套接口服务器、NT的事件记录器、UNIX Syslog守护进程等；用户也可以控制每一条日志的输出格式；通过定义每一条日志信息的级别，用户能够更加细致地控制日志的生成过程。这些可以通过一个配置文件（XML或Properties文件）来灵活地进行配置，而不需要修改程序代码。Log4j 2则是前任的一个升级，参考了Logback的许多特性；
* Logback - Logback是由log4j创始人设计的又一个开源日记组件。logback当前分成三个模块：logback-core,logback- classic和logback-access。logback-core是其它两个模块的基础模块。logback-classic是log4j的一个改良版本。此外logback-classic完整实现SLF4J API使你可以很方便地更换成其它日记系统如log4j或JDK14 Logging；
* java.util.logging - JDK内置的日志接口和实现，功能比较简单…；
* Slf4j - SLF4J是为各种Logging API提供一个简单统一的接口），从而使用户能够在部署的时候配置自己希望的Logging API实现；
* Apache Commons Logging - Apache Commons Logging （JCL）希望解决的问题和Slf4j类似。

  选项太多了的后果就是选择困难症，我的看法是没有最好的，只有最合适的。在比较关注性能的地方，选择Logback或自己实现高性能Logging API可能更合适；在已经使用了Log4j的项目中，如果没有发现问题，继续使用可能是更合适的方式；我一般会在项目里选择使用Slf4j, 如果不想有依赖则使用java.util.logging或框架容器已经提供的日志接口。

### 配置文件+格式

log4j.appender.rollingfile.layout.ConversionPattern=\[%d\] \[%-5p\] \[%t\] \[%F:%L\] - %m%n

```text
说明：
%d：时间，精确到毫秒
%p：日志级别
%t：线程名
%F:%L：类名与行号
%m：输出代码中日志的具体信息
%n：换行
打印出来效果：
[2015-08-20 12:51:23,677] [INFO ] [main] [AppServer.java:73] - Starting 3 services.
[2015-08-20 12:51:23,681] [INFO ] [main] [DubboService.java:43] - Starting dubbo services
[2015-08-20 12:51:25,232] [INFO ] [main] [NettyService.java:59] - Starting netty service  on port 50553
[2015-08-20 12:51:25,309] [INFO ] [main] [SocketService.java:34] - Starting socket service  on socketPort 50554
[2015-08-20 12:51:25,310] [INFO ] [main] [AppServer.java:79] - Startup completed in 1629 ms.
```

**template**

```text
log4j.rootLogger=INFO,rollingfile
log4j.addivity.org.apache=true

log4j.logger.com.tplink.cloud=debug

log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=[%d] [%-5p] [%t] [%F:%L] - %m%n

log4j.appender.rollingfile=org.apache.log4j.RollingFileAppender
log4j.appender.rollingfile.file=logs/cloud.log
log4j.appender.rollingfile.append=true
log4j.appender.rollingfile.maxBackupIndex=20
log4j.appender.rollingfile.maxFileSize=20MB
#log4j.appender.rollingfile.bufferedIO=true
#log4j.appender.rollingfile.bufferSize=8192
log4j.appender.rollingfile.layout=org.apache.log4j.PatternLayout
log4j.appender.rollingfile.layout.ConversionPattern=[%d] [%-5p] [%t] [%F:%L] - %m%n
```

cloud-pushservice 接收到dispatcher的invoke处：Analysis.recv.dispatcher:$req, size:$,... 返回消息的入口处：Analysis.send. dispatcher:$rsp,...

给dispatcher发送消息：Analysis.send. dispatcher:$rep,... 收到dispatcher返回的消息：Analysis.recv. dispatcher:$rsp,..

> 使配置文件生效方法： `java -Dlog4j.configuration=/path/to/file`

