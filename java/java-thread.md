# Java Thread

### ThreadPoolExecutor

java的线程池，减少线程的创建和切换代价，复用

1. 降低资源消耗：通过重用已经创建的线程来降低线程创建和销毁的消耗
2. 提高响应速度：任务到达时不需要等待线程创建就可以立即执行。
3. 提高线程的可管理性：线程池可以统一管理、分配、调优和监控。

线程池的 `runState` 表明其生命周期：

```java
    /**
     * The runState provides the main lifecyle control, taking on values:
     *
     *   RUNNING:  Accept new tasks and process queued tasks
     *   SHUTDOWN: Don't accept new tasks, but process queued tasks
     *   STOP:     Don't accept new tasks, don't process queued tasks,
     *             and interrupt in-progress tasks
     *   TIDYING:  All tasks have terminated, workerCount is zero,
     *             the thread transitioning to state TIDYING
     *             will run the terminated() hook method
     *   TERMINATED: terminated() has completed
     *
     * The numerical order among these values matters, to allow
     * ordered comparisons. The runState monotonically increases over
     * time, but need not hit each state. The transitions are:
     *
     * RUNNING -> SHUTDOWN
     *    On invocation of shutdown(), perhaps implicitly in finalize()
     * (RUNNING or SHUTDOWN) -> STOP
     *    On invocation of shutdownNow()
     * SHUTDOWN -> TIDYING
     *    When both queue and pool are empty
     * STOP -> TIDYING
     *    When pool is empty
     * TIDYING -> TERMINATED
     *    When the terminated() hook method has completed
     */


    // runState is stored in the high-order bits
    private static final int RUNNING    = -1 << COUNT_BITS;
    private static final int SHUTDOWN   =  0 << COUNT_BITS;
    private static final int STOP       =  1 << COUNT_BITS;
    private static final int TIDYING    =  2 << COUNT_BITS;
    private static final int TERMINATED =  3 << COUNT_BITS;
```

### ExecutorService

The following method shuts down an ExecutorService in two phases, first by calling shutdown to reject incoming tasks, and then calling shutdownNow, if necessary, to cancel any lingering tasks:

```java
   void shutdownAndAwaitTermination(ExecutorService pool) {
     pool.shutdown(); // Disable new tasks from being submitted
     try {
       // Wait a while for existing tasks to terminate
       if (!pool.awaitTermination(60, TimeUnit.SECONDS)) {
         pool.shutdownNow(); // Cancel currently executing tasks
         // Wait a while for tasks to respond to being cancelled
         if (!pool.awaitTermination(60, TimeUnit.SECONDS))
             System.err.println("Pool did not terminate");
       }
     } catch (InterruptedException ie) {
       // (Re-)Cancel if current thread also interrupted
       pool.shutdownNow();
       // Preserve interrupt status
       Thread.currentThread().interrupt();
     }
   }
```

