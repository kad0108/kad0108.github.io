---
title: 服务器推送信息
date: 2017-08-29 13:23:53
categories: 
- Front-End
tags:
- JavaScript
---

本文总结了服务器将信息主动推送到客户端的几种实现方式：

1. Ajax polling
2. Long polling
3. HTTP Stream
3. WebSockets
4. Server-Sent Events


Ajax是一种从页面向服务器请求数据的技术，Comet是一种服务器向页面推送数据的技术。

有两种方式实现Comet：长轮询和流。

<!-- more -->

## Polling

**短轮询**是浏览器定时向服务器发送请求，看有没有更新的数据。下图是短轮询的时间线。

⚠️注意：短轮询并不是一直发送请求，而是应该在服务器响应后再发送请求，只不过服务器响应返回的不一定是更新的数据。

![ajax polling](/ajax polling.png)

**长轮询**是页面发起一个到服务器的请求，然后服务器一致保持连接打开，直到有数据可发送。发送完数据之后，浏览器关闭连接，随即又发起一个到服务器的新请求。这个过程在页面打开期间一直持续不断。下图是长轮询的时间线。

![long polling](/long polling.png)

无论是短轮询还是长轮询，浏览器都要在接收数据之前，先发起对服务器的连接，两者最大的区别在于服务器如何发送数据。短轮询是服务器立即发送响应，无论数据是否有效，而长轮询是等待发送响应。轮询的优势是所有浏览器都支持，因为使用XHR对象和setTimeout就能实现的。

## HTTP Stream

第二种Comet实现是**HTTP流**。流不同于上述两种轮询，因为它在页面的整个生命周期内只使用一个HTTP连接。浏览器向服务器发送一个请求，服务器保持连接打开，然后周期性地向浏览器发送数据。

所有服务器端语言支持打印到输出缓存然后刷新（将输出缓存中的内容一次性全部发送到客户端）的功能。而这正是实现HTTP流的关键所在。

通过侦听readystatechange事件及检测readyState的值是否为3，就可以利用XHR对象实现HTTP流。在上述这些浏览器中，随着不断从服务器接收数据，readyState的值会周期性变为3，此时responseText属性中酒会保存接收到的所有数据。就需要比较此前接收到的数据，决定从什么位置开始取得最新的数据。使用XHR对象实现HTTP流的典型代码如下：

```
function createStreamingClient(url, progress, finished){
	var xhr = new XMLHttpRequest(),
		received = 0;
	xhr.open("get", url, true);
	xhr.onreadystatechange = function(){
		var result;
		if(xhr.readyState == 3){
			// 只取得最新数据并调整计数器
			result = xhr.responseText.substring(received);
			reveiced += result.length;
			// 调用progress回调函数
			progress(result);
		}else if(xhr.readyState == 4){
			finished(xhr.responseText);
		}
	};
	xhr.send(null);
	return xhr;
}
var client = createStreamingClient("streaming.php", function(data){
	alert("Recevied: " + data);
}, function(data){
	alert("Done!");
})
```

这个createStreamingClient函数接收三个参数：要连接的url，在连接到数据时调用的函数以及关闭连接时调用的函数。有时候，当连接关闭时，很可能还需要重新建立，所以关注连接什么时候关闭还是有必要的。

只要readystatechange事件发生，而且readyState值为3，就对responseText进行分割以取得最新数据。这里的received变量用于记录已经处理了多少个字符，每次readyState值为3时都递增。然后通过progress回调函数来处理传入的新数据。当readyState值为4时，则执行finished回调函数，传入响应返回的全部内容。

这个例子比较简单，在大多数浏览器中正常运行（IE除外），但管理Comet的连接是很容易出错的，需要时间不断改进才能达到完美。

## Server-Sent Events

**服务器发送事件SSE**是围绕只读Comet交互推出的API或者模式。SSE API用于创建到服务器的单向连接，服务器通过这个连接可以发送任意数量的数据。服务器响应的MIME类型必须是`text/event-stream`，而且是浏览器中的JavaScript API能解析格式输出。SSE支持短轮询、长轮询和HTTP流，而且能在断开连接时自动确定何时重新连接。

### SSE API

要预定新的事件流，首先要创建一个新的EventSource对象，并传入一个入口点：

```
var source = new EventSource("myevents.php");
```

注意，传入的URL必须与创建对象的页面同源（相同的URL模式，域及端口）。EventSource的实例有一个readyState的属性，值为0表示正连接到服务器，值为1表示打开了连接，值为2表示关闭了连接。

另外还有三个事件：

1. open：在建立连接时触发。
2. message：在从服务器接收到新事件时触发。
3. error：在无法建立连接时触发。

```
source.onmessage = function(event){
	var data = event.data;
	// 处理数据
}
```

服务器发回的数据以字符串形式保存在`event.data`中。

默认情况下，EventSource对象会保持与服务器的活动连接。如果连接断开，还会重新连接，这就意味着SSE适合长轮询和HTTP流。如果想强制立即断开连接并且不再重新连接，可以调用close方法。

```
source.close();
```

### 事件流

所谓的服务器事件会通过一个持久的HTTP响应发送，这个响应的MIME类型为`text/event-stream`。响应的格式是纯文本，最简单的情况是每个数据项都带有前缀`data:`，例如：

```
data: foo
// blank line
data: bar
// blank line
data: foo
data: bar
```

对以上响应，事件流中的第一个message事件返回的`event.data`值为foo，第二个message事件返回的是bar，第三个message事件返回的`event.data`是`foo\nbar`（注意中间的换行符）。对于多个连续的以`data:`开头的数据行，将作为多段数据解析，每个值之间以一个换行符分隔。只有在包含`data:`的数据行后面有空行时，才会触发message事件，因此在服务器上生成事件流时不能忘了多添加这一行。

通过`id:`前缀可以给特定的事件指定一个关联的ID，这个ID行位于`data:`行前面或后面。

```
data: foo
id: 1
```

设置了ID后，EventSource对象会跟踪上一次触发的事件，如果连接断开，会向服务器发送一个包含名为`Last-Event-ID`的特殊HTTP头部的请求，以便服务器知道下一次该触发哪个事件。在多次连接的事件流中，这种机制可以确保浏览器以正确的顺序收到连接的数据段。

## WebSockets

[WebSockets总结详见这篇博文](http://kadong.space/2017/02/15/websocket/)

### SSE与WebSockets

面对某个具体的用例，在考虑是使用SSE还是使用WebSocket时，可以考虑如下几个因素。首先，你是否有自由度建立和维护WebSockets服务器？因为Web Socket协议不同于HTTP，所以现有服务器不能用于Web Socket通信。SSE通过常规HTTP通信，因此现有服务器可以满足需求。

第二个要考虑的问题是到底需不需要双向通信。如果用例只需读取服务器数据（比如比赛成绩），那么SSE比较容易实现，如果用例必须双向通信（如聊天室），那么Web Sockets显然更好。别忘了，在不能选择Web Sockets的情况下，组合XHR和SSE也是能实现双向通信的。

> 以上内容基本都摘自《JS高级程序设计》一书。