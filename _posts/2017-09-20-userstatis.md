---
layout: post
title: 系统用户数统计
description: shell统计用户数
keywords: shell
categories : [案例]
tags : [shell]
---


# 功能

shell统计系统日活用户数，统计使用过的用户数。并用jenkins调用脚本执行。

# 原理

- 程序中对日志进行埋点，输出用户信息日志。
- 日志为每天生产一个文件，对每一份日志的用户信息提取出来存到一个单独的文件。
- 并保存一份所有使用过用户的文件。

# shell


statistic_user.sh

```
datestr=`date "+%G-%m-%d"`
cat /path/server-default.$datastr.log | grep HostHolder | awk '{print $9}' | cut -c 1-60 | sort -u | grep -v null > /path/statistic/user/used_user.log.$datestr

cat /path/statistic/user/used_user* | sort | uniq > /home/xiaoju/path/statistic/user/used_user_all.log

```

showuser.sh

```
source /etc/profile
/path/statistic_user.sh
cd /path/statistic/user
whitelist=`mysql -u*** -p*** -e "select count(1) from user where status=0 " | grep -v count`
echo 'whitelist: '$whitelist
used=`cat used_user_all.log | wc -l`
echo 'used user: '$used
find . -type f | grep -v used_user_all | sort -r | grep -v history_use |  grep -v showuser | xargs wc -l | grep -v total | sed 's/.\/used_user.log.//g' | awk '{print $2" "$1}'

```

# 常用命令

- date
echo `date "+%G-%m-%d"`   
2017-09-21

- grep
grep -v null  
去掉包含null的行

- awk
文本处理，按行，默认空格分隔 $0(所有行) $n(第n行)

- cut 
擅长处理“以一个字符间隔”的文本内容。
-b:byte,字节  
-c:char,字符  
-f:field,域  

- sort
排序 sort -u = sort | uniq

- arges与管道命令
管道:将前面的标准输出作为后面的标准输入。  
xargs:是实现“将标准输入作为命令的参数。  

``
echo "--help"|cat   
--help
echo "--help"|xargs cat
输出帮助信息
``

- sed
查找与替换 sed 's/查找的内容/替换的内容/(g替换全部和替换第一个的区别)'

- uniq
对应相邻的重复行去掉重复行。

- find
find查找 -type为类型，f为文件

