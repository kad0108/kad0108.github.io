---
title: Cookie
date: 2017-02-21 19:52:19
categories: 
- Front-End
tags: 
- HTTP
---

### cookie的产生与使用

browser第一次请求server，server提供web页面，还附带一些键值对，也就是cookie。browser在本地存储cookie，之后每次请求和响应中都会来回发送cookie，仅限4k数据。

cookie与一个域相关联，而且只能发送给这个域。

cookie一般存储的是用户的登陆信息包括用户名、认证信息、登陆时间等，以便于下次免登陆，但一般不会将密码放在cookie中。

<!-- more -->

### HTTP协议是无状态的，为了保持用户会话状态使用什么技术方案弥补？

常用的保持会话状态的方式有session、cookie、URL GET参数：

1. cookie是保存在浏览器终端内存或磁盘上的一小块数据，只能保存字符串类型。所有cookie信息都随着浏览器的请求而发送。cookie分为持久cookie（数据保存在磁盘中）、会话cookie（保存在内存，浏览器关闭后被删除）。浏览器对单域下cookie的个数和每个key的长度都有限制。

2. session是一种服务器端的机制，服务器使用一种类似于散列表的结构来保存信息。客户端需要接收、记忆和回送session的会话标识号，session通常是借助客户端的cookie来纪录会话标识。

3. GET参数是URL中的普通参数，GET方式是明文传输，其传送的数据量会有限制，不同浏览器支持的最大长度略有不同。

要保持用户的会话状态，服务端在客户端第一次访问时自动创建一个session来存储客户端的信息，同时生成一个唯一的key发送给客户端。如果客户端没有禁用cookie，服务器会在客户端的cookie中写入一个key值，用户保存用户信息，之后每一次访问携带cookie给后端，服务器可以根据这个值判断用户的唯一性。如果用户禁用了cookie，则需要将验证信息写入URL中，所有的请求也需要携带这个参数，比如`http://a.com/b.html?session_id=auij123456789`，不携带则客户端会丢失会话状态。



### cookie和session的区别

1. cookie存储在客户端，session存储在服务器端

2. cookie存储**编码**后的字符串，session保存对象

3. session借助cookie才能工作，如果客户端禁用cookie，可以将验证信息写入URL，所有请求携带这个参数

### 浏览器端存储技术localStorage、sessionStorage、cookie区别

cookie是存储在浏览器端，只有4k大小，并且随浏览器的请求一起发送到服务器端的，它有一定的过期时间，到了过期时间自动会消失。

sessionStorage和localeStorage也是存储在客户端的，同属于web Storage，比cookie的存储大小要大有5M，cookie只有4kb，localeStorage是持久化的存储在客户端，如果用户不手动清除的话，不会自动消失，会一直存在，sessionStorage也是存储在客户端，但是它的存活时间是在一个回话期间，只要浏览器的回话关闭了就会自动消失。localStorage在移动端会因为一些原因(比如内存不足等)会被清空。

cookie可以跨域读取，localStorage不允许跨域。读取同一份localStorage必须在相同的协议、主机、端口下。sessionStorage除了要在同域下，还需要在同一浏览器标签页下。

cookie的读取是通过document.cookie，读取到的字符串中存储方式是：`key=val;`。localStorage则直接以键值对的形式存储，值也必须是字符串，有setItem、getItem、removeItem、clear方法。

### cookie可设置属性

domain属性：决定允许访问cookie的域名。

path属性：决定允许访问cookie的路径。同一服务器，URL中有和path相同的路径的页面都可以共享cookie。

expires属性：cookie过期时间。

secure属性：默认为空，使用HTTP传递数据。设置secure则使用HTTPS等安全协议传输。

HttpOnly属性：限制非HTTP方式的访问。

如果想让a.hello.com访问b.hello.com的cookie，可以将domain设为hello.com，并把path设为`/`。

### cookie的弊端

1. 数量和长度限制。

2. 安全性。cookie被拦截后也不需要知道cookie的意义，原样转发就可达到目的。[看这里有关前端安全的总结](http://kadong.space/2017/03/11/ajax/#前端安全)

3. 用户可以禁用cookie，则cookie功能受限。


### 记住密码自动登录怎么实现？

这里是我自己的理解，不一定正确。

登录时勾选了记住密码，则expires选项会附加到登录cookie中。如果未勾选，则不携带expires属性让cookie的寿命仅限本次会话，或者携带设置了一个过去的时间点的expires这样cookie会立刻被删除。

勾选自动登录则再携带一个自定义属性autologin。

### 封装cookie的添加、删除、查询方法

```
var CookieUtil = {
	setCookie: function(key, val, expiredays){
		var exdate = new Date();
		exdate.setDate(exdate.getDate() + expiredays);
		document.cookie = key + "=" + escape(val) + ((expiredays == undefined) ? "" : ";expires=" + exdate.toGMTString());
	},
	getCookie: function(key){
		var cookie = document.cookie;
		var start = cookie.indexOf(key);
		if(start != -1) return null;
		var end = cookie.indexOf(";", start);
		if(end == -1) end = cookie.length;
		return escape(cookie.substring(start+key.length+1, end));
	},
	deleteCookie: function(key){
		var val = this.getCookie(key);
		if(val == null) return null;
		this.setCookie(key, val, 0);//过期时间设为0，浏览器会马上自动删除cookie	
	}
}
```