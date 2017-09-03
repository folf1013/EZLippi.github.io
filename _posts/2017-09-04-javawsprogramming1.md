---
layout: post
title: java websocket programming(前言)
description: java websocket programming
keywords: websocket
categories : [websocket系列]
tags : [java websocket编程学习笔记]
---

本章节讲述为什么产生websocket的协议的问题。

# 产生的原因

如果服务器端想保持web网站的更新，使得网站更加有趣，迷人，那么服务器端向客户端推送的通道是必须的。
而目前没有好的方式去解决web服务器端的推送。

# 非正规的实现方式

目前web网站为了解决这个问题，采用了一些非正规的实现方式。

## 轮询
浏览器端通过一小段js代码向服务器端轮询更新。强迫服务器端以预定的时间间隔进行更新。

- 会刷新所有的数据
- 网络延迟比较明显

## http keep alive机制

web页面中通过js代码打开一个长连接，它将定期的使用新信息进行更新。

- 浏览器和服务器之间关于连接打开多长时间存在巨大的差异
- 仍然带有完整的http消息报文头等信息


产生了comet等框架，但是仍然没有解决上面的问题。 

# websocket的目的
允许客户端和服务器端建立一个轻量级的连接，并允许双向通信和一个轻量级的内容模型通信。

 
