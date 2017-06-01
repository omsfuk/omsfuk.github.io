-----------------------
title: Java并发编程基础
tags: java,并发
-----------------------
# 线程的状态
```
public enum State {
    NEW,
    RUNNABLE,
    BLOCKED,
    WAITING,
    TIMED_WAITING,
    TERMINATED
}

```

# 线程的基本操作
## 新建线程
* 继承Thread
* 实现Runnable

使用start方法启动线程（发送启动“信号”）
## 终止线程
* stop（太暴力，不建议使用）
* 增加轮询出口
```
@Override
public void run() {
    while(!isStop) {
        ...
    }
}
```

## 线程中断
中断就是让目标线程停止运行的意思。严格来说，线程中断并不会使程序立即退出，而是给线程发送一个通知，告知目标线程，有人希望你退出了。至于目标线程接到通知后如何处理，则完全由目标线程自行决定。
与线程中断有关的有三个方法：
```
public void Thread.interrupt()//中断线程
public boolean Thread.isInterrupted()//判断是否被中断
public static boolean Thread.isInterrupt()//判断是否被中断，并清除当前中断状态
```
```
@Override
public void run() {
    while(true) {
        if(Thread.currentThread().isInterrupted()) {
            break;
        }
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {        
            Thread.currentThread.interrupt();
        }
    }
}
```
当中断异常被捕捉到时，中断标志位会被清除，那么在下一次循环开始时，就无法捕获这个中断，故需要在异常处理中，在此设置中断标志位

## 等待（wait）和通知（notify）
有关的方法如下，这几个方法并不在Thread类中，而是Object类的方法
```
public final void wait(long timeout) throws InterruptedException
public final void wait() throws InterrupedException
public final native void notify()
public final void notifyAll()
```
使用wait时，要先获得当前对象的锁

当在一个对象实例上调用wait后，当前线程就会进入这个对象的等待队列。当此对象的notify被调用时，就会从等待队列中* **随机** *唤醒一个，而当notifyAll被调用时，将会唤醒所有对象，此等待队列所有线程都将被唤醒，一起竞争CPU。

## 挂起（suspend）和继续执行（resume）线程
注意：* **已经废弃** *
原因：suspend方法在导致线程暂停的同时，并不会去释放任何资源，此时，其他任何线程想要访问被它暂用的锁时，都会被牵连，导致无法正常运行。
如果resume方法意外的在suspend前就执行了，那么被挂起的线程可能很难有机会被继续执行。

## 等待线程结束（join）和谦让（yield）
* join
调用join后，它会一直阻塞当前线程，知道目标线程执行完毕。
```
public final void join() throws InterruptedException
public final void join(long millis) throws InterruptedException
public final void join(long millis, int nanos) throws InterruptedException
```
* yield
```
public static native void yield();
```
这是一个静态方法，一旦执行，它会使当前线程让出CPU。要注意，让出CPU并不代表当前线程不执行了，当前线程让出CPU后，还会进行CPU资源的争夺，但是是否能够再次被分配到，就不一定了。

# volatile 与 Java 内存模型（JMM）
当你用`volatile`去申明一个变量时，就等于告诉了虚拟机，这个变量极有可能会被某些程序或线程改变。为了确保这个变量被修改后，应用程序范围内的所有线程都能够“看到”这个改动，虚拟机就必须采用一些特殊的手段，保证这个变量的可见性等特点。

例如在32位虚拟机中，long类型极易被写坏，为了确保不被写坏，最简单的方法就是加入volatile声明。

volatile可以保证有序性和可见性，但无法保证原子性。

## 分门别类的管理：线程组
尽量给线程起一个友好的名字，便于调试
```
ThreadGroup tg = new ThreadGroup("PrintGroup");
Thread t1 = new Thread(tg, new MyThread(), "T1");
Thread t2 = new Thread(tg, new MyThread(), "T2");
t1.start();
t2.start();
System.out.println(tg.activeCount());
tg.list();
```

## 驻守后台：守护线程（Daemon）
setDaemon方法一定要在start方法之前调用。如果不这么做，只会抛一个异常，然后程序和线程依旧可以正常执行，但是会被当做用户线程。
当用户线程全部结束，守护线程救护自动终止（即时设置为死循环）

## 线程优先级
```
public final static int MIN_PRIORITY = 1
public final static int MIN_PRIORITY = 5
public final static int MIN_PRIORITY = 10;
```

## 线程安全的概念与 synchronized

`syschronized`关键字如下集中用法：
指定加锁对象：对给定对象加锁，进入同步代码前要获得给定对象的锁
直接作用于实例方法：相当于对当前实例加锁，进入同步代码前要获得当前实例的锁
直接作用于静态方法：相当于对当前类加锁，进入同步代码前要获得当前类的锁。

## 程序中的幽灵
