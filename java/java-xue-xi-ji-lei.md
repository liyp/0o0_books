# Java 学习积累

1. Java HashMap 实现
2. 锁机制
3. j.u.c
4. jmm
5. CAS, AQS
6. 双亲继承机制
7. 类加载，类卸载
8. Netty
9. Java NIO

### reference

* [http://cmsblogs.com](http://cmsblogs.com)  的 《死磕Java》 系列
* 《[The\_art\_of\_multiprocessor\_programming](https://www.e-reading.club/bookreader.php/134637/Herlihy,_Shavit_-_The_art_of_multiprocessor_programming.pdf)》
  * [http://www.cs.tau.ac.il/~multi/errata.pdf](http://www.cs.tau.ac.il/~multi/errata.pdf)
* [http://tutorials.jenkov.com/java-concurrency/](http://tutorials.jenkov.com/java-concurrency/)
* [http://winterbe.com/posts/2015/04/30/java8-concurrency-tutorial-synchronized-locks-examples/](http://winterbe.com/posts/2015/04/30/java8-concurrency-tutorial-synchronized-locks-examples/)

### java 基础

java基础的数据结构，常用的 api 中的实现 hashmap concurrent io nio jvm gc classloader

#### java collections

**hashmap**

* HashTable, synchronized
* HashMap, not synchronized, Fail-fast iterators
* ConcurrentHashMap [\_infoQ](http://www.infoq.com/cn/articles/ConcurrentHashMap/) **jdk7**
  * Segment\[\] HashEntry\[\]
  * ReentrantLock
  * hash \(variant of single-word Wang/Jenkins hash\)
  * \(int initialCapacity, float loadFactor, int concurrencyLevel\)
  * get\(\) 不加锁，volatile，jmm happen before，HashEntry getObjectVolatile volatile-read
  * put\(\) 加锁，判断扩容，插入，HashEntry putOrderedObject volatile-write
  * size\(\) 尝试3次 RETRIES\_BEFORE\_LOCK 如果累加count 变化再加锁统计，overflow则 Integer.MAX\_VALUE
  * Retrievals reflect the results of the most recently completed update operations holding upon their onset. [ConcurrentHashMap 弱一致性](https://my.oschina.net/hosee/blog/675423)
* LinkedHashMap
  * \(int initialCapacity, float loadFactor, boolean accessOrder\)
  * 实现LRU 重写  加 ReentrantLock safe thread

    ```text
    private static final int MAX_ENTRIES = 100;
    protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
      return size() > MAX_ENTRIES;
    }
    ```

  * get\(\), modCount++, e.recordAccess\(this\);
* ConcurrentHashMap JDK8 [\_importnew](http://www.importnew.com/26049.html) **TODO**
  * synchronized+CAS+Node+红黑树

对比 jdk8 和 jdk7 的ConcurrentHashMa 实现：

> 其实可以看出JDK1.8版本的ConcurrentHashMap的数据结构已经接近HashMap，相对而言，ConcurrentHashMap只是增加了同步的操作来控制并发，从JDK1.7版本的ReentrantLock+Segment+HashEntry，到JDK1.8版本中synchronized+CAS+HashEntry+红黑树,相对而言，总结如下思考：
>
> 1. JDK1.8的实现降低锁的粒度，JDK1.7版本锁的粒度是基于Segment的，包含多个HashEntry，而JDK1.8锁的粒度就是HashEntry（首节点）
> 2. JDK1.8版本的数据结构变得更加简单，使得操作也更加清晰流畅，因为已经使用synchronized来进行同步，所以不需要分段锁的概念，也就不需要Segment这种数据结构了，由于粒度的降低，实现的复杂度也增加了
>
>    JDK1.8使用红黑树来优化链表，基于长度很长的链表的遍历是一个很漫长的过程，而红黑树的遍历效率是很快的，代替一定阈值的链表，这样形成一个最佳拍档
>
> 3. JDK1.8为什么使用内置锁synchronized来代替重入锁ReentrantLock，我觉得有以下几点：
>    * 因为粒度降低了，在相对而言的低粒度加锁方式，synchronized并不比ReentrantLock差，在粗粒度加锁中ReentrantLock可能通过Condition来控制各个低粒度的边界，更加的灵活，而在低粒度中，Condition的优势就没有了
>    * JVM的开发团队从来都没有放弃synchronized，而且基于JVM的synchronized优化空间更大，使用内嵌的关键字比使用API更加自然
>    * 在大量的数据操作下，对于JVM的内存压力，基于API的ReentrantLock会开销更多的内存，虽然不是瓶颈，但是也是一个选择依据

#### Thread

* sleep 不会释放对象锁
* wait 会释放对象锁， 一般和 synchronized 使用保证获取到 锁
* [Java Thread Life Cycle and Thread States](http://howtodoinjava.com/core-java/multi-threading/java-thread-life-cycle-and-thread-states/)

#### j.u.c lock & synchronized & volatile & CAS

* [自旋锁、排队自旋锁、MCS锁、CLH锁](https://coderbee.net/index.php/concurrent/20131115/577)
* synchronized 对 method 和 block 是不一样的实现方式，一个是 方法访问标志 ACC\_SYNCHRONZIED 一个是通过 monitorenter/monitorexit
* synchronized is reentrant, ,与 spin lock 的区别？ **TODO**
  * enforcing exclusive access to an object's state 
  * establishing happens-before relationships that are essential to visibility.

      When a thread releases an intrinsic lock, a happens-before relationship is established between that action and any subsequent acquisition of the same lock.
* synchronized, thread spin lock [--from](http://blog.takipi.com/5-things-you-didnt-know-about-synchronization-in-java-and-scala/)

> Synchronization is built around an internal entity known as the intrinsic lock or monitor lock. \(The API specification often refers to this entity simply as a "monitor."\) Intrinsic locks play a role in both aspects of synchronization: enforcing exclusive access to an object's state and establishing happens-before relationships that are essential to visibility. --FROM [\_url](https://docs.oracle.com/javase/tutorial/essential/concurrency/locksync.html)

* volatile 内存可见性，指令重排序，happens-before, mem barrier
* Writes and reads of volatile long and double values are always atomic.
* happens-before [-ifeve](http://ifeve.com/easy-happens-before/) 定义：JDK5\(JSR-133\)提供的概念，用于描述多线程操作之间的内存可见性。如果一个操作执行的结果需要对另一个操作可见，那么这两个操作之间必须存在happens-before关系。

作用：描述多线程操作之间的内存可见性。

```text
     [程序顺序规则]：一个线程中的每个操作，happens- before 于该线程中的任意后续操作。

     [监视器锁规则]：对一个监视器锁的解锁，happens- before 于随后对这个监视器锁的加锁。

     [volatile变量规则]：对一个volatile域的写，happens- before 于任意后续对这个volatile域的读。

     [传递性]：如果A happens- before B，且B happens- before C，那么A happens- before C。
```

* CAS 的 ABA 问题，携带 版本信息， AtomicStampedReference/AtomicMarkableReference
* spin lock, CLH vs MCS , 比较cpu架构区别 SMP、NUMA、MPP体系
  * 对称多处理器结构 \(SMP ： Symmetric Multi-Processor\)  使用 CLH
  * 非一致存储访问结构 \(NUMA ： Non-Uniform Memory Access\) 使用 MCS
  * 海量并行处理结构 \(MPP ： Massive Parallel Processing\)
* JSR-133 JAVA Memory Model
  * [JSR133中文版1.pdf](http://ifeve.com/wp-content/uploads/2014/03/JSR133中文版1.pdf)
  * [JMM](http://docs.oracle.com/javase/specs/jls/se8/html/jls-17.html#jls-17.4)
  * [深入理解Java内存模型](http://ifeve.com/java-memory-model-1/)
* final 重排序
  * 写重排序规则禁止把 final 域的写重排序到构造函数之外；编译器会在final 域写后，构造函数return 前，插入 StoreStore barrier
  * 初次读对象引用与初次读该对象包含的 final域，JMM 禁止**处理器**重排序，会在读final域前插入 LoadLoad barrier
* java monitor object
* synchronized 的优化，锁的四种状态： **重要**
  * 无锁状态，
  * 偏向锁，
  * 轻量级锁，
  * 重量级锁，使用系统底层的 Mutex Lock
  * [Java 并发编程：核心理论](http://www.cnblogs.com/paddix/p/5374810.html)
  * 优化：自适应自旋\(adaptive spinning\)，锁粗化\(lock coarsening\)，锁消除\(lock elimination\)

> Condition，Condition 将 Object 监视器方法（wait、notify 和 notifyAll）分解成截然不同的对象，以便通过将这些对象与任意 Lock 实现组合使用，为每个对象提供多个等待 set （wait-set）。其中，Lock 替代了 synchronized 方法和语句的使用，Condition 替代了 Object 监视器方法的使用。

#### StringBuilder vs StringBuffer

char\[\]

StringBuilder , non synchronization

### JVM

* [https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-2.html](https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-2.html)
* [https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html\#jvms-2.5](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html#jvms-2.5)

Runtime Data Areas

* 方法区
* 堆
* 虚拟机栈
* 本地方法栈
* 程序计数器

#### garbage collecter

* 引用计数
* 可达性分析
* Mark-Sweep
* Copying
* Mark-Compact
* Generational Collection

可选为 GC roots 的对象：

* 虚拟机栈（栈帧中的本地变量表）引用的对象
* 方法区中类静态属性引用对象
* 方法去常量引用对象
* 本地方法栈中JNI引用对象

垃圾回收器：

* Serial GC -XX:+UseSerialGC
* Parallel GC -XX:+UseParallelGC
* Parallel Old GC -XX:+UseParallelOldGC
* CMS -XX:+UseConcMarkSweepGC
* G1 -XX:+UseG1GC

### sql

* [https://tech.meituan.com/innodb-lock.html](https://tech.meituan.com/innodb-lock.html)

事务的四种隔离级别：

* 未提交读\(Read Uncommitted\)：允许脏读，也就是可能读取到其他会话中未提交事务修改的数据
* 提交读\(Read Committed\)：只能读取到已经提交的数据。Oracle等多数数据库默认都是该级别 \(不重复读\)
* 可重复读\(Repeated Read\)：可重复读。在同一个事务内的查询都是事务开始时刻一致的，InnoDB默认级别。在SQL标准中，该隔离级别消除了不可重复读，但是还存在幻象读
* 串行读\(Serializable\)：完全串行化的读，每次读都需要获得表级共享锁，读写相互都会阻塞

索引优化和实现 B+tree

