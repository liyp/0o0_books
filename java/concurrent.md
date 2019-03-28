# JAVA-concurrent

java并发知识，学习多线程相关的容器、技术原理。

## ConcurrentHashMap Vs HashMap

* HashMap
  * 线程不安全的，一个线程更改map，另一个线程遍历，就可能会出现

    `java.util.ConcurrentModificationException`

    （在每个线程内部执行了`map.put(key,value)`的操作，因此在高并发情况下会可能会抛出异常（出现概率低）

    如果此时在线程中执行`map.keySet()`操作，遍历map中的数据，那么出现异常的概率就很高了）
* ConcurrentHashMap 线程安全的，对于map的操作。

> 如果使用HashMap，我们已经清楚不是线程安全的了，那么可能会出现什么问题呢？ 1. put 的数据丢失。 2. remove 的数据未被清除，仍然存在。 3. HashMap resize 导致存在性能问题。 4. get 数据时出现死循环。
>
> 解决方案：推荐使用ConcurrentHashMap。

除此之外，还有性能比较问题，由于ConcurrentHashMap的锁的细粒度。

## ConcurrentHashMap原理分析

![&#x539F;&#x7406;&#x56FE;](http://p.blog.csdn.net/images/p_blog_csdn_net/liuzhengkang/EntryImages/20080912/58adc9e7b4725349c149a.jpg)

## Executor

> * [并发框架Executor](http://willsunforjava.iteye.com/blog/1631353)

Executors类，提供了一系列工厂方法用于创先线程池，返回的线程池都实现了ExecutorService接口。

* `public static ExecutorService newFixedThreadPool(int nThreads)`

  创建固定数目线程的线程池。

* `public static ExecutorService newCachedThreadPool()`

  创建一个可缓存的线程池，调用`execute()`将重用以前构造的线程（如果线程可用）。如果现有线程没有可用的，则创建一个新线程并添加到池中。终止并从缓存中移除那些已有 60 秒钟未被使用的线程。

* `public static ExecutorService newSingleThreadExecutor()`

  创建一个单线程化的Executor。

* `public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize)`

  创建一个支持定时及周期性的任务执行的线程池，多数情况下可用来替代Timer类。

