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
不同的任务用不同的线程池分开处理，计算型 Ncpu + 1,IO型的需要多起线程2*Ncpu。
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


# 经验
批量邮件，每封邮件起一个线程，当同时发送5000封邮件时，测试时发现邮件有丢失。  
通过核对，发现线程池这块设置有问题。

- coreSize = 5 
- maxPoolSize = 10
- 队列大小为1000

当产生5000封时，放到Pool会导致饱和，需要设置饱和策略。自定义饱和策略，当丢失时，重新放入，解决了问题。
