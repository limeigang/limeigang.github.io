---
layout:     post
title:      "多线程知识点（一）"
subtitle:   " \"线程创建⽅方式，五种状态，锁机制,线程池\""
date:       2018-07-03 23:00:00
author:     "Mr.Xu"
header-img: ""
#catalog: true
tags:
    - java
    - 多线程
---

> “talk is cheap，show me your code. ”



整理了一下多线程的相关知识点，包括线程创建⽅方式，五种状态，锁机制,线程池，本地线 程等。

## 一、线程创建方式：

1. **继承Thread类，重写run（）方法：**

```java
public class ThreadOne extends Thread {
    @Override
    public void run() {
       system.out.println("第一种创建线程的方式");
    }
}

// 启动线程方式
ThreadOne threadOne = new ThreadOne();
threadOne.start();
```

**2.实现Runable接口：**

```java
public class ThreadTwo implents Runable {
    @Override
 public void run() {
  system.out.println("第二种创建线程的方式");
    }
}

// 启动线程方式
Thread thread = new Thread(new ThreadTwo());
thread.start();
```

**3.通过Callable和Future实现：**

```java
 FutureTask<String> ft = new FutureTask<String>(new Callable<String>() {
             
             @Override
            public String call() throws Exception {
                System.out.println("new Thread 3");//输出:new Thread 3
                 return "aaaa";
            }
        }

// 启动方式
 Thread t3 = new Thread(ft);
        t3.start();
         String result = ft.get();
         System.out.println(result);
```

>  创建线程的方式： 1.继承Thread类，并复写run方法，创建该类对象，调用start方法开启线程。 2.实现Runnable接口，复写run方法，创建Thread类对象，将Runnable子类对象传递给Thread类对象。调用start方法开启线程。   第二种方式好，将线程对象和线程任务对象分离开。降低了耦合性，利于维护。   3.创建FutureTask对象，创建Callable子类对象，复写call(相当于run)方法，将其传递给FutureTask对象（相当于一个Runnable）。创建Thread类对象，将FutureTask对象传递给Thread对象。调用start方法开启线程。这种方式可以获得线程执行完之后的返回值。   第三种使用Runnable功能更加强大的一个子类.这个子类是具有返回值类型的任务方法. 第一种和第二种两种方式是run()方法运行完是没有返回值的.

## JDK五种状态：

**1.新建(NEW)：**新创建了一个线程对象。

