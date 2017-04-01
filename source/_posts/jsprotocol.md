---
title: JS伪协议
date: 2017-02-05 19:38:06
categories: 
- Front-End
tags:
- JavaScript
---

总能看到网页上一些a标签的链接是`javascript:;`，点击之后页面并没有跳转。下面详细说明：

## 伪协议

伪协议不同于因特网上所真实存在的协议，如`http://，https://，ftp://`，伪协议是为关联应用程序而使用的，如:`tencent://(关联QQ)`，`mailto:kad108@foxmail.com(关联电脑中Email软件)`，还有就是`javascript:;`。


`javascript:`这个特殊的协议类型声明了URL的主体是javascript代码，由javascript解释器执行。在浏览器打开`javascript:URL`的时候，它会先运行URL中的代码，当返回值不为undefined的时候，前页链接会替换为这段代码的返回值。可以在浏览器地址栏输入以下URL查看效果：

```
javascript:var now = new Date(); "<h1>The time is:</h1>" + now;
```
<!-- more -->

## javascript:;与javascript:void(0)

`javascript:`是表示在触发`<a>`默认动作时，执行一段JavaScript代码，而 `javascript:;`表示什么都不执行，这样点击a标签时就没有任何反应。

`javascript:void(0)`, 其中[void运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/void)会对给定的表达式进行求值，然后直接返回 undefined。`javascript:;`也是返回undefined，所以两种方法是等价的。

## href的值为javascript: 与 click 的区别

1. 定义的scope不同，前者是global，后者可以是任何作用域

2. 执行的context不同，前者是window，后者是a元素本身

3. 执行的时机不同，总是click事件先执行，href="javascript:..."后执行，所以，只能在click事件里阻止默认行为的发生，却不能在href里阻止事件的执行。


## 再看看a标签的用法

有href属性的`<a>`标签才有`cursor：pointer;`的效果

1. 点击这个链接后，会让页面跳到页面顶部，在location.href后面增加#号。

   ```
   <a href="#"></a>
   ```

2. 如果页面里面有id为ele的元素，会执行锚点机制跳转到这个元素上缘

   ```
   <a href="#ele"></a>
   ```

3. 页面不跳转：三种写法

   ```
   <a href="javascript:;"></a>
   <a href="javascript :void(0);"></a>
   <a href="xxxx" onclick="return false;">
   ```
