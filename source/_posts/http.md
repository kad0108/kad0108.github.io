---
title: HTTP协议
date: 2017-02-08 20:46:10
categories: 
- Front-End
tags:
- HTTP
---

从浏览器地址栏的请求链接开始，浏览器通过DNS解析查到域名映射的IP地址，然后浏览器端向此IP地址取得连接，成功连接之后，浏览器端将请求头信息通过HTTP协议向此IP地址所在服务器发起请求，服务器接受到请求后处理发回响应。浏览器从服务器接收到text/html类型的代码，浏览器开始显示此html。

HTTP是互联网上应用最广泛的一种网络协议，也是浏览器和服务器通信时所采用的协议。HTTP是基于TCP传输，**无连接**(每次连接只处理一个请求，响应后断开)，**无状态**(服务器不会保存客户的任何信息)的**应用层**协议。

## HTTP的报文格式

请求报文(从客户端发到服务器)

<!-- more -->

```
<方法><URL><版本>
<首部header>
<主体body>(可选)
```

响应报文(从服务器发给客户端)

```
<版本><状态码><状态信息>
<首部>
<主体>
```

HTTP header字段包括通用头、请求头、响应头、实体头四个部分。

### 请求方法中GET和POST的区别

| GET用于获取资源          | POST用于发送数据           |
| -------------  |:-------------:|
| 通过URL请求传输数据      | 通过HTTP包的包体 |
| 受URL长度限制，只能传递约1024字节  | 数据量大，可达2M |
| 只支持ASCII字符        | 支持标准字符集，可传递中文字符 |
| get请求会缓存     | 不缓存，每次刷新重新发送 |
| url可见，不安全 | 较get安全 |

### POST提交数据的几种数据格式

POST提交的数据必须放在HTTP的body中，服务器端根据http header中的**Content-Type**字段来判断数据是用哪种编码方式编码的，然后进行解码。

#### 先看Form表单的**enctype**属性：

1. 默认编码方式发送前编码所有字符：`application/x-www-form-urlencoded`

2. 不对字符编码，在使用包含文件上传的表单时必须使用该值：`multipart/form-data`

3. 空格转换为+加号，不对特殊字符编码：`text/plain`

#### Ajax以POST请求向服务器提交数据时，可以通过setRequestHeader设置头部属性Content-Type定义发送内容编码属性。

1. `application/json`：json数据

2. `application/xml`：根据xml头指定的编码格式进行编码。还有`text/xml`这种编码方式但是不推荐，因为`text/*`这种MIME类型有一个US-ASCII字符集，`text/xml`会忽略xml头指定的编码格式而默认采用US-ASCII编码。



## 状态码

**1xx：临时响应（表示请求已接收，继续处理）**

**2xx：请求成功**

200 OK

**3xx：重定向**

302 Move temporarily

304 Not Modified

**4xx：客户端请求错误**

401 Unauthorized

403 Forbidden

404 Not Found

**5xx：服务器错误**

500 Internal Server Error

503 Service Unavailable


## HTTPS

与HTTP协议紧密相关的还有HTTPS协议，它通过SSL和HTTP协议构建可进行加密传输、身份认证的网络协议，能防止数据在传输过程中被劫持或篡改。

| HTTP           | HTTPS           |
| -------------  |:-------------:|
| 标准端口80      | 标准端口443 |
| 明文传输，不安全  | 具有安全性的SSL加密传输协议 |
| 无需证书        | 需要CA机构颁发的SSL证书 |
| 工作在应用层     | 工作在传输层？  |


## Reference

[四种常见的 POST 提交数据方式](https://imququ.com/post/four-ways-to-post-data-in-http.html)