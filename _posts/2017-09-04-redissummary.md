---
layout: post
title: redis回顾总结
description: redis回顾总结
keywords: redis
categories : [缓存]
tags : [redis]
---

在项目中已经多次用到了redis，今天想做个总结。目前主要是3个用途：

# 缓存
缓存是一种用得最多的情况，通过灵活运用String,Hash,List,Set,SortedSet等数据结构，可以极大的提高性能。
由于redis是单线程的，所有的操作都是原子操作。所以对于缓存的更新最好做到是一个redis操作，保持原子性，这样在多线程下才不需要加锁互斥。
尽量避免先从redis取出，然后修改，再写入三个步骤的非原子操作，因为这种操作可能需要加锁。

# 互斥锁

### DECR,DECRBY
递减1或者减n,可以用于信号量一定的场景。

### SETNX，GETSET,GET,DEL
- SETNX:当且仅当key不存在，将key的值设为value,并返回1,若给定的key已经存在,则SETNX不做任何动作,并返回0。  
- GETSET:将给定key的值设为value,并返回key的旧值 (old value)，当key存在但不是字符串类型时，返回一个错误，当key不存在时，返回nil。  
- GET:返回key所关联的字符串值，如果key不存在那么返回特殊值nil。  
- DEL:删除给定的一个或多个key ,不存在的key会被忽略。  

第一步：C1, setnx(key,now())返回1，则获取锁，0则没有获取到锁，del(key)则释放锁。    
第二步：C2,C3,C4,当setnx获取锁返回0时，调用GET命令获得foo.lock的时间戳T1，通过比对时间戳，发现锁超时。    
第三步：C4,此时某个客户端发送GESET命令，当返回时间戳为T1时，则获取锁，当返回时间戳为T2，则表示其他C已经获取锁。进入下一个轮回了。  

redis锁要特别注意线程奔溃的问题，防止死锁，以及sleep的问题，减少redis是压力。

### expire KEY seconds
设置key的超时时间，超时自动删除。 

# 消息队列
redis可以作为消息队列使用，jedis提供了publish/subscribe的功能。




