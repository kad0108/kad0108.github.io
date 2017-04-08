---
title: 前端性能优化
date: 2017-03-08 10:07:03
categories: 
- Front-End
tags: HTTP缓存
---

前端性能优化，目的是为什么？让用户有更友好的体验，让服务商节省可观的资源。

对优化方向进行分类，直接来看表：

| 优化方向        | 优化手段          |
| ------------- |:-------------:|
| 请求数量      | CSS Sprites，合并脚本和样式表 |
| 请求带宽      | 使用Gzip压缩传输文件，精简JS文件     |
| 缓存利用 | 使用http缓存，使用CDN，ajax，减少DNS查找      |
| 页面结构 | 样式表放顶部，脚本放底部 |
| 代码校验 | 避免CSS表达式，避免重定向 |
| 减少Repaint和Reflow | 设置class，脱离文档流，减少dom操作 |

<!-- more -->

下面具体解释其中一些优化点：

### CDN

Content Delivery Network 即内容分发网络，CDN把**内容分发**到距离用户近的缓存服务器，然后DNS托管(把DNS解析的地址改成CDN提供的)，DNS服务器根据用户IP地址，将域名解析成相应节点的缓存服务器IP地址，实现**用户就近访问**，提高网站渲染速度和性能。

### 减少repaint和reflow

[回流与重绘](http://www.zhangxinxu.com/wordpress/2010/01/%E5%9B%9E%E6%B5%81%E4%B8%8E%E9%87%8D%E7%BB%98%EF%BC%9Acss%E6%80%A7%E8%83%BD%E8%AE%A9javascript%E5%8F%98%E6%85%A2%EF%BC%9F/)

1. 通过设置class的方式，而不是多次修改元素style属性。

2. 有动画效果的元素可以将position属性设为fixed或absolute。

3. 权衡速度的平滑性，函数节流（自己的理解）

4. 减少对DOM元素的查询和修改，查询时可将其赋值给局部变量。

### CSS引用

尽量使用link代替@import，因为link是在页面加载时同时加载，import是等页面加载完再加载。

## 有关缓存

### http缓存

缓存可以减少响应延迟，减少网络带宽消耗。http header中与缓存控制相关的头部有：Cache-Control、Expires、Etag、Last-Modified。

浏览器在第一次第一次请求后，再次请求时：

1. 浏览器会先看header中的Cache-Control和Expires(强缓存)，如果有缓存则直接从本地缓存中获取资源，不和服务器通信，返回200。

2. 如果没有缓存，则携带Etag和Last-Modified(协商缓存)发请求给服务器，如果服务器端没有变化，返回304，告诉浏览器可以从缓存中获取资源。

3. 如果本地没有缓存或者服务器端资源有更新，或者用户强制刷新Ctrl+F5，浏览器会直接从服务器下载最新数据


#### Cache-Control

请求报文的请求头部，控制缓存行为，相对时间。

**max-age**这个值也爱考，max-age>0直接从浏览器缓存中提取，max-age<=0向server发送http请求确认该资源是否有修改，有返回200，无返回304。

#### Expires

响应内容过期时间，是绝对时间。如果max-age和Expires同时存在，**cache-control覆盖expires**。

#### Etag

请求报文的响应头部，响应中资源的校验值，用于标示URL对象是否改变，是与web资源关联的记号(token)。

请求头中**If-Match**和**If-None-Match**用来比较Etag是否一致。

#### Last-Modified

响应中文件在服务器端最后一次修改的时间。

请求头中**If-Modified-Since**询问该时间后文件是否有被修改过。没有变化返回304。

ETag解决了Last-modified无法解决的问题：

* 一些文件修改时间改变了，但是内容没有改变，此时使用Last-modified会认为资源更新了，而ETag不会。
* Last-modified只能精确到秒，如果资源修改非常频繁，在秒以下的时间进行修改，Last-modified无法正确响应。
* 某些服务器不能精确的得到文件的最后修改时间，这样就无法通过最后修改时间判断资源是否更新。

> 有面试题问304缓存的原理，Not Modified，就可以讲讲上面的http缓存了。

### 离线缓存

之前一直以为离线缓存就是指本地存储localStorage...是我孤陋寡闻了。但是！**这个HTML5新特性已被弃用**！

这里还是简要说一下这个技术的思想：开发者可以使用 Application Cache (AppCache) 接口设定浏览器应该缓存的资源并使得离线用户可用。

来分析一下mainfest的优缺点：

优点：
1. 离线运行
2. 加载速度快
3. 减少服务器负载，只下载更新资源

缺点：
1. 更新的资源，需要二次刷新才会被页面采用
2. 不支持增量更新，只要manifest发生变化，所有资源全部重新下载一次
3. 缺乏足够容错机制，当清单中任意资源文件出现加载异常，都会导致整个manifest策略运行异常

全量加载和二次刷新这两个缺点就已经够严重了。再看其优点也不是真的那么好用：

1.离线运行对于普通页面来说，离线运行没什么用；对于webapp来说，这个特性还不错；对于hybird app来说，也没什么用。
2.减少资源请求HTTP协议的Cache-Control和Expires就也能在缓存有效期内，不再发送资源请求
3.可以更新资源manifest是文件被更新后，全量更新缓存。而改用HTTP协议的缓存方案，只需要对资源文件引用的URL做少许变动即可刷新缓存，例如补个时间戳参数

> 面试问题：浏览器的缓存机制

在浏览器端保存数据能够快速读取本地数据加速网页展示，避免重复的网络请求。可以在Chrome的开发者工具的Application中看到浏览器支持的缓存机制。

浏览器缓存机制有：HTTP缓存、WEB SQL、IndexedDB、Cookie、LocalStorage、SessionStorage、Application Cache

![浏览器缓存机制](/cache.png)



## Reference

[浏览器缓存详解](http://blog.csdn.net/eroswang/article/details/8302191)

[Etag](http://www.iteye.com/topic/656658)

[yahoo前端性能优化](http://www.cnblogs.com/lei2007/archive/2013/08/16/3262897.html)

[前端工程与性能优化](https://github.com/fouber/blog/issues/3)

[manifest](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Using_the_application_cache)

[浏览器HTTP协议缓存机制](https://www.cnblogs.com/skynet/archive/2012/11/28/2792503.html)