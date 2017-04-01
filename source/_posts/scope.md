---
title: Scope and Thread
date: 2017-02-24 18:37:15
categories: 
- Front-End
tags:
- JavaScript
---


有关作用域和执行栈的几道题目，直接来看例子：

### 例子一：
```
for(var i = 0; i < 10; i++){}
console.log(i);
```
输出10，var声明的变量i在全局范围内都有效。

<!-- more -->

### 例子二：
```
for(let i = 0; i < 10; i++){}
console.log(i);
```
会输出ReferenceError，因为let声明的变量i只在let所在代码块内有效。在循环体外引用就会出错。

注意声明了而没有赋值的变量才输出undefined。

### 例子三：
```
for(var i = 0; i < 5; i++){
	setTimeout(()=>{
		console.log("one:", i);
	}, 0);
}
```
输出是**5个5**。

为什么出现这种情况？执行栈

#### 怎么解决？

1. 匿名函数自执行
```
for(var i = 0; i < 5; i++){
	setTimeout((function(){
		console.log(i);
	})(i), 0);
}
```
2. 闭包里返回匿名函数
```
for(var i = 0; i < 5; i++){
	setTimeout((function(i){
		return function(){
			console.log(i);
		}
	})(i), 0);
}
```

上面这两种方法的思想都是要隔离作用域。我们都知道JS在ES6之前是没有块级作用域的，只有函数作用域，通过自执行函数来隔离变量作用域，我自己的理解就是让本次循环的i只在本次循环内作用。

### 例子四：
```
for(let i = 0; i < 5; i++){
	setTimeout(()=>{
		console.log(i);
	}, 0);
}
```
输出是正常的0，1，2，3，4。因为i是let声明的，当前的i只在本轮循环有效，所以每一次循环的i其实都是一个新的变量。

你可能会问，如果每一轮循环的变量i都是重新声明的，那它怎么知道上一轮循环的值，从而计算出本轮循环的值？这是因为 JavaScript 引擎内部会记住上一轮循环的值，初始化本轮的变量i时，就在上一轮循环的基础上进行计算。

### 例子五：promise与setTimeout

```
setTimeout(function(){
	console.log(0);
}, 0);
console.log(1);
new Promise(function executor(resolve){
	console.log(2);
	for(var i = 0; i < 10000; i++){
		i == 6666 && console.log(3);
		i == 9999 && resolve();
	}
	console.log(4);
}).then(function(){
	console.log(5);
});
console.log(6);
```

输出： 1 2 3 4 6 5 0

其中异步事件注册顺序：setTimeout->promise.then

实际优先级关系：promise.then->setTimeout

### 例子六：nextTick和setImmediate

```
setImmediate(function(){
    console.log(1);
},0);
setTimeout(function(){
    console.log(2);
},0);
new Promise(function(resolve){
    console.log(3);
    resolve();
    console.log(4);
}).then(function(){
    console.log(5);
});
console.log(6);
process.nextTick(function(){
    console.log(7);
});
console.log(8);
```

输出： 3 4 6 8 7 5 2 1

优先级关系：process.nextTick->promise.then->setTimeout->setImmediate

### 有关执行任务

任务分两类：microtask、macrotask

macrotask：script整体代码、setTimeout、setInterval、setImmediate、I/O

microtask：process.nextTick、Promises

执行顺序是：先执行一个macrotask，然后执行所有microtask任务，然后再执行macrotask，重复过程.

process.nextTick在当前执行栈尾部，下一次event loop(主线程读取任务队列)之前触发回调函数，setTimeout也是在下一次event loop调用。

### 有关定时器的知识：

```
setInterval(function(){
	
}, 150ms);
```

第二个参数，一直以为是每次执行前需要等待的毫秒数，其实不是。这个参数表示它的代码会在150ms之后被加入到任务队列中。但是不管怎样它的执行都要等到主线程的所有同步任务执行完毕才能执行。

## Reference

[event loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)

[promise的队列和setTimeout的队列](https://www.zhihu.com/question/36972010)


