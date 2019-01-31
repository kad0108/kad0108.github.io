---
title: CSS布局
date: 2017-03-24 21:27:13
categories: 
- Front-End
tags:
- CSS
---


## 水平垂直居中

### 文本的水平垂直居中

`text-align + line-height`

### 盒模型的水平垂直居中

1. 绝对定位 + top:50%;left:50%; + margin-left,margin-top元素宽高的一半

2. 弹性布局 `display:flex; + justify-content:center; + align-items:center;`

3. 表格布局 `display:table;text-align:center;` + `display:table-cell;vertical-align:middle;`

<!-- more -->

## 三栏布局

左右两栏宽度固定，中间一栏自适应宽度。

实现三栏布局的几种方法：

### 最古老的table的布局，三个td搞定

### float布局：middle必须放最后

middle如果放在right前，没有设置浮动，和right不在一个流中会把right挤下去

```
html,body,div{
	height: 100%;
}
.middle{
	margin: 0 200px;
}
.left, .right{
	background-color: purple;
	width: 200px;
}
.left{
	float: left;
}
.right{
	float: right;
}

<div class="left"></div>
<div class="right"></div>
<div class="middle"></div>
```

### absolute布局：middle可以放中间
```
html,body,div{
	height: 100%;
}
.middle{
	margin: 0 200px;
}
.left, .right{
	background-color: purple;
	width: 200px;
	position: absolute;
	top: 0;
}
.left{
	left: 0;
}
.right{
	right: 0;
}

<div class="left"></div>
<div class="middle"></div>
<div class="right"></div>
```

### flex一维布局

父元素设置flex布局，子元素中需要自适应宽度的设置flex:1;，通过order来实现对子元素排列顺序的控制
```
html,body,div{
	height: 100%;
}
.container{
	display: flex;
}
.middle{
	order: 2;
	flex: 1;
}
.left, .right{
	background-color: purple;
	width: 200px;
}
.left{
	order: 1;
}
.right{
	order: 3;
}

<div class="container">
	<div class="left"></div>
	<div class="middle"></div>
	<div class="right"></div>
</div>
```

### grid: 目前仍然在W3C草案中的栅格二维布局，需要确定浏览器支持

```
html,body,div{
	height: 100%;
}
.container{
	display: grid;
	grid-template-columns: 200px auto 200px;
	grid-template-rows: 100%;
}
.middle{
	
}
.left, .right{
	background-color: purple;
	width: 200px;
}
.left{
	
}
.right{
	
}

<div class="container">
	<div class="left"></div>
	<div class="middle"></div>
	<div class="right"></div>
</div>
```

## 两列布局

左栏固定宽度，右栏自适应宽度
```
html,body,div{
	height: 100%;
}
.left{
	width: 200px;
	float: left;
}
.right{
	margin-left: 200px;
}

<div class="left"></div>
<div class="right"></div>
```

也可以触发右栏元素的BFC实现自适应，见{% post_link bfc BFC %}这篇博文。

## Reference

[CSS制作水平垂直居中对齐](http://www.w3cplus.com/css/vertically-center-content-with-css)

[Flex](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html?utm_source=tuicool)

[CSS Grid](http://www.jackpu.com/san-lie-bu-ju-zuo-you-gu-ding-zhong-jian-lie-zi-gua-ying-tan-jiu/)
