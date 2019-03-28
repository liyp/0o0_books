# Thread

## thread block

[Java里阻塞线程的三种实现方法](http://my.oschina.net/flashsword/blog/114527)

> JDK里提供了便利的ThreadPoolExecutor以及其包装的工具类Executors。但是我们知道ExecutorService.excute\(Runnable r\)是异步的，超过线程池处理能力的线程会被加入到执行队列里。有时候为了保证任务提交的顺序性，我们不希望有这个执行队列，在线程池满的时候，则把主线程阻塞。那么，怎么实现呢？

```java
private ReentrantLock pauseLock = new ReentrantLock();
    private Condition unpaused = pauseLock.newCondition();
    @Override
    public void execute(Runnable command) {
        pauseLock.lock();
        try {
            while (getPoolSize()==getMaximumPoolSize() && getQueue().remainingCapacity()==0)
                unpaused.await();
            super.execute(command);//放到lock外面的话，在压力测试下会有漏网的！
        } catch (InterruptedException e) {
            log.warn(this, e);
        } finally {
            pauseLock.unlock();
        }
    }
    @Override
    protected void afterExecute(Runnable r, Throwable t) {
        super.afterExecute(r,t);
        try{
            pauseLock.lock();
            unpaused.signal();
        }finally{
            pauseLock.unlock();
        }
    }
```

```java
// 与我们熟知的OO基本原则之一–里氏替换原则冲突了，即子类的方法与父类的方法有不同的行为
public static ExecutorService newFixedThreadPool(int threadSize) {
    return new ThreadPoolExecutor(threadSize, threadSize, 0L, TimeUnit.MILLISECONDS,
        new LinkedBlockingQueue<Runnable>(1) {
            private static final long serialVersionUID = -9028058603126367678L;
            @Override
            public boolean offer(Runnable e) {
                try {
                    put(e);
                    return true;
                } catch (InterruptedException ie) {
                    Thread.currentThread().interrupt();
                }
                return false;
            }
        });
    }
}
```

```java
// AbortPolicy(中止)、CallersRunPolicy(调用者运行)、DiscardPolicy(丢弃)、DiscardOldestPolicy(丢弃最旧的)
// 这样加上主线程，总是会比参数的size线程多上一个。要么在函数开始就把size-1，要么，我们可以尝试自己实现一个RejectedExecutionHandler:
public static ExecutorService newBlockingExecutorsUseCallerRun(int size) {
    return new ThreadPoolExecutor(size, size, 0L, TimeUnit.MILLISECONDS, new SynchronousQueue<Runnable>(),
            new ThreadPoolExecutor.CallerRunsPolicy());
}
```

```java
public static ExecutorService newBlockingExecutorsUseCallerRun(int size) {
    return new ThreadPoolExecutor(size, size, 0L, TimeUnit.MILLISECONDS, new SynchronousQueue<Runnable>(),
            new RejectedExecutionHandler() {

                @Override
                public void rejectedExecution(Runnable r, ThreadPoolExecutor executor) {
                    try {
                        executor.getQueue().put(r);
                    } catch (InterruptedException e) {
                        throw new RuntimeException(e);
                    }
                }
            });
}
```

> 除了JDK的CallerRunsPolicy方案，其他的方案都存在一个隐患：  
> 如果线程仍在执行，此时显式调用`ExecutorService.shutdown()`方法，会因为还有一个线程阻塞没有入队，而此时线程已经停止了，而这个元素才刚刚入队，最终会导致`RejectedExecutionException`。

