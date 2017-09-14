---
layout: post
title: 一次有意思的抽奖活动
description: 利用redis实现的抽奖方案
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
![](/images/pimg/drawlucky.png)

## 绳子是否存在

在redis中Set存有所有的绳子，请求过来可以直接判断绳子是否有效。

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
```
String key = flag  + userid;
Long leftCount = jedis.decrBy(key,1L);
if(leftCount + 1 >= 0){
   return true
}
return false;
每天产生一个key,同时给key设置超时时间7天
```
每次抽中前4等奖，执行下decrby方法判断活动期间是否中过。


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
