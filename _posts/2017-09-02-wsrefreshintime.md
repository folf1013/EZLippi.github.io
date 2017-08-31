---
layout: post
title: web实时推送之实时更新
description: websocket实现web实时推送
keywords: websocket
categories : [案例]
tags : [websocket]
---

一个面板有多人打开，当有人进行写操作，如何在不刷新的页面的情况下，把写的内容推送到所有打开的该面板的浏览器上，这就是websocket要解决的问题。

# 设计思想
总的设计思想是后端搭建消息通道，具体的消息类型由前端自己定义。

## 消息通道
假设有5个浏览器打开A面板，5个浏览器打开B面板，当某个A面板向服务器发送一条消息时，服务器会将该消息转发给其他4打开A面板的浏览器。

# 设计方案

![](/images/pimg/ws1.png)

当客户端A向服务器端发送消息时，服务器端将消息publish(topic socket.io)到消息队列中，因为每个服务器端在启动时都subscribe socket.io，
故所有服务器都能拿到客户端A发送的消息，然后每个服务器根据过滤原则，转发给需要转发的客户端。

## 如何区分确定同一面板

```
public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
    registry.addHandler(myHandler(), "/socket.io/{kind}/{code}").setAllowedOrigins("*").addInterceptors
            (myInterceptors()).withSockJS();
}
```
kind表示类别，code表示这个类别下的唯一标识，当kind标识面板时，code可以使用面板的id。只有kind和code都相同时，才是同一个面板。

## 连接状态
socket服务器启动后，等待socket客户端的连接，每个客户端与服务器的连接都会产生一个session,通过这个session，服务器端可以获取连接信息，
向该客户端发送消息，断开连接等所有操作。

# 调试与测试

## 调试
可以通过chrome浏览器的工作台可以查看收到的消息，心跳消息，连接状态，连接信息等。

## 压测
websocket-bench命令可以模拟n个线程发送m个请求。

# 注意事项
- 通过压测，每个socket服务端实例可以同时建立2000个连接。多的连接会自动断掉。当连接数过多时，需要在一个服务器实例中起多个socket服务器实例。
- 重连机制，当上线时或者其他情况下，socket连接有可能断开，需要前端制定合适的socket断开重连机制。


