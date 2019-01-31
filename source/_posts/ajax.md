---
title: Ajax和跨域总结
date: 2017-03-11 21:41:17
categories: 
- Front-End
tags:
- JavaScript
---

## Ajax = 异步JS + XML

通过在后台与服务器进行少量数据交换，AJAX 可以使网页实现异步更新(不阻塞)。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。

### 创建XMLHttpRequest对象，用于和服务器交换数据

浏览器兼容写法：
```
var xhr;
if(window.XMLHttpRequest){
	xhr = new XMLHttpRequest();
}eles if(window.ActiveXObject){
	// code for IE5,IE6
	xhr = new ActiveXObject(Microsoft.XMLHTTP);
}
```

<!-- more -->

### 向服务器发起请求：

**GET or POST?**

与 POST 相比，GET 更简单也更快，并且在大部分情况下都能用。然而，在以下情况中，请使用 POST 请求：

1. 无法使用缓存文件（更新服务器上的文件或数据库）
2. 向服务器发送大量数据（POST 没有数据量限制）
3. 发送包含未知字符的用户输入时，POST 比 GET 更稳定也更可靠

**GET请求**

```
xhr.open("GET", url, true);
xhr.send(null);
```

为避免获取到的缓存结果，可以向url添加一个唯一的ID：
```
xhr.open("GET","demo_get.asp?t=" + Math.random(),true);
```

通过 GET 方法发送信息，请向 URL 添加信息：
```
xhr.open("GET","demo_get2.asp?fname=Bill&lname=Gates",true);
```

**POST请求**

需要使用 setRequestHeader() 来添加 HTTP 头。然后在 send() 方法中规定希望发送的数据：
```
xmlhttp.open("POST","ajax_test.asp",true);
xmlhttp.setRequestHeader("Content-type","application/x-www-form-urlencoded");//提交时的内容类型
xmlhttp.send("fname=Bill&lname=Gates");
```

### 发送XHR请求时，会发送的HTTP头部信息：
```
Accept：浏览器能够处理的内容类型
Accept-Charset：浏览器能够显示的字符集
Accept-Encoding：浏览器能够处理的压缩编码
Accept-Language：浏览器当前设置的语言
Connection：浏览器与服务器之间连接的类型
Cookie：缓存
Host：发出请求的页面所在的域
Referer：发出请求的页面的URI
User-Agent：浏览器的用户代理字符串
```

![ajax http header](/ajaxheader.png)

使用setRequestHeader()可以设置自定义的请求头部信息。要成功发送请求头部信息，必须在open方法之后和send方法之前调用。


### 服务器响应：

responseText：获得字符串形式的响应数据。
responseXML：获得 XML 形式的响应数据

### 原生JS写Ajax

XMLHttpRequest Level 1 的写法：
```
var url = 'xx.json';
var xhr = new XMLHttpRequest();
xhr.onreadystatechange = function(){
	if(xhr.readyState == 4 && xhr.status == 200){
		console.log(xhr.responseText);
	}
};
xhr.open("GET", url);
xhr.send(null);
```

XMLHTTPRequest Level 2 的写法：
```
var url = 'xx.json';
var xhr = new XMLHttpRequest();

xhr.open('GET', url);
xhr.send(null);

xhr.onload = function(){
	if(xhr.status == 200 || xhr.status == 304){
		console.log(JSON.parse(xhr.responseText));
	}
}
```

**老版本缺点：**

1. 只支持文本数据的传送，无法用来读取和上传二进制文件。
2. 传送和接收数据时，没有进度信息，只能提示有没有完成。
3. 受到"同域限制"（Same Origin Policy），只能向同一域名的服务器请求数据。


**XMLHttpRequest Level 2新版本的功能：**

