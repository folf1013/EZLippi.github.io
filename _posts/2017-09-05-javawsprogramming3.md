---
layout: post
title: java websocket programming(生命周期)
description: java websocket programming
keywords: websocket
categories : [websocket系列]
tags : [websocket]
---

与http的请求，响应的模式不同，websocket具有生命周期。

# websocke协议

## 控制帧
内部功能逻辑的数据帧。
- 关闭帧：发送者即将关闭连接。
- Ping帧：当一端想知道连接的状态时，可以向另外一端发送Ping帧。
- Pong帧：当收到Ping帧时，需要回复Pong帧。

## 数据帧
俩种基本类型，分为文本型和二进制类型（图像数据），同时不管是哪种类型，都可以携带全部数据或者数据的一部分，
又称为部分帧，部分帧在传递很大的数据或者是正在生成的数据时特别有用。

websocket协议主要用于传输短消息，但是并没有限制消息的大小。

# Java websocket生命周期

![](/images/pimg/wsmsg3.png)

总的来说就是4个过程，建立连接，传送消息，错误处理，连接关闭。
有点错误是可以恢复的，有的错误会导致连接关闭。

# Java websocket api生命周期

- OnOpen
打开在所有操作之前，主要包含实例化的session对象，EndpointConfig对象，@PathParam对象。

- OnMessage
消息事件伴随的信息是Session对象(它表示消息抵达时的连接)、EndpointConfig对象、打开阶段握手中从匹配入站URI过程中获取的路径参数以及最重要的消息本身。
连接上的消息将以3种基本形式抵达:文本消息、二进制消息或Pong消息。

当消息是分片消息时，会带有一个boolean值，为false，表示后面还有分片没到，若为true,则表示是最后一片了。

onMessage修饰的值有返回类型值时，接收消息之后，马上就会返回。
  
- OnError
出现错误时，会调用该方法。

- OnClose
连接关闭时，调用该方法，最后调用的方法。

对于编程式唯一需要处理事件是打开事件，它提供了对会话和配置信息的访问。不需要拦截其他
任何生命周期事件。为了处理入站消息,你需要提供MessageHandler实现，对于文本消息, 使用MessageHandler.Whole<String>; 
对于二进制消息,使用MessageHandler.Whole<ByteBuffer>。

# 实例数目和线程机制
WebSocket容器在每次有新的客户端连接时会实例化端点的一个新的实例。这样做的结果是每个WebSocket端点实例仅能够永远“看到”同样的会话实例，
此实例表示从唯一的客户端连接到那个端点实例的唯一连接。

WebSocket实现也为你提供了另外一个重要的保证:同一个会话(或者是连接)中不允许两个事件线程同时调用一个端点实例，这样避免了多线程问题。





