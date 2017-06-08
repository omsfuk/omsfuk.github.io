-----------------------
title: Java并发编程工具包
tags: java,并发
-----------------------
# JDK并发包
## 多线程的团队协作：同步控制
#### synchronized、wait、notify
这个已经介绍过了，不再重复介绍。
#### synchronized的功能拓展：重入锁（ReentrantLock）
重入锁完全可以替代synchronized关键字。JDK6.0开始，两者性能差不多。
重入锁（Re-Entrant-Lock）之所以称为重入锁，那是因为这种锁是可以反复进入的。当然，这里的反复仅仅局限于一个线程。
有的时候会用到递归调用，在递归里边使用了锁。假设锁无法重入，就会造成死锁，所以有了重入锁。
注意，虽然可以重入，但是也要将锁释放相应的次数。

重入锁相对于synchronized关键字，因为重入锁有着显示的操作过程，开发人员必须手动指定何时加锁，何时解锁，所以重入锁更加的灵活。除此之外，重入锁还提供了一些高级功能。
* 中断响应
对于synchronized来说，如果一个线程在等待锁，那么结果只有两种情况，要么它获得这把锁继续执行，要么它就保持等待。使用重入锁，则提供了第三种可能：线程可以被中断。如果一个线程正在等待锁，那么它依然可以收到一个通知，被告知无需等待了，可以停止工作了。这对于处理死锁是有一定帮助的。

* 锁申请等待限时
使用`tryLock`方法可以设定等待时间，若超过一定时间仍未获得锁，则放弃获得锁，返回false。这对于避免死锁有些帮助。

* 公平锁
在大多数情况下，锁的申请都是非公平的。对于非公平锁，根据系统的调度，一个线程会倾向于再次获得已经持有的锁，这种分配方式是高效的，但是无公平性可言。
公平锁的一大特点是：它不会产生饥饿现象。只要你排队，最终就一定可以获得资源。

对`ReentrantLock`的几个方法整理如下：
* `lock()`
* `lockInterruptibly()`获得锁，但优先响应中断。
* `tryLock()`尝试获得锁，如果成功，返回true
* `trLock(long time, TimeUnit unit)`
* `unlock()`

重入锁的实现
主要包含三个要素：
* 原子状态。使用CAS
* 等待队列。
* 阻塞原语`park`和`unpark()`，用来挂起和恢复线程。

#### 重入锁的好搭档：Condition条件
`Condition`之于`ReentrantLock`，就是`wait`、`notify`之于`synchronized`。
方法概览：
* `await()`等待，相当于`Object.wait()`，但是能够响应中断
* `awaitUninterruptibly()`不响应中断
* `singal()`相当于`Object.notify()`。唤醒的时候要先获得锁，最重要的是不要忘了接着解锁。
* `singalAll()`

#### 允许多个线程同时访问：信号量（Semaphore）
通过信号量可以指定允许同时访问的线程数。
方法概览
* `acquire()`
* `acquireUninterruptibly()`
* `tryAcquire()`
* `tryAcquire(long timeout, TimeUnit unit)`
* `release`

#### 读写锁（ReadWriteLock）
实现了读写分离，有效的帮助减少锁的竞争，以提高系统性能。
读写锁的访问约束情况
|-|读|写|
|--|--|--|
|读|非阻塞|阻塞|
|写|阻塞|阻塞|
可以看到，只有读读是不互斥的。就是说假设有个线程已经获得了读锁，那么写锁的获取就要等待，然而读锁的获取不需要等待。可以直接获取。
在读多写少的情况下，性能提高是很明显的。

#### 闭锁（CountDownLatch）
闭锁也叫倒计时器。Latch为`门栓`的意思。用来控制线程等待。例如100米跑，只有当所有人都跑完了，才能进行下一波。

#### 循环栅栏（CyclicBarrier）
栅栏的功能和闭锁非常相似，不同的是它是循环的。如果说闭锁关注的是一波100米跑，那么栅栏关注的是n波。
栅栏还可以设置每次计数（一波）完成时要执行的任务（会首先执行一次）。

#### 线程阻塞工具类（LockSupport）
LockSupport是一个非常有用的线程阻塞工具，它可以发生在线程内任意位置让线程阻塞。和`Thread.suspend()`相比，它弥补了由于`resume()`在前发生，导致线程无法继续执行的情况。和Object.wait相比，它不需要获得某个对象的锁，也不会抛出InterruptedException异常。

## 线程复用：线程池
若不加控制和管理的随意的使用线程，对系统的性能范围会产生不利影响。在实际的生产环境中，线程的数量必须要得以控制，盲目的大量创建线程对系统性能是有伤害的。

#### 什么是线程池
使用线程池后，创建线程变成了从线程池中获得空闲线程，关闭线程变成了向池子归还线程。

