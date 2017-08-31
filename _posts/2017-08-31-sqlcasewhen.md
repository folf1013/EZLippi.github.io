---
layout: post
title: sql中case when的运用
description: case when在分数统计中的使用
keywords: sql
categories : [database]
tags : [case when,sql]
---

sql中case when的语句用的较少，记录一下

# 背景
在一次抽奖活动中，需要统计用户的中积分情况，给用户发积分。

# 生成sql 
其中数据库中有俩张表

用户表t_user
属性 | 描述
---|---
id | 主键
username | 用户名 
 
抽奖记录表t_luckydraw
属性 | 描述
---|---
id | 主键
cuser | 用户id
ticket | 是否中奖（1中奖，-1未中奖）
prize | 奖品类型代号(0：特等奖，其中3，4，5，6为积分)
userid | 抽奖用户


```
select tly.cuserid,sum(
case tly.prize 
  when '0' then '0'  
  when '1' then '0'
  when '2' then '0'
  when '3' then '1000'
  when '4' then '200'
  when '5' then '50'
  else '10'
end
) gradeSun,tu.empl_id,tu.username 
from t_luckydraw tly 
left join t_user tu on tly.cuserid = tu.id 
where tly.ticket = 1 
group by tly.cuserid
order by tu.username

其中prize，(3：1000，4：200，5：50，6：10)
```

当然case when then有另外一种写法
```
select tld.cuserid,sum(
case  
  when tld.prize < 3 then '0'  
  when tld.prize < 3 then '1000'
  when tld.prize < 4 then '200'
  when tld.prize < 5 then '50'
  else '10'
end
) gradeSun,tu.empl_id,tu.username from t_luckydraw  tld
left join t_user tu  on tld.cuserid = tu.id 
where tld.ticket = 1 group by tld.cuserid order by tu.username
```



