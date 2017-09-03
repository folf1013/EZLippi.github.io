---
layout: post
title: java websocket programming(基本原理)
description: java websocket programming
keywords: websocket
categories : [websocket系列]
tags : [java websocket编程学习笔记]
---

本章节讲述了java websocket编程的基本原理，流程。

# websocket端点

- 注解式端点
- 编程式端点
 
在Java中创建websocket端点有俩种方式，基本等价，注解的方式更加方便。

# java websocket api基本对象

websocket的连接，是端到端的连接。

## RemoteEndpoint
websocket对象的另外一端。

- RemoteEndpoint.Basic同步发送。
- RemoteEndpoint.Async异步发送。

## session

每一个客户端到服务器的连接信息都保存在一个session的实例中。

## EndpointConfig

表示websocket端点的配置信息，比如说路径等。

## MessageHandler接口

接收入站消息的方式，比如说部分接收还是全部接收，以文本类型还是二进制类型。

## ServerApplicationConfig

该类允许开发人员配置应用中的接口。

```
public interface ServerApplicationConfig {

    /**
     * Enables applications to filter the discovered implementations of
     * {@link ServerEndpointConfig}.
     *
     * @param scanned   The {@link Endpoint} implementations found in the
     *                  application
     * @return  The set of configurations for the endpoint the application
     *              wishes to deploy
     */
    Set<ServerEndpointConfig> getEndpointConfigs(
            Set<Class<? extends Endpoint>> scanned);

    /**
     * Enables applications to filter the discovered classes annotated with
     * {@link ServerEndpoint}.
     *
     * @param scanned   The POJOs annotated with {@link ServerEndpoint} found in
     *                  the application
     * @return  The set of POJOs the application wishes to deploy
     */
    Set<Class<?>> getAnnotatedEndpointClasses(Set<Class<?>> scanned);
}
```

# 部署
检测war包文件，获取@ServerEndpoit和ServerApplcationConfig实现的所有类，获取所有需要部署的端点集合。
并将这些端点部署到其声明的URI上，然后等待客户端的接入。

# 接入请求
一次握手，采用了http的升级机制，客户端发起upgrade:websocket的http请求，服务器端返回状态码为101，update：websocket的回应，通过一次http握手，websocket连接建立。

# websocket消息通信
![](/images/pimg/wsmsg.png)

tcp连接建已经建立。websocket在tcp之上定义了消息协议的最小帧。
当websocket实现接收入栈信息时，它保证存在一个可以处理连接的端点的实例，并且将其与发起的客户端相连。




