---
layout: post
title: 多行打印成一行
description: 多行打印成一行
keywords: shell
categories : [shell]
tags : [shell]
---

# awk

``` 
awk 'BEGIN{RS=EOF} {gsub(/\n/," ");print}'  file  

awk默认将记录分隔符（record separator即RS）设置为\n，此行代码将RS设置为EOF（文件结束），也就是把文件视为一个记录，然后通过gsub函数将\n替换成空格。 

```

### gsub

```
打印替换的字符个数
echo "a b c 2011-11-22 a:d" | awk '{ $1 = gsub(/-/," ");print $1}'

打印替换后的字符
echo "a b c 2011-11-22 a:d" | awk '{gsub(/-/," ");print}'  

sub匹配第一次出现的符合模式的字符串，相当于 sed 's//' 。

gsub匹配所有的符合模式的字符串，相当于 sed 's//g' 。


```
# sed

```
sed ':a ; N;s/\n/ / ; t a ; ' file

sed默认只按行处理，N可以让其读入下一行，再对\n进行替换，这样就可以将两行并做一行。但是怎么将所有行并作一行呢？可以采用sed的跳转功能。:a 在代码开始处设置一个标记a，在代码执行到结尾处时利用跳转命令t a重新跳转到标号a处，重新执行代码，这样就可以递归的将所有行合并成一行。

```





