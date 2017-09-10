---
layout: post
title: redis的数据结构(下)
description: redis的数据结构
keywords: redis
categories : [缓存]
tags : [redis]
---

上篇分析了SortedSet与Set俩种数据结构，这篇主要分析List，String,Hash这3中数据结构。

# List
- 字符串列表。
- 按照插入顺序排序，可以选择在头部添加，或者尾部添加。
- 一个列表最多包含2^32-1个元素。

## 命令

命令 | 描述 | 返回值
---|---|---
lpush key value[value...] | 表头插入 | 返回list中元素的个数
lpop key  | lpop是左边弹出 | 返回表头的第一个元素或者null  
rpush key value[value...] | 表尾插入 | 返回list中元素的个数
rpop key | 表尾弹出 | 返回表尾弹出的第一个元素或者null
lpushx key value | 将一个值表头插入到已经有元素的list中
rpushx key value | 将一个值表尾插入到已经有元素的list中
blpop key[key...] time | 当[key...]中都没有元素时，则等待time时间超时，若一个有元素，则返回 | 每个key返回表头第一个元素,key中没有元素，则不返回
brpop key[key...] time | 同blpop，表尾返回 | 同上
brpoplpush source destination time | 弹出source的表尾元素，插入到des的最左边，time为超时时间 | source弹出的元素
lindex key index | 获取列表中索引对应的元素 | 元素（按照lpush的顺序）
linsert key before/after member value | 将key插入到key2的前面或者后面 | 左边为前面，右边为后面 
llen key | 获取列表长度 | 返回长度
lrange key start stop | 获取start,stop的元素 | 返回获取的元素列表
lrem key count value | 从count开始搜索，删除与value相同的元素 | > 0 表头开始搜索，< 0表尾开始搜索
lset key index value | 通过索引设置元素值 
ltrim key start stop | 获取start,stop的值 | 返回ok
rpoplpush source destination | source表尾取一个元素插入到des的表头

list提供了表头，表尾的插入与弹出，删除，包括阻塞的和非阻塞的，以及元素个数的操作。

# Hash
- string类型的field和value的映射表,hash特别适合用于存储对象。
- 每个hash可以存储2^32-1键值对。

命令 | 描述 | 返回值
---|---|---
hset key field value | 设置key的field和value | 设置成功返回1
hsetnx key field value | 存在，则返回0，不存在，则设置成功，返回1 | 
hmset key field value [field,value] | 一次设置多个field，value | 返回ok
hget key field | 获取key中field的值 | 
hmget key field [field...] | 获取key中field...的值 | 
hgetall key | 获取key中的所有field的值 | 
hexists key field | key的field是否存在 | 存在返回1，否则返回0
hdel key field [field...] | 删除key中的field.. | 返回删除元素的个数
hincrby key field increment | key对应的field加increment,field必须是数字类型字符串 | 添加之后的值
hincrbyfloat key field increment | field对应的value和increment是浮点数 | 添加之后的值
hkeys key | 获取key中所有的fields |
hlen key | 元素个数 | 
hvals key | 获取key对应的values | 
hscan key cursor [match pattern] [count] | 获取key中匹配的元素 

# String

- 字符串

## 命令

命令 | 描述 | 返回值
---|---|---
set key value | 设置key的值 | 返回ok
setnx key value | 设置key的值 | 不存在，设置成功，返回1，否则返回0
setex key secondes value | 设置key的value,同时设置超时时间 |
setbit key offset value | 设置key的对应的value的二进制对应偏移量的值为0或者1 | 返回原来的bit值
setrange key offset value | 修改key对应的value，（offset，end）的对应大数据的值 | 被修改的字符串的长度
mset key value [key,value] | 设置多个key,value的值 |
msetnx key value [key,value] | 设置多个key,value的值，不存在则设置 | 不存在，则返回1，否则返回0
psetex key milliseconds value | 同setex,超时时间为毫秒 | 
get key | 获取key对应的值 |
getrange key start end | 返回key对应的字符串的子串 | 子串
getset key value | 设置新值，返回旧值 | 旧值
gitbit key offset | value的offset未0或1 | bit
mget key [key...] | 批量获取key的值 | values
strlen key | 获取key对应value的长度 | 长度
incr key | key对应的数字字符串+1 | 新值
incrby key increment | 将key所储存的值加上给定的增量值 | 新值
incrbyfloat key increment | 将key所储存的值加上给定的浮点增量值| 新值
decr key | 递减 | 
decrby key increment | -n |
append key value | 若key对应的值为字符串，则添加value | 新值 
 





