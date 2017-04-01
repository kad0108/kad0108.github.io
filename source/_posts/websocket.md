---
title: WebSocket
date: 2017-02-15 19:23:41
categories: 
- Front-End
tags:
- Html5
---

**WebSocket是Html5新增的协议，目的就是为了在浏览器和服务器之间建立不受限的双向通信通道，使得服务器能够主动向客户端发送数据。**


## 传统的HTTP协议不能实现WebSocket的功能吗？

HTTP是一个请求－响应协议，必须是浏览器向服务器发起请求，服务器才能响应这个请求，再把数据发送给客户端。也就是说浏览器不主动请求，服务器是没法主动发数据给浏览器的。

<!-- more -->

### 轮询能实现啊

ajax轮询的原理是启动一个定时器，固定间隔向服务器发起请求，问服务器是否有新信息。这种机制频繁请求会给服务器带来巨大的压力。

```
场景再现：
C：有没有新信息？（Request）
S：没有（Response）
C：哎呀有没有新信息 （Request）
S：没有了啦！（Response）
－－－loop
```

还有comet本质也是轮询，客户端在发起连接后，服务器没消息就一直不返回response，直到有消息才返回，返回之后客户端再建立起连接。

```
场景再现：
C：有没有消息？（Request）
S：没有，等有的时候再给你。
...
S: 来，给你消息 (Response)
－－－loop
```

> 如何判断用户是否是在网的状态，就可以采用轮询。也可以使用HTML5提供的navigator.onLine来判断设备是在线还是离线。

## 那WebSocket是怎么实现的啊

```
场景再现：
C：我要建立websocket协议，协议版本xx （Request）
S：OK，已升级为WebSocket协议（HTTP Protocols Switched）
C：麻烦你有信息的时候推送给我哈
S：lalala
S：我给你发送数据啦
S：嗯，加油哦
```

也就是经过一次HTTP请求，就可以源源不断发送信息啦

### WebSocket请求是什么样的呢？

![websocket](/websocket/websocket.png)

该请求和普通的HTTP请求有几点不同：

1. GET请求的地址不是类似/path/，而是以ws://开头的地址；
2. 请求头Upgrade: websocket和Connection: Upgrade表示这个连接将要被转换为WebSocket连接；
3. Sec-WebSocket-Key是用于标识这个连接，并非用于加密数据；
4. Sec-WebSocket-Version指定了WebSocket的协议版本。

响应代码101表示本次连接的HTTP协议即将被更改，更改后的协议就是Upgrade: websocket指定的WebSocket协议。

[简易小demo看这里](https://github.com/kad0108/Html5/tree/gh-pages/webSocket)

## 那HTTP不是也有keep-alive来实现长连接吗？

connection：keep-alive是指在一次 TCP 连接中完成多个 HTTP 请求，但是对每个请求仍然要单独发header。也就是除了真正的数据部分外，服务器和客户端还要大量交换 HTTP header，信息交换效率很低。

WebSocket 解决的第一个问题是，通过第一个 HTTP request 建立了 TCP 连接之后，之后的交换数据都不需要再发 HTTP request了


## Reference

[websocket实现持久连接](https://www.zhihu.com/question/20215561)