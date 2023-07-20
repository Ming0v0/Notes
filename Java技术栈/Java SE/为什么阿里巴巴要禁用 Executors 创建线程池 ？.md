[原文](https://blog.csdn.net/dabusiGin/article/details/105327873/)
## Executor是不建议的
Executors类为我们提供了各种类型的线程池，经常使用的工厂方法有：
```java
public static ExecutorService newSingleThreadExecutor()
public static ExecutorService newFixedThreadPool(int nThreads)
public static ExecutorService newCachedThreadPool()
public static ScheduledExecutorService newSingleThreadScheduledExecutor()
public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize)
```

书写一段很简单的测试代码：
```java
public class ThreadDemo {
	public static void main(String[] args) {
		ExecutorService es = Executors.newSingleThreadExecutor(); 
	}
}
```

当我们用阿里巴巴的P3C检查代码时，会被教育的！！！！
![图片](https://mmbiz.qpic.cn/mmbiz_png/A0bYOQcma0PGgLpviaZ3JSMwUWoVZoYKBhmwf8yibojdFDB9Zmh2PTABwTtf15VFIH1m5s2oSxMvpKKqiaibhEsVbw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

阿里巴巴是不允许这么创建线程池的，上面的警告写的很明确“线程池不允许使用Executors去创建，而是通过ThreadPoolExecutor的方式，这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险。

## 强制使用ThreadPoolExecutor
我们使用ThreadPoolExecutor创建线程池：
```java
public class ThreadDemo {  
 public static void main(String[] args) {  
  ExecutorService es = new ThreadPoolExecutor(1, 1, 0L, TimeUnit.MILLISECONDS,  
    new LinkedBlockingQueue<Runnable>(10), Executors.defaultThreadFactory(),  
    new ThreadPoolExecutor.DiscardPolicy());  
 }  
}
```

此时，再用P3C检查代码，终于没有报错了。
在华丽的分隔符之后，我们还是有必要从JDK源码的层面深挖一下其中的原理。
首先是静态方法`newSingleThreadExecutor()`、`newFixedThreadPool(int nThreads)`、`newCachedThreadPool()`。我们来看一下其源码实现（基于JDK8）。
```java
public static ExecutorService newSingleThreadExecutor() {  
        return new FinalizableDelegatedExecutorService  
            (new ThreadPoolExecutor(1, 1,  
                                    0L, TimeUnit.MILLISECONDS,  
                                    new LinkedBlockingQueue<Runnable>()));  
}  
  
public static ExecutorService newFixedThreadPool(int nThreads) {  
        return new ThreadPoolExecutor(nThreads, nThreads,  
                                      0L, TimeUnit.MILLISECONDS,  
                                      new LinkedBlockingQueue<Runnable>());  
}  
  
public static ExecutorService newCachedThreadPool() {  
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,  
                                      60L, TimeUnit.SECONDS,  
                                      new SynchronousQueue<Runnable>());  
}
```
通过查看源码我们知道上述三种静态方法的内部实现均使用了`ThreadPoolExecutor`类。难怪阿里爸爸会建议通过`ThreadPoolExecutor`的方式实现，原来Executors类的静态方法也是用的它，只不过帮我们配了一些参数而已。

第二是`ThreadPoolExecutor`类的构造方法。既然现在要直接使用`ThreadPoolExecutor`类了，那么其中的初始化参数就要我们自己配了，了解其构造方法势在必行。

`ThreadPoolExecutor`类一共有四个构造方法，我们只需要了解之中的一个就可以了，因为其他三种构造方法只是帮我们配置了一些默认参数，最后还是调用了它。
```java
public ThreadPoolExecutor(int corePoolSize,  
            int maximumPoolSize,  
            long keepAliveTime,  
            TimeUnit unit,  
            BlockingQueue<Runnable> workQueue,  
            ThreadFactory threadFactory,  
            RejectedExecutionHandler handler)
```

其中的参数含义是：
-   `corePoolSize`：线程池中的线程数量；
-   `maximumPoolSize`：线程池中的最大线程数量；
-   `keepAliveTime`：当线程池线程数量超过corePoolSize时，多余的空闲线程会在多长时间内被销毁；
-   `unit`：keepAliveTime的时间单位；
-   `workQueue`：任务队列，被提交但是尚未被执行的任务；
-   `threadFactory`：线程工厂，用于创建线程，一般情况下使用默认的，即Executors类的静态方法defaultThreadFactory()；`handler`：拒绝策略。当任务太多来不及处理时，如何拒绝任务。

对于这些参数要有以下了解：

**corePoolSize与maximumPoolSize的关系**
首先corePoolSize肯定是 <= maximumPoolSize。
其他关系如下：
-   若当前线程池中线程数 < corePoolSize，则每来一个任务就创建一个线程去执行；
-   若当前线程池中线程数 >= corePoolSize，会尝试将任务添加到任务队列。如果添加成功，则任务会等待空闲线程将其取出并执行；
-   若队列已满，且当前线程池中线程数 < maximumPoolSize，创建新的线程；
-   若当前线程池中线程数 >= maximumPoolSize，则会采用拒绝策略（JDK提供了四种，下面会介绍到）。
> 注意：关系3是针对的有界队列，无界队列永远都不会满，所以只有前2种关系。

**workQueue**
参数workQueue是指提交但未执行的任务队列。若当前线程池中线程数>=corePoolSize时，就会尝试将任务添加到任务队列中。主要有以下几种：
-   `SynchronousQueue`：直接提交队列。SynchronousQueue没有容量，所以实际上提交的任务不会被添加到任务队列，总是将新任务提交给线程执行，如果没有空闲的线程，则尝试创建新的线程，如果线程数量已经达到最大值（maximumPoolSize），则执行拒绝策略。
-   `LinkedBlockingQueue`：无界的任务队列。当有新的任务来到时，若系统的线程数小于corePoolSize，线程池会创建新的线程执行任务；当系统的线程数量等于corePoolSize后，因为是无界的任务队列，总是能成功将任务添加到任务队列中，所以线程数量不再增加。若任务创建的速度远大于任务处理的速度，无界队列会快速增长，直到内存耗尽。

**handler**
JDK内置了四种拒绝策略：
-   `DiscardOldestPolicy策略`：丢弃任务队列中最早添加的任务，并尝试提交当前任务；
-   `CallerRunsPolicy策略`：调用主线程执行被拒绝的任务，这提供了一种简单的反馈控制机制，将降低新任务的提交速度。
-   `DiscardPolicy策略`：默默丢弃无法处理的任务，不予任何处理。
-   `AbortPolicy策略`：直接抛出异常，阻止系统正常工作。