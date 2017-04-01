---
title: CSS布局模型之浮动模型
date: 2017-01-25 21:04:50
categories: 
- Front-End
tags: 
- CSS
- Float
description: "脱离文档流就不占据空间了吗？"
---

## CSS的基本布局模型：

### Flow Model

流模型是Html元素默认布局

* 块级元素：垂直分布，宽度100%
* 内联元素：水平分布，不可设宽高

<!-- more -->

### Layer Model

层模型有三种形式：

* 绝对定位absolute
* 相对定位relative
* 固定定位fixed

### Float Model

今天的主要学习内容：浮动模型。

去查看了文档，说float这个属性本来是应用于图像，使文字围绕在图像周围。这里就有了疑问，既然一直说float会让元素脱离文档流，那为什么还会让占据空间挤开文字呢？

Q: 脱离文档流就不占据空间了吗？
A: 可以这么说。更准确地一点说，是一个元素脱离文档流（out of normal flow）之后，其他的元素在定位的时候会当做没看见它，两者位置重叠都是可以的。

Q: 脱离文档流是不是指该元素从dom树中脱离？
A: 不是，用浏览器的审查元素就可以看到脱离文档流的元素（例如被float了）依然会出现在dom树里


## 下面是测试时间

### 在正常的flow中是这样的：

```
<div class="father">
	<div class="child1">child one</div>
	<div class="child2">child two</div>
</div>
```

![normal flow](/float/float1.png)

可以看到两个child div是垂直排列

### 在对第一个child加上浮动后：

```
<div class="father">
	<div class="child1 float">child one</div>
	<div class="child2">child two</div>
</div>
```

![float](/float/float2.png)

第一个蓝色的child1 div脱离文档流，跟在后面的第二个橙色的child2 div无视浮动元素去定位，所以它们都顶着父元素的左边和顶部边框，而且浮动元素child1覆盖了child2，但是很奇怪的是child2中的文字却看到了这个浮动元素，飘到了child1的边上。**这就是float的特性：其它盒子会无视浮动元素，但其他盒子内的文本仍然会为这个元素让出位置，环绕在其周围。**

### 那么将child1改为绝对定位呢？

![absolute](/float/float3.png)

发现child2盒子以及它的文本内容都无视了child1的存在。这是脱离文档流时float和absolute的区别。

## 总结

**脱离文档流，就是将元素从普通的布局中拿走，其他盒子在定位时会无视脱离文档流的元素进行定位。float布局会其他盒子内的文本依然会为这个元素让出位置，环绕在其周围。浮动元素会生成一个块级框，也就是display:block;而不论它本身是何种元素。而absolute定位则其他盒子及其内容都会无视它。**

## float的缺点 & 清除浮动

当一个父元素包含了多个浮动子元素时，子元素设置了宽高，父元素的宽度不会受影响，但是**高度会塌陷**，解决办法清除浮动。

清除浮动的本质就是**触发元素的BFC**，让容器中的子元素不会影响到外面的元素。触发元素BFC的情况看[这篇博文](http://kadong.space/2017/03/02/bfc/)总结。

### 清除浮动的几种方法

* 在父元素上使用“:before”和“:after”两个伪元素。使用这些伪元素，我们可以在浮动元素的父容器前面和后面创建隐藏元素。“:before”伪元素是用来防止子元素顶部的外边距塌陷，使用“display: table”创建一个匿名的“table-cell”元素。这也确保在IE6和IE7下具有一致性。“:after”伪元素是用来防止子元素的底部的外边距塌陷，以及用来清除元素的浮动。

```
.father:before, .father:after {
	content:"";
	display:block;
	clear:both;
}

<div class="father"></div>
```

* 在容器的结束标签前添加一个空标签，在空标签上直接设置样式“clear:both”。用这种方法来清除浮动

```
.clearfix {
	clear:both;
	zoom:1; /* For IE 6/7 (trigger hasLayout) */
}
<div class="father"></div>
<div class="clearfix"></div>
```


## Reference

1. [脱离文档流](https://www.zhihu.com/question/24529373/answer/29135021)

2. [清除浮动](http://www.w3cplus.com/css/advanced-html-css-lesson2-detailed-css-positioning.html)