#### JDK对线程池的支持
JDK提供了Executor框架，帮助开发人员有效的进行线程控制，其本质就是一个线程池。Execuotrs类扮演者线程工厂的角色，通过Executors可以取得一个具有特定功能的线程池。
Executors类方法总结
```
newFixedThreadPool(int nThreads) 返回固定线程数量的线程池
newSingleThreadExecutor() 返回只有一个线程的线程池。
newCachedThreadPool() 返回一个可根据实际情况调整线程数量的线程池。
newSingleThreadScheduledExecutor() 定时，线程数为1
newScheduledThreadPool(int corePoolSize) 定时，线程数可指定。
```
#### 核心线程池的内部实现
无论是`newFixedThreadPool(int nThreads) `还是`newSingleThreadExecutor()` 又或者是`newCachedThreadPool() `其内部均使用了ThreadPoolExecutor实现。
ThreadPoolExecutor最重要的构造函数：
```java
public ThreadPoolExecutor(int corePoolSize, 
                        int maximumPoolSize,
                        long keepAliveTime,
                        TimeUnit unit,
                        BlockingQueue<Runnable> workQueue,
                        ThreadFactory threadFactory,
                        RejectedExecutionHandler handler);
```
`corePoolSize`：指定了线程池中的线程数量
`maximumPoolSize`：指定了线程池中的最大线程数量
`keepAliveTime`：当线程池的数量超过corePoolSize是，多余的空闲线程的存活时间。
`unit`：keepAliveTime的单位
`workQueue`：任务队列
`ThreadFactory`：线程工厂，用于创建线程，一般用默认的即可
`handler`：拒绝策略。当任务太多来不及处理时，如何拒绝任务。
参数`workQueue`有以下几种选择：
* 直接提交的队列：`SynchronousQueue`。没有容量。被提交的任务不会被真实的保存，而总是将新任务提交给线程执行。所以为了避免执行拒绝策略，要设置很大的maximumPoolSize。
* 有界的任务队列：`ArrayBlockingQueue`
* 无界的任务队列：`LinkedBlockingQueue`
* 优先任务队列：`PriorityBlockingQueue`

#### 拒绝策略
核心线程池ThreadPoolExecutor类提供了拒载参数。也就是当任务数量超过系统实际承载能力时的处理方案。
JDK内置了四种拒绝策略：
`AbortPolicy`：直接抛出异常，阻止系统正常运行
`CallerRunsPolicy`：只要线程池未关闭，该策略直接在调用者线程中运行当前被丢弃的任务。但是任务提交线程的性能可能会急剧下降。
`DiscardOledestPolicy`：该策略将丢弃最老的一个请求，也就是即将被执行的一个任务，并尝试再次提交当前任务。
`DiscardPolicy`：该策略将会默默地丢弃无法处理的任务，不予任何处理。如果允许任务丢失，这应该是最好的一种解决方案了。

当然，你也可以自定义策略，只要实现`RejectedExecutionHandler`接口。例如，我们可以将对DiscardPolicy进行一些改进，例如最起码要把丢弃的任务信息打印一下。

#### 自定义线程创建：ThreadFactory
线程池只负责线程复用，线程池中有一个参数ThreadFactory，负责线程的创建。
ThreadFactory是一个接口，只有一个方法
```
Thread newThread(Runnable r)
```
通过自定义线程创建可以做很多事情：跟踪线程池究竟在何时创建了多少线程，也可以自定义线程的名称、组以及优先级等信息。总之，使用自定义线程池可以让我们更加自由的设置池子中所有线程的状态。

#### 扩展线程池
虽然JDK已经帮我们实现了这个稳定的高性能线程池，但有时候我们想要对线程池进行一些拓展，例如监控每个任务的开始和结束时间，或者其他一些自定义的增强功能，这时候就可以选择拓展线程池了。
ThreadPoolExecutor是一个可以拓展的线程池，它提供了`beforeExetutor()`、`afterExecutor()`、`terminated()`三个接口对线程池进行拓展。

#### 优化线程池线程数量
估算线程池大小的经验公式：
```
Ncpu = CPU的数量
Ucpu = 目标CPU的使用率，0 <= Ucpu <= 1
W/C = 等待时间与计算时间的比率

Nthreads = Ncpu * Ucpu * (1 + W/C)
```

#### 寻找堆栈信息
线程池很有可能会吃掉程序抛出的异常，导致我们对程序的错误一无所知。

* 方法一：放弃`submit()`，使用`execute()`

* 方法二：或者使用下面的方法改造submit
```
Future re = pools.submit(new DivTash(100, i));
re.get();
```
* 方法三：以上两种方法**只能得到部分堆栈信息**。以上两种方式只能或者异常是在哪里抛出的，但是不能获得任务是在哪里提交的。所以要自己动手，拓展ThreadPoolExecutor线程池。让它在提交任务的堆栈信息之前，先保存一下提交任务的堆栈信息。（有clientTrace属性，所以只要建一个包装类，将clientTrace附上就好了）

#### Fork/Join框架
