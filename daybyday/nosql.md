NoSQL
=====

* [NoSQL Notes](http://www.nosqlnotes.net)

## NoSQL benchmark

[bankmark-nosql-benchmark](http://www.bankmark.de/wp-content/uploads/2014/12/bankmark-20141201-WP-NoSQLBenchmark.pdf)
[mongodb-benchmark](http://www.mongodb.com/blog/post/high-performance-benchmarking-mongodb-and-nosql-systems)

## MongoDB

存储引擎

* 支持集合级锁的存储引擎MMAP
* 支持压缩和文档级锁的存储引擎WiredTiger   ver>=2.8


## Cassandra

### port

[cassandra-port-usage-how-are-the-ports-used](http://stackoverflow.com/questions/2359159/cassandra-port-usage-how-are-the-ports-used)

- 7199 - JMX (was 8080 pre Cassandra 0.8.xx)
- 7000 - Internode communication (not used if TLS enabled)
- 7001 - TLS Internode communication (used if TLS enabled)
- 9160 - Thrift client API
- 9042 - CQL native transport port

### DataStruct

Merkle tree

### reference

Cassandra Blog

[Cassandra – 理解关键概念和数据模型](http://my.oschina.net/silentriver/blog/182678)
[Cassandra 数据存储结构与数据读写](http://www.oschina.net/question/12_11855)

Cassandra官方文档

[Cassandra21](http://docs.datastax.com/en/cassandra/2.1/cassandra/gettingStartedCassandraIntro.html)
[Cassandra21.pdf](http://docs.datastax.com/en/cassandra/2.1/pdf/cassandra21.pdf)

### ORM

[Kundera](https://github.com/impetus-opensource/Kundera) NoSQL ORM 框架

[spring-data-c*](http://docs.spring.io/spring-data/cassandra/docs/1.2.0.RELEASE/reference/html/#cassandra.core)
官方文档中给的例子是在`spring-data-c*-1.0.0`上，`ver > 1.2.0`提示`Exception`，暂未解决。
> Exception in thread "main" java.lang.NoClassDefFoundError: org/springframework/core/DefaultParameterNameDiscoverer  
> Caused by: java.lang.ClassNotFoundException: org.springframework.core.DefaultParameterNameDiscoverer

## Q&A

**xxx to cassandra migration**

UUID 问题

Snowflake : a network service for generating unique ID numbers at high scale with some simple guarantees

Twitter在把存储系统从MySQL迁移到Cassandra的过程中由于Cassandra没有顺序ID生成机制，于是自己开发了一套全局唯一ID生成服务：Snowflake。GitHub地址：https://github.com/twitter/snowflake/tree/snowflake-2010。根据twitter的业务需求，snowflake系统生成64位的ID。由3部分组成：

 - 41位的时间序列（精确到毫秒，41位的长度可以使用69年）
 - 10位的机器标识（10位的长度最多支持部署1024个节点）
 - 12位的计数顺序号（12位的计数顺序号支持每个节点每毫秒产生4096个ID序号）

 最高位是符号位，始终为0。

 优点：高性能，低延迟；独立的应用；按时间有序。
 缺点：需要独立的开发和部署。

> MySQL UUID方案 Flicker : 采用了MySQL自增长ID的机制（auto_increment + replace into + MyISAM）。

**datastax-java-driver**  

`ver = 2.1.5`

Object-mapping API 问题

使用cassandra java driver 时，取Bean 是使用Row 列位置映射的方式，动态添加列会使 Row 列位置错位，导致映射的 Bean 属性是错误的。

**other**
一致性问题
时间戳、版本号

### cassandra tombstone_failure_threshold

system.log 中出现如下异常。(执行大量delete操作后，100k->2k的程度)

```
WARN  [SharedPool-Worker-2] 2015-09-28 16:03:30,780 SliceQueryFilter.java:242 - Read 2968 live and 5934 tombstoned cells in cloud.push_app_task (see tombstone_warn_threshold). 5001 columns was requested, slices=[4_1433132231_0002232786FA_506_00eebd999772:time-]
ERROR [SharedPool-Worker-2] 2015-09-28 16:03:31,729 SliceQueryFilter.java:218 - Scanned over 100000 tombstones in cloud.push_app_task; query aborted (see tombstone_failure_threshold)
WARN  [SharedPool-Worker-2] 2015-09-28 16:03:31,736 AbstractTracingAwareExecutorService.java:169 - Uncaught exception on thread Thread[SharedPool-Worker-2,5,main]: {}
java.lang.RuntimeException: org.apache.cassandra.db.filter.TombstoneOverwhelmingException
        at org.apache.cassandra.service.StorageProxy$DroppableRunnable.run(StorageProxy.java:2182) ~[apache-cassandra-2.1.5.jar:2.1.5]
        at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471) ~[na:1.7.0_60]
        at org.apache.cassandra.concurrent.AbstractTracingAwareExecutorService$FutureTask.run(AbstractTracingAwareExecutorService.java:164) ~[apache-cassandra-2.1.5.jar:2.1.5]
        at org.apache.cassandra.concurrent.SEPWorker.run(SEPWorker.java:105) [apache-cassandra-2.1.5.jar:2.1.5]
        at java.lang.Thread.run(Thread.java:745) [na:1.7.0_60]
Caused by: org.apache.cassandra.db.filter.TombstoneOverwhelmingException: null
        at org.apache.cassandra.db.filter.SliceQueryFilter.collectReducedColumns(SliceQueryFilter.java:220) ~[apache-cassandra-2.1.5.jar:2.1.5]
        at org.apache.cassandra.db.filter.QueryFilter.collateColumns(QueryFilter.java:107) ~[apache-cassandra-2.1.5.jar:2.1.5]
        at org.apache.cassandra.db.filter.QueryFilter.collateOnDiskAtom(QueryFilter.java:81) ~[apache-cassandra-2.1.5.jar:2.1.5]
        at org.apache.cassandra.db.RowIteratorFactory$2.getReduced(RowIteratorFactory.java:99) ~[apache-cassandra-2.1.5.jar:2.1.5]
        at org.apache.cassandra.db.RowIteratorFactory$2.getReduced(RowIteratorFactory.java:71) ~[apache-cassandra-2.1.5.jar:2.1.5]
        at org.apache.cassandra.utils.MergeIterator$ManyToOne.consume(MergeIterator.java:117) ~[apache-cassandra-2.1.5.jar:2.1.5]
        at org.apache.cassandra.utils.MergeIterator$ManyToOne.computeNext(MergeIterator.java:100) ~[apache-cassandra-2.1.5.jar:2.1.5]
        at com.google.common.collect.AbstractIterator.tryToComputeNext(AbstractIterator.java:143) ~[guava-16.0.jar:na]
        at com.google.common.collect.AbstractIterator.hasNext(AbstractIterator.java:138) ~[guava-16.0.jar:na]
        at org.apache.cassandra.db.ColumnFamilyStore$8.computeNext(ColumnFamilyStore.java:2006) ~[apache-cassandra-2.1.5.jar:2.1.5]
        at org.apache.cassandra.db.ColumnFamilyStore$8.computeNext(ColumnFamilyStore.java:2002) ~[apache-cassandra-2.1.5.jar:2.1.5]
        at com.google.common.collect.AbstractIterator.tryToComputeNext(AbstractIterator.java:143) ~[guava-16.0.jar:na]
        at com.google.common.collect.AbstractIterator.hasNext(AbstractIterator.java:138) ~[guava-16.0.jar:na]
        at org.apache.cassandra.db.ColumnFamilyStore.filter(ColumnFamilyStore.java:2157) ~[apache-cassandra-2.1.5.jar:2.1.5]
        at org.apache.cassandra.db.ColumnFamilyStore.getRangeSlice(ColumnFamilyStore.java:2115) ~[apache-cassandra-2.1.5.jar:2.1.5]
        at org.apache.cassandra.db.PagedRangeCommand.executeLocally(PagedRangeCommand.java:115) ~[apache-cassandra-2.1.5.jar:2.1.5]
        at org.apache.cassandra.service.StorageProxy$LocalRangeSliceRunnable.runMayThrow(StorageProxy.java:1516) ~[apache-cassandra-2.1.5.jar:2.1.5]
        at org.apache.cassandra.service.StorageProxy$DroppableRunnable.run(StorageProxy.java:2179) ~[apache-cassandra-2.1.5.jar:2.1.5]
        ... 4 common frames omitted
```

这种问题，google出的一些参考
- http://stackoverflow.com/questions/21755286/what-exactly-happens-when-tombstone-limit-is-reached
