---
title: JS自定义事件
date: 2017-08-28 20:08:00
categories: 
- Front-End
tags:
- JavaScript
---


### 绑定事件几种方式：

1. HTML事件处理程序`<input type="button" value="click me" onclick="alert('clicked')"/>`

2. DOM0级事件处理程序`$btn.onclick = function(){}`

3. DOM2级事件处理程序`$btn.addEventListener('click', function(){}, false)`

### 解除绑定：

1. `$btn.onclick = null;`

2. `$btn.removeEventListener()`传入参数和addEventListener参数相同，也就是通过addEventListener添加的匿名函数无法移除。 
     ```
     function handler(){}
     $btn.addEventListener('click', handler, false);
     $btn.removeEventListener('click', handler, false);
     ```

<!-- more -->

### onclick VS addEventListener：

1. 使用addEventListener可以添加多个事件处理程序，都会触发。
   ```
   $btn.addEventListener('click', function(){ console.log(1); }); // 触发
   $btn.addEventListener('click', function(){ console.log(2); }); // 触发
   ```

2. 使用onclick会有覆盖，但是onclick不会覆盖addEventListener
   ```
   $btn.onclick = function(){ console.log(3); }; // 被覆盖
   $btn.onclick = function(){ console.log(3); }; // 触发
   ```
二者回调函数中的参数都是一样的，event一样，this指向也一样。


### addEventListener小知识点

1. addEventListener第三个参数表示事件能否在捕获或冒泡阶段执行。默认false，事件发生在冒泡阶段执行。

2. 阻止同类型事件的触发使用`event.stopImmediatePropagation()`，这个API会阻止事件的冒泡行为以及后序的同类型事件监听函数。

3. blur、focus、load、unload这些事件不会冒泡。

### addEventListener兼容性

```
var EventUtil = {
	addEvent: function(ele, eventName, listener){
		if(ele.addEventListener){
			ele.addEventListener(eventName, listener, false);		
		}else if(ele.attachEvent){
			ele.attachEvent("on" + eventName, listener);
		}else{
			ele["on" + eventName] = listener;
		}
	},
	removeEvent: function(ele, eventName, listener){
		if(ele.removeEventListener){
			ele.removeEventListener(eventName, listener);		
		}else if(ele.detachEvent){
			ele.detachEvent("on" + eventName, listener);
		}else{
			ele["on" + eventName] = null;		
		}
	},
	getEvent: function(e){
		var evt = e || window.event;
		return evt;
	},
	getTarget: function(e){
		var e = this.getEvent(e);
		var target = e.target || e.srcElement;
		return target;
	},
	stopPropagation: function(e){
		var e = this.getEvent(e);
		if(e.stopPropagation) e.stopPropagation();
		else e.cancelBubble = true;		
	},
	preventDefault: function(e){
		var e = this.getEvent(e);
		if(e.preventDefault) e.preventDefault();
		else e.returnValue = false;//兼容firefox
	}
}
```

## JS自定义事件

创建自定义事件可以使用API来实现：

```
var eventObj = document.createEvent("MouseEvents");//创建事件对象
eventObj.initEvent("click", true, false);//初始化事件信息
node.dispatchEvent(eventObj);//将事件绑定到特定dom节点上
```

监听事件，触发事件执行回调函数的行为换种思路可以这样来实现：定义一个数组，push进去事件处理函数；执行时，遍历数组中事件处理函数并执行。

类似于这样的结构：

```
var Event = {
    _listeners: {},
    addEvent: function(type, fn) {
    	// 添加    
    },
    fireEvent: function(type) {
    	// 触发    
    },
    removeEvent: function(type, fn) {
    	// 删除
    }
};
```
使用方式：
```
Event.addEvent("alert", function() {
    alert("弹出！");
});
// 触发自定义alert事件
Event.fireEvent("alert");
```

### 原型模式实现

```
function EventTarget(){
	this._listener = {};
}

EventTarget.prototype = {
	constructor: this,
	addEvent: function(type, fn){
		if(typeof type === "string" && typeof fn === "function"){
			if(typeof this._listener[type] === "undefined"){
				this._listener[type] = [];
			}
			this._listener[type].push(fn);
		}
		return this;
	},
	fireEvent: function(type){
		if(type && this._listener[type]){
			var arrayEvent = this._listener[type];
			if(arrayEvent instanceof Array){
				for(var i = 0; i < arrayEvent.length; i++){
					arrayEvent[i].call(this, {type: type}); // 其实这里还没太明白为什么需要传入type这个参数
				}
			}
		}
		return this;
	},
	removeEvent: function(type, fn){
		console.log(this._listener[type], fn);
		var arrayEvent = this._listener[type];
		if(typeof type === "string" && arrayEvent instanceof Array){
			if(typeof fn === "function"){
				for(var i = 0; i < this._listener[type].length; i++){
					console.log(arrayEvent[i], fn);
					if(arrayEvent[i] === fn){
						this._listener[type].splice(i, 1);
						break;
					}	
				}
			}else{
				delete this._listener[type];
			}
		}
		console.log(this._listener[type]);
		return this;
	}
}
```

需要实现自定义事件功能时候，先new构造下:

```
var myEvent = new EventTarget();

var fn1, fn2;
myEvent.addEvent('alert', fn1 = function(){
	console.log(arguments);
	alert('first alert');
}).addEvent('alert', fn2 = function(){
	alert('second alert');
})
document.onclick = function(){
	myEvent.fireEvent('alert');
}
myEvent.removeEvent('alert', fn1);
```

上面这种设计模式叫做**发布-订阅者模式**。观察者对象，有一个消息容器和三个方法，分别是订阅消息、取消消息、发布消息。


## Reference

[Creating and triggering events](https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Creating_and_triggering_events)

[漫谈js自定义事件、DOM/伪DOM自定义事件](http://www.zhangxinxu.com/wordpress/2012/04/js-dom%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BA%8B%E4%BB%B6/)
