---
title: 那些天玩过的爬虫
date: 2018-08-27 22:54:29
categories: 
- Front-End
tags: 
- NodeJS
---

> 最近沉迷爬虫无法自拔。。。做到现在也算是有点收获，总结一下。

最开始想要爬阿里系的一个APP，用Charles抓包发现，抓到携带数据的包都是zip，批量解压后分析发现并没有有效的数据。所以结论是，数据传输不是HTTP协议，抓不到包，只能通过[群控](https://mp.weixin.qq.com/s/vaqFkjj5Mg_f3JkZXhzLYQ)来做。

然后想分析一些其它同类的APP，鉴于之前分析移动端APP可能抓不到包的情况，转而去看有没有对应的微信小程序。微信小程序的请求是一定能抓到包的，因为，微信小程序的项目在编译时是将wxml、wxss编译成js来压缩打包为wxapkg文件，微信小程序在微信中的运行其实就是在微信内嵌浏览器（渲染引擎：安卓端X5内核，IOS端Webkit内核）的JSCore（JS引擎）中解压执行JS代码。而浏览器中的请求一定是遵循http协议的（HTTP协议是Web浏览器和服务器之间通信一定遵循的规则）。所以比移动端APP抓到包的概率大很多。

<!-- more -->

<!-- X5内核是渲染引擎，浏览器内核也称为渲染引擎 -->


### 其他方式：

1. 一些移动端App如果开启了debug模式，在手机连接电脑之后可以在`chrome://inspect`中进行`inspect`调试。
2. 尝试了一下类似`selenium`的移动端自动化测试工具`appium`，在App授权的操作上卡死了。



## 有关抓包

先来理解 **代理** ：正常浏览器访问网页时是直接去访问服务器，如果中间通过代理来访问，代理可以帮忙做很多事，比如修改你请求的内容或者将请求的资源映射为本地的资源。

Charles实际上就是一个代理服务器，对手机、电脑等抓包分析。

### Charles抓不到包时，可以查看

1. 是否勾选了电脑系统代理`windows proxy`或`Mac OSX Proxy`

2. 是否关闭了电脑的自动代理`Automatic Proxy Configuration`

3. 是否给手机配置了代理？

4. 是否给电脑或手机安装了信任证书`SSL Proxying > Install Charles Root Certificate `或`https://chls.pro/ssl` 



### 抓包分析了四个小程序，可以很明显地发现对应的请求拦截策略的不同：

1. 最基本的会对爬取频率有限制，那就每个请求[sleep](https://www.reddit.com/r/javascript/comments/5abyi2/is_there_a_way_to_implement_sleep_with_es6/)几秒钟

2. 针对定位的拦截，定位信息包括经纬度`lat,lng`、城市`city_id`、编码串`address_code`

3. 针对设备有关参数的拦截：设备信息包括`User Agent`、编码串`device_id`

4. 针对`token`的拦截：大多抓取到的`token`不会很快过期，伪造方式不明。其中有一个小程序的`token`获取基于微信接口返回的`code`参数，这个请求过期时间在一分钟左右，而微信根据用户信息去获取`code`的请求是抓不到包的。

5. 针对签名串`sign`的拦截：这个参数是基于请求`query`中携带的其他参数加密得到的签名串，同样不知如何伪造。

---

通过真机定位去爬取定位地区的数据，自己对爬虫的分析目前只能做到这个程度，想要进一步实现爬虫自动化，需要考虑的问题列在下面：

* 数据存储方式，与做数据分析的小伙伴如何对接。
* 遇到这种接口变更或者反爬策略变更的情况如何实时应对。
* 每款App请求构造规则都不同，需要每个都写一套爬虫？可复用性如何考虑。
* 各种参数编码串如何伪造。
* 针对定位的不同，不同App可拉取数据方式不同，比如：
  * 一款App是定位到同一城市不同地区可拉取到的数据是一致的，这种形式的数据拉取可以通过一些地图接口批量给不同城市的地址解析得到对应的经纬度信息。
  * 另一款App是定位到同一城市不同地区会有不同的数据来源，针对这种形式的数据获取，可能需要先在地图中搜索到需要的地址，然后伪造对应经纬度信息作为拉取参数。




## Reference

[JavaScriptCore解析](https://cloud.tencent.com/developer/article/1004875)
[Using Charles from an iPhone（可能需要翻墙）](https://www.charlesproxy.com/documentation/faqs/using-charles-from-an-iphone/)
[SSL Certificates （可能需要翻墙）](https://www.charlesproxy.com/documentation/using-charles/ssl-certificates/)