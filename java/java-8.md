# JAVA 8

java 9 都要来了，我们却还对 java8 一无所知。

> \[JAVA 9 release roadmap\] JDK 9 Schedule
>
> 2016/05/26 Feature Complete
>
> 2016/12/22 Feature Extension Complete
>
> 2017/01/05 Rampdown Start
>
> 2017/02/09 All Tests Run
>
> 2017/02/16 Zero Bug Bounce
>
> 2017/03/16 Rampdown Phase Two
>
> 2017/06/22 Initial Release Candidate
>
> 2017/07/06 Final Release Candidate
>
> 2017/07/27 General Availability

Oracle 在 2014 年 3 月发布了 Java 8 正式版，该版本是一个有重大改变的版本，对 JAVA 带来了诸多新特性。其中主要的新特性涵盖：函数式接口、Lambda 表达式、集合的流式操作、注解的更新、安全性的增强、IO\NIO 的改进、完善的全球化功能等。

最近的一次 [java 调查问卷](http://www.baeldung.com/java-in-2017) 中，java 8 采用率已经占有 74.7%（2017年4月），java 7 约占 19.9% 。

### why java8

* java8 目前是唯一 Oracle 支持的 JAVA 发行版， JDK7 在 2015 年 4 月后不再被提供更新\(EoPU\)。
* 在很多情况下，Java8 都能提升应用性能，而无需任何改变或性能调优。
* Lambda 表达式、 Streams API 以及现有类的新方法都是提高生产力的重要工具。
* Java8 新推出的 Optional 类型在处理 null 值时，能减少 NullPointerExceptions 的可能性，给开发者极大的灵活度。

#### new features

* java language
  * lambda expression
  * Method references
  * Interfaces now allow default methods and static methods
  * Repeating Annotations
  * Type Annotations anywhere
  * Improved type inference
  * Method parameter reflection
* library api
  * Optional    
  * stream api
  * date api \(JSR310\)
  * base64 api
  * collection parallel
  * j.u.c enhancements
  * io/nio
* removal of PermGen, add Metaspace \(JEP 122\)

ref:

* [What's New in JDK 8](http://www.oracle.com/technetwork/java/javase/8-whats-new-2157071.html)
* [http://ifeve.com/java-8-features-tutorial](http://ifeve.com/java-8-features-tutorial)

#### perform better

* Performance Improvement for HashMaps with Key Collisions
* Performance improvement for the `java.lang.String(byte[], *)` constructor and the `java.lang.String.getBytes()` method. 

ref:

* [Learn More About Performance Improvements in JDK 8](https://blogs.oracle.com/thejavatutorials/learn-more-about-performance-improvements-in-jdk-8)

#### backward compatibility

* Source Compatibility. 大部分情况下，几乎不需要对代码进行改动，即可完成 java se8 上的构建任务。
* Binary Compatibility. java se8 是对 java se7 二进制兼容的，基于java se7 编译构建的jar 完全可以正确运行在 java se8 上的。 

除了个别情况，比如有依赖 `ASM` `GCLIB` 等字节码层操作库的情况，可能需要做些处理。

* [Compatibility Guide for JDK 8](http://www.oracle.com/technetwork/java/javase/8-compatibility-guide-2156366.html)

#### migration

关于迁移到 JAVA8，建议分阶段进行：

1. 不兼容问题确认。
   * 从构建系统入手，测试现有工程代码是否都是成功在 JDK8 上完成构建，对于出现的问题进行记录 issue 跟踪，步步解决。
   * 通过构建系统，可以将大部分模块大部分问题进行解决，根据线上运行模块列表，一一确保解决 java8 迁移兼容性问题。
2. 环境准备。需要在所有环境都安装 JDK8。不需要添加到 PATH，环境中 JAVA 已经使用 JDK7
3. java7 8 共存阶段：
   * 此阶段，java7 还是作为默认java环境。对于要使用 java8 的开发者，我们准备了一个环境启用脚本，可以在 `start.sh`脚本中调用启用java8环境。
   * 分批独立上线。可以独立运行并有计划更新的模块，可以独立完成迁移工作并上线。 （个人驱动，想体验java8的同志们，自己多干点活，把上下流程走通）
4. 整体迁移 java8 阶段：
   * 对应用模块的 java8 迁移
   * 对基础服务的java8迁移，cassandra

ref:

* [http://allegro.tech/2014/12/How-to-migrate-to-Java-8.html](http://allegro.tech/2014/12/How-to-migrate-to-Java-8.html)
* [http://product.hubspot.com/blog/upgrading-to-java-8-at-scale](http://product.hubspot.com/blog/upgrading-to-java-8-at-scale)
* [http://engineering.indeedblog.com/blog/2016/09/job-search-web-app-java-8-migration/](http://engineering.indeedblog.com/blog/2016/09/job-search-web-app-java-8-migration/)

### Java feature

#### Metaspace

Metaspace \(-XX:MaxMetaspaceSize\) 元数据空间使用 native heap PermGen \(-XX:MaxPermSize\) 使用的 jvm 内存

### Reference

* [Why should I upgrade Java?](https://www.java.com/en/download/faq/why_upgrade.xml)
* [Why Java 8?](https://dzone.com/articles/why-java-8-1)
* [Why You Need to Upgrade to Java 8 Already](https://www.toptal.com/java/why-you-need-to-upgrade-to-java-8-already)
* [Upgrading major Java versions](https://blogs.oracle.com/java-platform-group/upgrading-major-java-versions)

