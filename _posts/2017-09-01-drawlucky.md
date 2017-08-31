---
layout: post
title: 一次有意思的抽奖活动
description: 利用redis,zk锁实现的抽奖方案
keywords: 抽奖
categories : [案例]
tags : [高并发]
---

一次有意思的抽奖方案。
有很多绳子，每个用户每天可以点击n次，每根绳子可以点击1次，有m的奖品总量，要求在k天把奖品全部抽完。
奖品分为1-6等奖，但是前面1-4等奖每个用户只能中一次。

# 设计思想
由于之前并没有进行过抽奖，没有相关数据进行参考，故每天投放的奖品数和中奖的概率需要可以手动调节。

## 设计方案
![](/images/images/luckydraw.png)


## 每个用户点每天击n次设计

```
String key = flag + dateStr + userid;
Long leftCount = jedis.decrBy(key,1L);
if(leftCount + n >= 0){
   return true
}
return false;

key中dateStr当天的时间,每天产生一个key,同时给key设置超时时间7天
```

## 每根绳子每天可点击一次
```
String key = flag + dateStr + userid;
Long ropeLeftCount = jedis.decrBy(key,1L);
if(leftCount + 1 >= 0){
   return true
}
return false;
key中dateStr当天的时间,每天产生一个key,同时给key设置超时时间7天
```

## 前4等奖每个用户只能中一次
添加zk锁，创建节点成功则获取锁，否则获取锁失败，锁的粒度为每个用户一个锁。当一个抽中前4等奖时，
添加锁，若是获取锁失败(正常情况下，一个用户只能在一个地方登陆，这种情况可能是刷奖)，则将该请求直接丢弃，
若是获取锁，判断该用户是否中过前4等奖

## 奖品的投放
将所有的奖品投放到奖品总池中(redis中)，每天通过调用接口，手动将奖品从奖品总池取到奖品分发池(redis)中，
奖品分发池中有的奖品才能被抽取。通过这种方式控制每天奖品的投放。

## 中奖概率的调节
在抽奖A环节，是通过random.nextInt(max)来取一个值，max可以通过接口更改（放在redis）中，同时抽中哪些值就中奖也可以更改，
通过修改最大范围值和中奖的值来调节中奖的概率。

## 1-4等奖的概率要小于5-6等奖
当在抽奖B环节（取nextInt(7),确定奖品），当抽到1-4等奖时，需要再进行一次B抽奖，只有俩次数字一样才能中1-4等奖，否则转化为5-6等奖


[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help