**2.可运行(RUNNABLE)：**线程对象创建后，其他线程(比如main线程）调用了该对象的start()方法。该状态的线程位于可运行线程池中，等待被线程调度选中，获取cpu 的使用权 。

**3.运行(RUNNING)：**可运行状态(runnable)的线程获得了cpu 时间片（timeslice） ，执行程序代码。

**4.阻塞(BLOCKED)**：阻塞状态是指线程因为某种原因放弃了cpu 使用权，也即让出了cpu timeslice，暂时停止运行。直到线程进入可运行(runnable)状态，才有机会再次获得cpu timeslice 转到运行(running)状态。阻塞的情况分三种： 

- **等待阻塞：**运行(running)的线程执行o.wait()方法，JVM会把该线程放入等待队列(waitting queue)中。
- **同步阻塞：**运行(running)的线程在获取对象的同步锁时，若该同步锁被别的线程占用，则JVM会把该线程放入锁池(lock pool)中。
-  **其他阻塞：**运行(running)的线程执行Thread.sleep(long ms)或t.join()方法，或者发出了I/O请求时，JVM会把该线程置为阻塞状态。当sleep()状态超时、join()等待线程终止或者超时、或者I/O处理完毕时，线程重新转入可运行(runnable)状态。

**5.死亡(DEAD)：**线程run()、main() 方法执行结束，或者因异常退出了run()方法，则该线程结束生命周期。死亡的线程不可再次复生。

![img](/img/2018-06-04-javaTread.jpg)

**更多内容详见**：[Java多线程学习](https://blog.csdn.net/Evankaka/article/details/44153709)



## 与等待队列相关的步骤和图：

![img](/img/2018-06-04-javaTread2.jpg)

- 线程1获取对象A的锁，正在使用对象A。
- 线程1调用对象A的wait()方法。
- 线程1释放对象A的锁，并马上进入等待队列。
- 锁池里面的对象争抢对象A的锁。
- 线程5获得对象A的锁，进入synchronized块，使用对象A。
- 线程5调用对象A的notifyAll()方法，唤醒所有线程，所有线程进入锁池。||||| 线程5调用对象A的notify()方法，唤醒一个线程，不知道会唤醒谁，被唤醒的那个线程进入锁池。
- notifyAll()方法所在synchronized结束，线程5释放对象A的锁。
- 锁池里面的线程争抢对象锁，但线程1什么时候能抢到就不知道了。||||| 原本锁池+第6步被唤醒的线程一起争抢对象锁。

## java锁机制和线程池：（转）

**锁机制：**

主要关键词：**synchronized、lock、volatile**

锁主要用来实现资源共享的同步。只有获取到了锁才能访问该同步代码，否则等待其他线程使用结束释放锁。

## 一、volatile内存语义：

volatile是Java虚拟机提供的轻量级的同步机制，是线程不安全的，volatile关键字有如下两个作用:

（1）violate用于锁定一个变量，保证变量的值是直接从内存中读取，而不是从缓存中读取，使得被volatile修饰的共享变量对所有线程总数可见的，也就是当一个线程修改了一个被volatile修饰共享变量的值，该变量的改变立即写到主存，这样在其它的线程查看此变量的时候是最新的值。在Java中为了加快程序的运行效率，对一些变量的操作通常是在该线程的寄存器或是CPU缓存上进行的，之后才会同步到主存中，而加了volatile修饰符的变量则是直接读写主存。

（2）禁止指令重排序优化。

**二、synchronized和lock两者的区别？**

**1、synchronized和lock的用法区别：**

**（1）synchronized(隐式锁)**：在需要同步的对象中加入此控制，synchronized可以加在方法上，也可以加在特定代码块中，括号中表示需要锁的对象。 

**（2）lock（显示锁）：**lock通过java代码实现，有更精确的线程语义，需要显示指定起始位置和终止位置。一般使用ReentrantLock类做为锁，多个线程中必须要使用一个ReentrantLock类做为对象才能保证锁的生效。且在加锁和解锁处需要通过lock()和unlock()显示指出。所以一般会在finally块中写unlock()以防死锁。

**2、synchronized和lock性能区别：** 

（1）synchronized是托管给JVM执行的，系统会监控锁的释放与否，而lock是java写的控制锁的代码，需要手动释放。

（2）竞争不激励的情况下，synchronize比lock性能好。在多线程环境并存在大量竞争的情况下，synchronized的用时迅速上升，而lock却依然保存不变或增加很少。

（3）Lock是用CAS（Compare and Swap）来实现的。JDK 1.6以上synchronized也改用CAS来实现了，所以两者性能差不多。

**3、synchronized和lock机制区别：**

（1）synchronized原始采用的是CPU悲观锁机制，即线程获得的是独占锁。独占锁意味着其他线程只能依靠阻塞来等待线程释放锁。JDK 1.6以上synchronized也改用CAS来实现了。 （2）Lock用的是乐观锁方式。所谓乐观锁就是，每次不加锁而是假设没有冲突而去完成某项操作，如果因为冲突失败就重试，直到成功为止。乐观锁实现的机制就是CAS操作（Compare and Swap）。

**乐观锁与悲观锁的区别：**

（1）悲观锁(Pessimistic Lock), 顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。

（2）乐观锁(Optimistic Lock), 顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。乐观锁适用于多读的应用类型，这样可以提高吞吐量，像数据库如果提供类似于write_condition机制的其实都是提供的乐观锁。

两种锁各有优缺点，不可认为一种好于另一种，像乐观锁适用于写比较少的情况下，即冲突真的很少发生的时候，这样可以省去了锁的开销，加大了系统的整个吞吐量。但如果经常产生冲突，上层应用会不断的进行retry，这样反倒是降低了性能，所以这种情况下用悲观锁就比较合适。

**三、有关线程同步的问题：**

**1、同步代码块和同步函数的区别？**  

（1）同步代码块使用的锁可以是任意对象。

（2）同步函数使用的锁是this，静态同步函数的锁是该类的字节码文件对象。  

（3）在一个类中只有一个同步，可以使用同步函数。如果有多同步，必须使用同步代码块，来确定不同的锁。所以同步代码块相对灵活一些。

**2、线程中sleep与wait的区别？**

**（1）sleep（）方法：**         sleep()使当前线程进入停滞状态（阻塞当前线程），让出CUP的使用、目的是不让当前线程独自霸占该进程所获的CPU资源，以留一定时间给其他线程执行的机会;sleep()是Thread类的Static(静态)的方法；因此他不能改变对象的机锁，所以当在一个Synchronized块中调用Sleep()方法是，线程虽然休眠了，但是对象的机锁并木有被释放，其他线程无法访问这个对象（即使睡着也持有对象锁）。     在sleep()休眠时间期满后，该线程不一定会立即执行，这是因为其它线程可能正在运行而且没有被调度为放弃执行，除非此线程具有更高的优先级。 

**（2）wait（）方法：**     wait()方法是Object类里的方法；当一个线程执行到wait()方法时，它就进入到一个和该对象相关的等待池中，同时失去（释放）了对象的机锁（暂时失去机锁，wait(long timeout)超时时间到后还需要返还对象锁）；其他线程可以访问；

​    wait()使用notify或者notifyAlll或者指定睡眠时间来唤醒当前等待池中的线程。notify()和notifyAll()方法只是唤醒等待该对象的monitor（即锁）的线程，并不决定哪个线程能够获取到monitor（即锁）。

**（3）所以sleep()和wait()方法的最大区别是：**

**sleep()睡眠时，保持对象锁，仍然占有该锁；而wait()睡眠时，释放对象锁。**

**原文链接：**[Java锁机制以及Java多线程、线程池](https://blog.csdn.net/a745233700/article/details/80806349)

 