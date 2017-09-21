---
layout: post
title: shell处理文本
description: shell处理文本
keywords: shell
categories : [shell]
tags : [shell]
---


shell文本操作

# awk
就是把文件逐行的读入，以空格为默认分隔符将每行切片，切开的部分再进行各种分析处理。
用法 awk '{action pattern}'

```
默认以空格区分，$0(所有行)，$n(第几行)
tac file | awk '{print $1}' 

-F 指定分隔符
tac file | awk -F : '{print $1}'

1行和3行数据用tab隔开
tac file | awk '{print $1 "\t" $3}'

前面和后面分别答应begin,end
head -n file | awk 'BEGIN {print "begin,end"} {print $1","$3} END {print "begin,end"}'

含有Message的行才会执行{print $1}操作
head -n file | awk '/Message/{print $1}'

printf替代print
awk  -F ':' '{printf("filename:%s,linenumber:%s,columns:%s,linecontent:%s\n",FILENAME,NR,NF,$0)}' /etc/passwd

变量与赋值（统计某个文件夹下的总文件大小）
ll | awk 'BEGIN {count=0;print "the sum is"} {count=count+$5} END {print count}'

```

# sort
sort将文件的每一行作为一个单位，相互比较，比较原则是从首字符向后，依次按ASCII码值进行比较，最后将他们按升序输出。

```

排序并且去除重复行。
sort -u file

降序
sort -r file

先按第一列排序，再按第二列排序
ll | awk '{print $7 "\t" $8}' | sort -nr -k 1 -k 2


排序单位
你有没有遇到过10比2小的情况。我反正遇到过。出现这种情况是由于排序程序将这些数字按字符来排序了，排序程序会先比较1和2。  
 ll | awk '{print $5}' | sort -nu

```

# uniq
去重，uniq起作用，所有的重复行必须是相邻的。

```
忽略大小写字符的不同。  
uniq -i file

进行计数，左边显示出现的行数。  
uniq -c file

只显示唯一的行
uniq -u file

```

# head

```
展示前4行
head -n 4 file
```

# tail ，more ，less，cat ，tac ，sed

``` 
tail -100f file  取文件的最后多少条,并不断读取新的内容。    
cat顺序输出。  
tac逆序输出。  
more 输入内容过多，需要分页。Ctrl+f(forward) 向下滚动一屏;Ctrl+b(back)向上滚动一屏。enter 默认加1行。 
less  输入内容过多，需要分页。Ctrl+f(forward) 向下滚动一屏;Ctrl+b(back)向上滚动一屏,enter 默认加1行。

less比more更强大，提供搜索功能。

```

# grep

```
显示file文件里匹配foo字串那行以及上下5行。  
grep -C 5 foo file 

显示foo及前5行。  
grep -B 5 foo file

显示foo及后5行。
grep -A 5 foo file 

去掉含有null的行
grep -v null

```





