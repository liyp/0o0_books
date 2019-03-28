# Zookeeper 学习

Zookeeper 从设计模式角度来看，是一个基于观察者模式设计的分布式服务管理框架，它负责存储和管理大家都关心的数据，然后接受观察者的注册，一旦这些数据的状态发生变化，Zookeeper 就将负责通知已经在 Zookeeper 上注册的那些观察者做出相应的反应，从而实现集群中类似 Master/Slave 管理模式。

* 统一命名服务（Name Service）
* 配置管理（Configuration Management）
* 集群管理（Group Membership）
* 共享锁（Locks）
* 队列管理

ref:

* [Zookeeper apache doc](http://zookeeper.apache.org/doc/current/zookeeperOver.html)
* [Zookeeper 初体验之——JAVA API 初探](http://www.cnblogs.com/haippy/archive/2012/07/19/2600032.html)

## zk watch

> [ZooKeeper Watches](http://zookeeper.apache.org/doc/current/zookeeperProgrammers.html#ch_zkWatches) All of the read operations in ZooKeeper - getData\(\), getChildren\(\), and exists\(\) - have the option of setting a watch as a side effect. Here is ZooKeeper's definition of a watch: a watch event is one-time trigger, sent to the client that set the watch, which occurs when the data for which the watch was set changes. There are three key points to consider in this definition of a watch:
>
> * One-time trigger
>
>   One watch event will be sent to the client when the data has changed. For example, if a client does a getData\("/znode1", true\) and later the data for /znode1 is changed or deleted, the client will get a watch event for /znode1. If /znode1 changes again, no watch event will be sent unless the client has done another read that sets a new watch.
>
> * Sent to the client
>
>   This implies that an event is on the way to the client, but may not reach the client before the successful return code to the change operation reaches the client that initiated the change. Watches are sent asynchronously to watchers. ZooKeeper provides an ordering guarantee: a client will never see a change for which it has set a watch until it first sees the watch event. Network delays or other factors may cause different clients to see watches and return codes from updates at different times. The key point is that everything seen by the different clients will have a consistent order.
>
> * The data for which the watch was set
>
>   This refers to the different ways a node can change. It helps to think of ZooKeeper as maintaining two lists of watches: data watches and child watches. getData\(\) and exists\(\) set data watches. getChildren\(\) sets child watches. Alternatively, it may help to think of watches being set according to the kind of data returned. getData\(\) and exists\(\) return information about the data of the node, whereas getChildren\(\) returns a list of children. Thus, setData\(\) will trigger data watches for the znode being set \(assuming the set is successful\). A successful create\(\) will trigger a data watch for the znode being created and a child watch for the parent znode. A successful delete\(\) will trigger both a data watch and a child watch \(since there can be no more children\) for a znode being deleted as well as a child watch for the parent znode.
>
> Watches are maintained locally at the ZooKeeper server to which the client is connected. This allows watches to be light weight to set, maintain, and dispatch. When a client connects to a new server, the watch will be triggered for any session events. Watches will not be received while disconnected from a server. When a client reconnects, any previously registered watches will be reregistered and triggered if needed. In general this all occurs transparently. There is one case where a watch may be missed: a watch for the existance of a znode not yet created will be missed if the znode is created and deleted while disconnected.
>
> What ZooKeeper Guarantees about Watches With regard to watches, ZooKeeper maintains these guarantees:
>
> Watches are ordered with respect to other events, other watches, and asynchronous replies. The ZooKeeper client libraries ensures that everything is dispatched in order. A client will see a watch event for a znode it is watching before seeing the new data that corresponds to that znode. The order of watch events from ZooKeeper corresponds to the order of the updates as seen by the ZooKeeper service. Things to Remember about Watches Watches are one time triggers; if you get a watch event and you want to get notified of future changes, you must set another watch. Because watches are one time triggers and there is latency between getting the event and sending a new request to get a watch you cannot reliably see every change that happens to a node in ZooKeeper. Be prepared to handle the case where the znode changes multiple times between getting the event and setting the watch again. \(You may not care, but at least realize it may happen.\) A watch object, or function/context pair, will only be triggered once for a given notification. For example, if the same watch object is registered for an exists and a getData call for the same file and that file is then deleted, the watch object would only be invoked once with the deletion notification for the file. When you disconnect from a server \(for example, when the server fails\), you will not get any watches until the connection is reestablished. For this reason session events are sent to all outstanding watch handlers. Use session events to go into a safe mode: you will not be receiving events while disconnected, so your process should act conservatively in that mode.

## Q&A

线上遇到的问题。

Exception while start cloud-pushservice: org.apache.zookeeper.KeeperException$ConnectionLossException: KeeperErrorCode = ConnectionLoss for /PUSH/ELECTION org.apache.zookeeper.KeeperException$ConnectionLossException: KeeperErrorCode = ConnectionLoss for /PUSH/ELECTION at org.apache.zookeeper.KeeperException.create\(KeeperException.java:90\) at org.apache.zookeeper.KeeperException.create\(KeeperException.java:42\) at org.apache.zookeeper.ZooKeeper.exists\(ZooKeeper.java:809\) at org.apache.zookeeper.ZooKeeper.exists\(ZooKeeper.java:837\) at com.tplink.cloud.push.task.ZKManager.leaderElection\(ZKManager.java:102\) at com.tplink.cloud.push.ServerStarter.startServer\(ServerStarter.java:43\) at com.tplink.cloud.push.ServerStarter.main\(ServerStarter.java:62\)