1. 可以设置HTTP请求的时限，xhr.timeout，xhr.ontimeout。
2. 可以使用FormData对象管理表单数据，xhr.send(new FormData(form))。
3. 可以上传文件, formData.append('files[]', files[i])。
4. 可以请求不同域名下的数据（跨域请求）,CORS，使用跨域资源共享的前提是浏览器支持且服务器同意。
5. 可以获取服务器端的二进制数据。
6. 可以获得数据传输的进度信息，xhr.onprogress。


> 面试题：Ajax实现分页，分页切换url不变，现在想实现点击浏览器后退按钮回退到前一页而不是上一个链接

可以采用“地址栏加hash”技术来解决。只要改变锚点内容（‘#’后的），浏览器就认为URL变化，也就会放入浏览历史，这样前进后退问题就解决了。我们只要把不同的AJAX状态通过不同的hash写到地址栏就可以了，当页面加载的时候检测是否有hash值，有的话就读取hash进行相应的ajax还原操作。location.hash可以取到或者设置hash的值，当hash改变的时候window.onhashchange事件会被触发。


### JQuery的Ajax使用

```
$.ajax({
	url: './data.json',
	type: 'GET',
	aysnc: true,
	data: {//发送给服务器的数据

	},
	timeout: 5000, //超时
	dataType: 'json', //服务器返回的数据格式

	beforeSend:function(xhr){
        //发送请求前可修改 XMLHttpRequest 对象的函数，如添加自定义 HTTP 头
    },
	success: function(data, textStatus){
		//根据 dataType 参数进行处理后的服务器返回数据
	},
	error: function(){
		//请求出错处理
	},
	complete:function(){
        //请求完成之后调用这个函数，无论成功或失败
    }
})
```

jquery调用ajax中文乱码的问题，提交前用encode对数据进行编码，后台接收处对数据进行解码。



## 跨域

跨域是指在通过JS不同域之间进行数据传输或通信。只要协议、域名、端口有任何一个不同，都被当作是不同的域。

### CORS(Cross-Origin Resource Sharing, 跨域资源共享)

CORS的基本思想是使用自定义的HTTP头部让浏览器和服务器进行沟通。

发起请求时添加额外的Origin头部，包含和请求页面的源信息，服务器根据这个头部信息决定是否给予响应，就在Access-Control-Allow-Origin头部中返回相同的源信息。如：
```
Origin：http://www.abc.com
Access-Control-Allow-Origin: http://www.abc.com
```
注意，请求和响应都不包含cookie信息。

### JSONP

通过script标签引入的js是不受同源策略的限制的，其实凡是带有src这个属性的标签都拥有跨域的能力，如script、img、iframe。

JSONP是使用script元素获取JSON数据包装在一个回调函数中操作。

```
function handle(response){
	console.log(response.data);
}

var script = document.createElement('script');
script.src = 'http://abc.com/json/?callback=handle';
document.body.insertBefore(script, document.body.firstChild);
```
JSONP的缺点：无法确定其他域是否安全，无法确定JSONP请求是否失败。JSONP只支持GET请求，CORS支持所有类型的HTTP请求。

JSONP的优势：在于支持老式浏览器，以及可以向不支持CORS的网站请求数据。

### document.domain

比如两个页面地址分别为`x.abc.com`和`y.abc.com`，明显不同域，可以将两个页面的`document.domain`设为相同的域名`abc.com`，而且也只能设置为更高一级的父域，且**主域必须相同**。

不同域之间能够获得彼此的window对象，但不能获取window的属性和方法。通过修改两个页面的document.domain，就可以在父页面中操作它里面的iframe的window对象(contentWindow)。

修改document.domain的方法只适用于不同子域的框架间的交互.

### window.name

在每个窗口window的生命周期内，窗口载入的所有页面都是共享一个window.name的，每个页面都对window.name有读写的权限。window.name是持久存在在一个窗口载入过的所有页面中，并不会因为新页面的载入而重置。

```
//a.html
window.name = 'data from a.html';
window.location = 'b.html';

//b.html
console.log(window.name);//'data from a.html'
```

### HTML5 PostMessage

