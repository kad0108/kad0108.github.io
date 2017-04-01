---
title: 隐藏页面元素的几种方法
date: 2017-01-28 00:22:53
categories: 
- Front-End
tags:
- CSS
---

### 1. opacity

从视觉上隐藏元素，但元素依然占位，响应用户交互。

```
opacity: 0;
```

### 2. position

将元素移出可视区域，不影响页面布局，还可以与用户交互。应用：用Dom模拟复选框和单选框，使用这个方法来隐藏真正的checkbox和radio元素来接收焦点切换。

<!-- more -->

```
position: absolute;
top: -1000px;
left: -1000px;
```

### 3. clip-path

clip-path可以裁剪以隐藏元素，对元素添加下面属性即可隐藏，元素依然占位。

```
clip-path: polygon(0 0, 0 0, 0 0, 0 0);
```

### 4. visibility

隐藏元素占据空间，显示元素设置为visible，只触发repaint，不响应用户交互。

```
visibility: hidden;
```

### 5. display

隐藏元素不占据空间，显示时浏览器需要根据样式定义来计算元素应该出现的位置，触发reflow(有时处于性能考虑尽量减少触发reflow)

```
display: none
```

### 6. transform

元素所在位置会被保留，该用法对行内元素不作用。有兼容性问题。

```
transform: scale(0);
```

### 7. hidden

HTML5提供的属性，和`display:none;`效果相同。

```
<div hidden></div>
```

### IE下隐藏元素

```
filter:alpha(opacity=0);
```

