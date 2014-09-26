JAVA-concurrent
===========
java并发知识，学习多线程相关的容器、技术原理。

## ConcurrentHashMap Vs HashMap

* HashMap
  - 线程不安全的，一个线程更改map，另一个线程遍历，就可能会出现
    `java.util.ConcurrentModificationException`
    （在每个线程内部执行了`map.put(key,value)`的操作，因此在高并发情况下会可能会抛出异常（出现概率低）
    如果此时在线程中执行`map.keySet()`操作，遍历map中的数据，那么出现异常的概率就很高了）

* ConcurrentHashMap  线程安全的，对于map的操作。


> 如果使用HashMap，我们已经清楚不是线程安全的了，那么可能会出现什么问题呢？
> 1. put 的数据丢失。
> 2. remove 的数据未被清除，仍然存在。
> 3. HashMap resize 导致存在性能问题。
> 4. get 数据时出现死循环。

> 解决方案：推荐使用ConcurrentHashMap。

[hashmapVs]: http://blog.csdn.net/ilaotu/article/details/18285289

除此之外，还有性能比较问题，由于ConcurrentHashMap的锁的细粒度。
[ibm-HashMap]: http://www.ibm.com/developerworks/java/library/j-jtp08223/


[map]: http://blog.sina.com.cn/s/blog_7227719a0100lpj0.html "从ConcurrentHashMap 安全Map说起"


## ConcurrentHashMap原理分析

![原理图](http://p.blog.csdn.net/images/p_blog_csdn_net/liuzhengkang/EntryImages/20080912/58adc9e7b4725349c149a.jpg)
[ConcurrentHashMap]: http://blog.csdn.net/liuzhengkang/article/details/2916620 "ConcurrentHashMap原理分析"
