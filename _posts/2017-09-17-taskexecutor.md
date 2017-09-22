---
layout: post
title: java线程池原理
description: java线程池的内部实现
keywords: 线程池
categories : [多线程]
tags : [高并发]
---

本文主要讲述线程池的原理以及项目中使用遇到的一些问题。

# 优点

- 降低资源消耗，避免每次重复创建销毁。
- 提高响应速度。
- 提高线程的可管理性。

# 工作原理

![](/images/pimg/tpe1.png)

- 核心线程池是否满，否，则新建线程并提交task，之后线程会从阻塞队列中取，并且设置超时时间(poll),超时取到，则销毁线程。
- 阻塞队列是否满，否，则将task放入阻塞队列。
- 判断线程数是否超过最大值，否，则新建。
- 交给饱和策略处理。

# 基本参数

```
ThreadPoolExecutor(int corePoolSize, int maximumPoolSize,long keepAliveTime, TimeUnit unit, 
BlockingQueue<Runnable> workQueue, 
RejectedExecutionHandler handler)
 
corePoolSize： 核心线程池数量。   
maximumPoolSize：线程池维护线程的最大数量。   
keepAliveTime： 线程池维护线程所允许的空闲时间。   
unit： 线程池维护线程所允许的空闲时间的单位。 
workQueue： 线程池所使用的缓冲队列。 
handler： 线程池对拒绝任务的处理策略。 

```

# 四种队列

- LinkedBlockingQueue
链表阻塞队列。
- ArrayBlockingQueue
数组阻塞队列。
- SynchronousQueue
同步队列，没有缓冲区，每次有人提交就得等有人来取。
- PriorityBlockingQueue
需要传入比较器，优先级高的总在最前面，可能优先级低的永远执行不到。


# 三种方式

```

- execute(Runnable command) 
- <T> Future<T> submit(Runnable task)
- <T> Future<T> submit(Callable <T> task)

```

- submit与execute
execute(Runnable x) 没有返回值。可以执行任务，但无法判断任务是否成功完成。  
submit(Runnable x) 返回一个future。可以用这个future来判断任务是否成功完成。也可以判断是否已经完成。  

- Callable与Runnable
Runnable启动线程的方法通常为start，而Callable需要使用ExecutorService submit 方法。 
都返回future对象。Callable有返回值，也可以抛出异常， Runnable无返回值，future仅用来判断执行的状态。

# future模式

Future模式在请求发生时，会先产生一个Future物件给发出请求的客户，而同时间，真正的目标物件之生成，由一个 新的执行绪持续进行，真正的目标物件生成之后，将之设定至Future之中，而当客户端真正需要目标物件时， 目标物件也已经准备好，可以让客户提取使用。


# 三种线程池

强烈建议使用较为方便的 Executors 工厂方法：  
- Executors.newCachedThreadPool()（无界线程池，可以进行自动线程回收）
- Executors.newFixedThreadPool(int)（固定大小线程池） 
- Executors.newSingleThreadExecutor()（单个后台线程）
它们均为大多数使用场景预定义了设置。

# 饱和策略

![](/images/pimg/tpe2.png)

- AbortPolicy
默认，丢弃并抛异常。
- DiscardPolicy
直接丢弃。
- DiscardOldestPolicy
抛弃队列中head的值，再次执行execute。
- CallerRunsPolicy
在调用excute的线程里直接执行，会阻塞入口。
- 自定义策略
实现RejectedExecutionHandler，并自定义策略模式。

# 合理配置线程池

- 计算型任务或者IO密集型任务或者混合型。
不同的任务用不同的线程池分开处理，计算型 Ncpu + 1,IO型的需要多起线程N*(1+WT/ST) 。
- 任务的优先级。
可以使用PriorityBlockingQueue，但是需要考虑低优先级可能永远不会执行。
- 任务的执行时间。
可以分开执行，获取采用PriorityBlockingQueue，让短的先执行。
- 任务的依赖性，譬如数据库连接。
属于IO型。
- 设置成有界队列。
当为无界队列时，当里面的线程执行缓慢，又不断添加线程，可能会导致内存不足，整个系统不可用。

# 监控

线程池的监控主要通过继承线程池，重现线程池beforeExecute,afterExecute,terminated等方法，对线程池的一些关键变量进行记录。

- taskCount
需要执行的任务数。
- completedTaskCount
已经完成的任务数。
- largestPoolSize
最大线程数。
- getPoolSize
线程数量。
- getActiveCount
活动的线程数。

# 线程池的关闭

- shutdown 
正在执行的任务会执行完。
- shutdownnow
任务不一定会执行完。
批量线程池，逐个调用线程的interrupt方法。

# 风险

- 死锁
线程A持有对象X的锁，并且在等待对象Y的锁，而线程B持有对象Y的锁，并且在等待对象X的锁(添加序号规避，或者把AB放到一个线程中执行)。
- 资源不足
如果线程池中的线程数目非常多，这些线程会消耗包括内存和其他系统资源在内的大量资源，从而严重影响系统性能。
- 线程泄露
固定大小线程池，若线程异常不捕获，则会导致线程终止，如果所有的工作线程都异常终止，线程池就最终变为空。另外一种情况就是所有线程都被阻塞了（阻塞任务需设定超时时间）。
- 请求过载
当工作队列中有大量排队等候执行的任务时，这些任务本身可能会消耗太多的系统资源而引起系统资源缺乏。

# 经验
批量邮件，每封邮件起一个线程，当同时发送5000封邮件时，测试时发现邮件有丢失。  
通过核对，发现线程池这块设置有问题。

- coreSize = 5 
- maxPoolSize = 10
- 队列大小为1000

当产生5000封时，放到Pool会导致饱和，需要设置饱和策略。自定义饱和策略，当丢失时，重新放入，解决了问题。

# 
