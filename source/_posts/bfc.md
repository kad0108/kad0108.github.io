---
title: BFC
date: 2017-03-02 22:13:03
categories: 
- Front-End
tags: 
- CSS
---

### 首先明确BFC是什么

BFC(Block Formatting Context)就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。IE中是hasLayout

### 然后什么情况会触发BFC：

1. float的值不为none

2. overflow的值为auto,scroll或hidden

3. display的值为table-cell, table-caption, inline-block中的任何一个

4. position的值不为relative和static

<!-- more -->

### 然后通过例子来看BFC可以用来干嘛：

**1. 防止margin重叠**

垂直的两个块级元素会发生margin重叠：兄弟元素或父子元素。水平方向的margin是不重叠的。

解决：触发下面元素的BFC
```
p{
	width: 200px;
	height: 200px;
	background-color: pink;
	margin: 100px;
}

<p></p>
<p style="float: left;"></p>
或
<p></p>
<p style="position: absolute;"></p>
或
<p></p>
<div style="overflow:hidden;">
	<p></p>
</div>
或
<p></p>
<p style="display:inline-block;"></p>
```

**2. 防止浮动元素的父元素高度塌陷，即清除浮动**

当一个父元素包含了多个浮动子元素时，子元素设置了宽高，父元素的宽度不会受影响，但是高度会塌陷

因为计算BFC的高度时，浮动元素的高度也参与计算，所以解决方案是触发父元素的BFC

```
.child{
	width: 100px;
	height: 100px;
	border: 6px solid purple;
	float: left;
}

<div class="parent" style="overflow:hidden;">
	<div class="child"></div>
	<div class="child"></div>
</div>
或
其他几种BFC情况均可解决
```

**3. 自适应两栏布局**

普通流布局的div和浮动元素做兄弟时是覆盖关系(文字会环绕)，但是将div做BFC化之后，会顺着浮动元素边缘形成自己的封闭的上下文。

调节下面demo中aside的宽度会发现main的宽度在自适应变化

```
.container{
	width: 400px;
	height: 400px;
}
.aside{
	float: left; /**/
	width: 100px;
	height: 100px;
	background-color: pink;
}
.main{
	overflow: hidden; /**/
	background-color: lightgrey;
}

<div class="container">
	<div class="aside"></div>
	<div class="main">我就是为了撑开这个div的</div>
</div>
```

## 参考

[CSS深入理解流体特性和BFC特性下多栏自适应布局](http://www.zhangxinxu.com/wordpress/2015/02/css-deep-understand-flow-bfc-column-two-auto-layout/)

[BFC 神奇背后的原理](http://www.cnblogs.com/lhb25/p/inside-block-formatting-ontext.html)