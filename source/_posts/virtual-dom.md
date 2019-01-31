---
title: Virtual DOM
date: 2017-07-23 21:42:13
categories: 
- Front-End
tags:
- JavaScript
---


DOM操作是JS最基础的部分，前段时间重构的一个项目中使用JQuery来操作大量的DOM节点，就算是操作相同的节点也直接使用`$`去直接获取元素，JQuery是可以很方便的操作DOM，但是其底层封装的实现也依然是原生JS中提供的获取元素的API。现在项目里用到的knockoutJS，算是MVVM的鼻祖，虽说实现了数据绑定渲染，但其底层没有用到虚拟DOM，也就还是应该用了原生JS获取DOM元素的API。

## 前端应用状态管理

记得最开始做IFE的任务中有个题目是让实现一个排序表格，附上当时写的[代码](https://github.com/BaiduGo3/task03/blob/gh-pages/task03_038/kad/kad.js)，每次点击排序按钮，都要重新渲染整个table中的元素。DOM操作有多耗性能？看下图：

<!-- more -->

![dom](dom.png)

可以看到div元素的属性的庞大，所以每次的dom操作都会触发repaint、reflow，性能可想而知。

其实上面的问题就是状态改变时需要更新相应的dom元素，那么是不是可以实现让视图和状态进行绑定，状态变更视图自动变更，这也就是MVVM模式数据双向绑定。Virtual DOM解决的就是**维护状态，更新视图**。

## Virtual DOM

用原生JS对象可以很容易表示DOM节点。

```
var ele = {
	tagName: 'ul', // 节点名
	props: { //  dom属性
		id: 'list'
	},
	childs: [ // 子节点
		{tagName: 'li', props: {class: 'item'}, childs: ["Item1"]},
		{tagName: 'li', props: {class: 'item'}, childs: ["Item2"]},
		{tagName: 'li', props: {class: 'item'}, childs: ["Item3"]},
	]
}
```

用上面的JS对象可以表示如下的DOM结构

```
<ul id='list'>
  <li class='item'>Item 1</li>
  <li class='item'>Item 2</li>
  <li class='item'>Item 3</li>
</ul>
```

那么状态变更->渲染视图就可以通过这种方式来实现：用JS对象表示DOM信息结构，当状态变更时，重新渲染整个JS对象结构，然后用新渲染的对象树和旧的树进行对比，纪录两棵树的差异，然后将差异应用到真正的DOM树上。

这就是Virtual DOM的整体思想：

1. 用JS对象模拟DOM树

2. 比较两棵虚拟DOM树的差异

3. 把差异应用到真正的DOM树上

### 步骤一：用JS对象模拟DOM树

```
function Element(tagName, props, childs) {
	this.tagName = tagName;
	this.props = props;
	this.childs = childs;
}
module.exports = function (tagName, props, children) {
	return new Element(tagName, props, children)
}
```

上面的DOM结构就可以表示为：

```
var ele = require('./element')

var ul = el('ul', {id: 'list'}, [
	ele('li', {class: 'item'}, ['Item 1']),
	ele('li', {class: 'item'}, ['Item 2']),
	ele('li', {class: 'item'}, ['Item 3'])
])
```

需要根据JS对象ul构件真正的`<ul>`

```
Element.prototype.render = function(){
	var ele = document.createElement(this.tagName);
	var props = this.props

	for (var propName in props) { 
		var propValue = props[propName]
		ele.setAttribute(propName, propValue)
	}

	var childs = this.childs || [];
	childs.forEach(function(child){
		var childEle = (child instanceof Element)
	      ? child.render() // 如果子节点也是虚拟DOM，递归构建DOM节点
	      : document.createTextNode(child); // 如果字符串，只构建文本节点
	    ele.appendChild(childEle);
	})

	return ele;
}
```

render方法会根据tagName构建一个真正的DOM节点，然后设置这个节点的属性，最后递归地把自己的子节点也构建起来。

```
var ulRoot = ul.render()
document.body.appendChild(ulRoot)
```

### 步骤二：比较两棵虚拟DOM树的差异

比较两棵DOM树的差异是 Virtual DOM 算法最核心的部分，这也就是 Virtual DOM 的 diff 算法。在前端当中，很少会跨越层级地移动DOM元素。**所以 Virtual DOM 只会对同一个层级的元素进行对比，这样算法复杂度就可以达到 O(n)。**

对新旧两棵树进行深度优先遍历，每遍历到一个节点就把该节点和新的的树进行对比。如果有差异的话就记录到一个对象里面。

```
// diff 函数，对比两棵树
function diff (oldTree, newTree) {
	var index = 0; // 当前节点的标志
	var patches = {}; // 用来记录每个节点差异的对象
	dfsWalk(oldTree, newTree, index, patches);
	return patches;
}

// 对两棵树进行深度优先遍历
function dfsWalk (oldNode, newNode, index, patches) {
	// 对比oldNode和newNode的不同，记录下来
	patches[index] = [...];

	diffChilds(oldNode.childs, newNode.childs, index, patches);
}

// 遍历子节点
function diffChilds (oldChilds, newChilds, index, patches) {
	var leftNode = null;
	var currentNodeIndex = index;
	oldChilds.forEach(function (child, i) {
		var newChild = newChilds[i];
		currentNodeIndex = (leftNode && leftNode.count) // 计算节点的标识
			  ? currentNodeIndex + leftNode.count + 1
			  : currentNodeIndex + 1;
		dfsWalk(child, newChild, currentNodeIndex, patches) // 深度遍历子节点
		leftNode = child
	})
}
```

上面的ul和新的ul有差异，当前的标记是0，那么：

```
patches[0] = [{difference}, {difference}, ...] // 用数组存储新旧节点的不同
```

同理li1是patches[1]，li1的文本节点是patches[2]类推。

#### 差异类型

对 DOM 操作可能会：

1. 替换掉原来的节点
2. 移动、删除、新增子节点
3. 修改了节点的属性
4. 对于文本节点，文本内容可能会改变

定义节点差异为：

```
var REPLACE = 0
var REORDER = 1
var PROPS = 2
var TEXT = 3
```

对于节点替换，很简单。判断新旧节点的tagName和是不是一样的，如果不一样的说明需要替换掉。

```
patches[0] = [{
  type: REPALCE,
  node: newNode // el('section', props, children)
}]
```

如果给div新增了属性id为container:

```
patches[0] = [{
  type: REPALCE,
  node: newNode // el('section', props, children)
}, {
  type: PROPS,
  props: {
    id: "container"
  }
}]
```

如果是文本节点:

```
patches[2] = [{
  type: TEXT,
  content: "Virtual DOM2"
}]
```

如果是节点重新排序呢？`div ul p`的顺序换成了`ul div p`，应该怎么对比？如果按照同层级进行顺序对比的话，它们都会被替换掉。如ul和div的tagName不同，div会被ul所替代。最终，三个节点都会被替换，这样DOM开销就非常大。

**React给子节点加上唯一标识key，对比的时候，使用key进行对比，这样就能复用老的 DOM 树上的节点。**

### 步骤三：把差异应用到真正的DOM树上

对新DOM树进行深度优先的遍历，遍历的时候从步骤二生成的patches对象中找出当前遍历的节点差异，然后进行 DOM 操作.

```
function patch (node, patches) {
  var walker = {index: 0}
  dfs(node, walker, patches)
}

function dfs (node, walker, patches) {
  var currentPatches = patches[walker.index] // 从patches拿出当前节点的差异

  var len = node.childNodes
    ? node.childNodes.length
    : 0
  for (var i = 0; i < len; i++) { // 深度遍历子节点
    var child = node.childNodes[i]
    walker.index++
    dfs(child, walker, patches)
  }

  if (currentPatches) {
    applyPatches(node, currentPatches) // 根据不同类型的差异对当前节点进行 DOM 操作
  }
}
```

### 总结

Virtual DOM 算法主要是实现上面步骤：`element，diff，patch`。然后就可以实际的进行使用：

```
// 1. 构建虚拟DOM
var tree = el('div', {'id': 'container'}, [
    el('h1', {style: 'color: blue'}, ['simple virtal dom']),
    el('p', ['Hello, virtual-dom']),
    el('ul', [el('li')])
])
// 2. 通过虚拟DOM构建真正的DOM
var root = tree.render()
document.body.appendChild(root)
// 3. 生成新的虚拟DOM
var newTree = el('div', {'id': 'container'}, [
    el('h1', {style: 'color: red'}, ['simple virtal dom']),
    el('p', ['Hello, virtual-dom']),
    el('ul', [el('li'), el('li')])
])
// 4. 比较两棵虚拟DOM树的不同
var patches = diff(tree, newTree)
// 5. 在真正的DOM元素上应用变更
patch(root, patches)
```

## Reference

[深度剖析：如何实现一个 Virtual DOM 算法](https://github.com/livoras/blog/issues/13)

[React 源码剖析系列 － 不可思议的 react diff](https://zhuanlan.zhihu.com/p/20346379?columnSlug=purerender)