postMessage用于两个窗口iframe之间交换数据。两个窗口能通信的前提是：一个窗口以iframe的形式存在于另一个窗口中(MessageEvent.source获取源窗口)，或者一个窗口是从另一个窗口通过window.open()或超链接形式打开的(同样可以用window.opener获取源窗口)。也就是说要想交换数据，必须能获取目标窗口的引用。

```
// origin1
window.frames[0].postMessage(message, origin2URL);
window.onmessage = function(e){
	console.log(e.data);// MessageEvent
}
// origin2: iframe in origin1
window.onmessage = function(e){
	if(e.source != window.parent) return;// 判断数据发送方
	console.log(e.data);
	e.source.postMessage(message, e.origin);
}
```

[demo看这里](https://github.com/kad0108/Html5/tree/gh-pages/postMessage)

> 如果需要传输的数据很大，用HTTP GET请求肯定不行，而且需要兼容浏览器，应该怎么跨域？

使用POST请求，通过表单提交，action指向post请求的地方，提交表单会马上刷新页面，target可以指定刷新页面，写个iframe让target指向这个iframe。

## 前端安全

### XSS(Cross Site Scripting)

XSS跨站脚本攻击：攻击者向web页面插入恶意脚本代码`<script></script>`来攻击用户。

XSS防御：对输入(和URL参数)进行过滤，对输出进行html编码。特殊字符过滤，如：`<`,`>`,`&`,JS伪协议。

### CSRF(Cross Site Request Forgery)

CSRF跨站请求伪造：通过伪造来自受信任用户的请求来利用受信任的网站。

**CSRF原理：**

通过例子来说明，用户访问并登录银行网站，用户在未登出银行网站的情况下访问了危险网站(可以通过广告等诱使用户访问)，用户在访问这个危险网站同时，向银行发起一个转账请求，这个请求会携带着用户的cookie走了。这个请求因为是用户发起的并且携带了认证信息，所以请求会成功响应。所以，CSRF攻击是hacker借助受害者的cookie骗取服务器的信任，但hacker是打死都拿不到也无法读取cookie的，而且服务器返回的结果由于浏览器同源策略的限制，hacker也无法拿到。

**CSRF防御：**

1. 检查HTTP头部Referer(从哪个页面链接过来)

2. 检查token(服务器不存储用户登录状态，只需验证token是否合法)

3. 通过验证码来检测用户提交

4. 使用定制的HTTP报头

5. 严格设置cookie的域

## 小结：

Ajax是无需刷新页面就能够从服务器取得数据的一种方法，核心对象是XMLHttpRequest对象。

同源策略是对XHR的一个主要约束，它为通信设置了"相同的域、相同的端口、相同的协议"这一限制。试图访问上述限制之外的资源都会引发安全错误，除非采用被认可的跨域解决方案，这个方案叫做CORS。

Comet是对Ajax的扩展，让服务器能够实时向客户端推送数据。实现Comet的手段主要有：长轮询和HTTP流。

WebSockets是一种与服务器进行全双工、双向通信的信道。与其他方案不同，websockets不使用HTTP协议，而使用自定义的协议，专门为快速传输小数据设计。

JS跨域请求方式有：CORS、JSONP、修改`document.domain`跨子域、`window.name`、`HTML5 window.postMessage`.

## Reference

[XMLHttpRequest Level 2 使用指南](http://www.ruanyifeng.com/blog/2012/09/xmlhttprequest_level_2.html)
[Using XMLHttpRequest](https://developer.mozilla.org/cn/docs/Web/API/XMLHttpRequest/Using_XMLHttpRequest)
[jQuert.ajax()](http://www.jquery123.com/jQuery.ajax/)
[HTTP访问控制(CORS)](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS)
[详解js跨域问题](https://segmentfault.com/a/1190000000718840)
[HTML5 PostMessage](http://www.cnblogs.com/zichi/p/4638096.html)
[CSRF 攻击](https://www.ibm.com/developerworks/cn/web/1102_niugang_csrf/)