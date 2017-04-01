---
title: 关于JS中变量类型的一些知识总结
date: 2017-03-03 14:48:32
top: true
categories: 
- Front-End
tags:
- JavaScript
---

## 变量类型

基本类型：Number、String、Boolean、Null、Undefined

引用类型：Array、Object

ES6新数据类型：Symbol

### 声明变量

1. var、let、const、function

2. 不使用关键字，全局变量

3. window.a = 1，全局变量

<!-- more -->

### 判断变量类型

1. **typeof**

	typeof返回的是类型字符串，它的返回值有6种：

	```
	typeof 3; // "number"
	typeof "abc"; // "string"
	typeof true; // "boolean"
	typeof undefined; // "undefined"
	typeof null; // "object"
	typeof {}; // "object"
	typeof function(){}; // "function"
	```

2. **instanceof**

	检测引用类型是具体什么类型的对象时使用instaceof，本质是检查某个对象的原型链是否包含某个构造函数的prototype属性。instanceof是通过原型链来检查类型的，所以适用于任何object的类型检查。instanceof对基本数据类型不起作用，因为基本数据类型没有原型链。
	```
	[1, 2, 3] instanceof Array // true
	/abc/ instanceof RegExp // true
	({}) instanceof Object // true
	(function(){}) instanceof Function // true
	/*原型*/
	function Animal(){ }
	(new Animal) instanceof Animal     // true
	```

3. **constructor**
	
	constructor属性返回一个指向创建了该对象原型的函数引用(即函数本身)。与instanceof不同的是，在访问基本类型的constructor时，JS会自动调用其构造函数来生成一个对象。但是无法判断null和undefined。
	```
	(3).constructor === Number // true
	true.constructor === Boolean // true
	'abc'.constructor === String // true
	[1,2,3].constructor === Array // true
	(function(){}).constructor === Function // true
	null.constructor         // TypeError!
	undefined.constructor    // TypeError!
	```

4. **Object.prototype.toString**
	
	toString会将当前对象转换成字符串输出。可以检查所有ECMA标准中内置的所有类型，但是无法检测用户自定义类型。
	```
	toString = Object.prototype.toString;
	toString.call(new Date);    // [object Date]
	toString.call('abc');  // [object String]
	toString.call(3);           // [object Number]
	toString.call([]);          // [object Array]
	toString.call({});          // [object Object]
	toString.call(undefined);   // [object Undefined]
	toString.call(null);        // [object Null]
	/*无法检测自定义类型*/
	function Animal(){};
	var ani = new Animal();
	toString.call(ani);        // [object Object]
	```

### 基本类型这里有点需要注意：

当基本字符串需要调用一个字符串对象才有的方法时，js会自动将基本字符串转化为字符串对象并且调用相应的方法。

ECMAScript中字符串是不可变的，一旦创建，值就不能改变。

看控制台的测试：
```
var str = 'kad';
str[0] // 'k'
str[0] = 'a';
str // 'kad' 
```

咦？那为啥字符串拼接就可以修改啊
```
var str = 'k';
str += 'ad';
str // 'kad'
```
拼接过程实际是：创建一个新字符串，填充'k'和'ad'，然后销毁原来的字符串'k'和'ad'.在旧版本的浏览器中，拼接字符串速度很慢。

记得剑指offer上说提高时间效率中，就有不要多次用String的＋运算符来拼接字符串，因为会产生很多String的临时实例，造成空间和时间的浪费，更好的办法就是用StringBuffer的append方法来完成字符串的拼接。

那么JS中，现代浏览器已经解决了＋运算符拼接低效的问题(怎么解决的啊。。。)，还可以通过数组来实现字符串拼接：

```
var arr = ['k'];
arr.push('ad');
arr.join('');// 'kad'
```

而且通过new String('kad')这种方式创建的字符串，虽然类型是object，但是依然不可改变值。`Object.getOwnPropertyDescriptor(str, 0)`可以输出Object的属性类型，发现writable为false。



## 基础类型和引用类型的存储：

基本类型就是保存在栈内存中的简单数据段，在内存中占有固定大小的空间，他们的值保存在栈空间中，按值访问。

而引用类型的对象，栈内存中存放该对象在堆内存中的访问地址（内存地址大小固定），由堆内存分配空间。当查询引用类型的变量时，先从栈中读取内存地址，然后通过地址找到堆中的值，也就是按引用访问。

### 为什么会有栈内存和堆内存之分？

与垃圾回收机制有关，为了使程序运行时占用的内存最小。

每个函数执行时都会建立自己的内存栈，调用结束就释放内存。堆内存中的对象不会随着调用的结束而销毁，只有当一个对象没有任何引用变量引用时，系统的垃圾回收机制才会回收它。

### 内存释放

引用类型是在没有引用之后, 通过 v8 的 GC 自动回收, 值类型如果是处于闭包的情况下, 要等闭包没有引用才会被 GC 回收, 非闭包的情况下等待 v8 的新生代 (new space) 切换的时候回收。

垃圾收集机制的原理：找出那些不再继续使用的变量，然后释放其占用的内存。垃圾收集器会按照固定的时间间隔周期性地执行这一操作。

JS中最常用的垃圾收集方式是标记清除。销毁带标记的值并回收所占用的内存空间。

还有一种垃圾收集策略叫引用计数，跟踪记录每个值被引用的次数。但无法解决循环引用的问题。

> 有关闭包的内存问题

闭包是否一定会造成内存泄漏？不一定。

闭包在使用之后没有将其引用设为null，GC就会认为这部分资源还在使用，所以GC就不会对其进行回收。由于闭包的作用域链可能会很长，所以GC会认为这条作用域链还有可能会再次使用，所以整个链的资源都不会回收，这种情况引起的内存泄漏比不清除引用更严重。

```
var sG = 'Global Scope'; // 全局变量资源
function A(){
	var sA = 'Scope A'; // 作用域链A中的资源
	return function B(){
		var sB = 'Scope B'; // 作用域链B中的资源
		return function C(){
			var sC = 'Scope C'; // 作用域链C中的资源
			return function Closure(){
				console.log(sG); // 查找作用域链，对全局变量进行使用
				console.log(sA); // 查找作用域链，对作用域链A中的sA进行使用
				console.log(sB); // 查找作用域链，对作用域链B中的sB进行使用
				console.log(sC); // 查找作用域链，对作用域链C中的sC进行使用
			}
		}
	}
}

var clo = A()()(); // 返回一个函数名为Closure的闭包
clo(); // 使用闭包
clo = null; // 如果不对其进行null处理，那这个闭包会一直包涵作用域链A、B、C，作用域中的资源sA、sB、sC就不会背GC回收。
```

闭包引起的内存泄漏可以通过这个将变量设为null来解除对对象的引用，确保正常回收其占用的内存。

> 面试题：局部变量与全局变量的存储

全局变量存放在静态区(static)，程序运行开始时为其分配内存，程序运行结束后该内存才被释放。

局部变量存储在栈(stack)中，随函数调用被申请和释放。


## 函数的参数传递

这个问题上，网上的说法不一，以红宝书的标准来: **JS中所有函数的参数都是按值传递的**。

在向参数传递基本类型的值时，被传递的值会被复制给一个局部变量，用ECMAScript的概念来说就是arguments对象中的一个元素。在向参数传递引用类型的值时，会把这个值在内存中的地址复制给一个局部变量，因此这个局部变量的变化会反映在函数的外部。



### 证明一下对象是按值传递的（准确的说是call-by-sharing）

```
function setName(obj){
	obj.name = 'kad';
	obj = {name: 'lz'};
}
var person = new Object();
setName(person);
alert(person.name);// 'kad'
```

如果对象是按引用传递的，person应该被修改为指向name为'lz'的对象。实际上，在函数中重写obj时，这个变量引用的就是一个局部对象了，这个局部对象在函数执行完毕后立即被销毁。

那么，**如何将值类型的变量以引用的方式传递：将基础类型包装成对象**。


## 再来说说引用类型的拷贝

这种直接赋值，obj1和obj2指向的是堆内存中的同一个对象
```
var obj1 = new Object();
var obj2 = obj1;
a === b // true
```

### 数组的深拷贝：
1. newArr = oldArr.slice(0, oldArr.length);//第二个参数可省略
2. newArr = oldArr.concat();

### 对象的深拷贝
1. 把对象属性遍历一遍赋给新对象
```
function deepCopy(obj){
	var newObj = {};
	for(key in obj){
		newObj[key] = typeof obj[key] === 'Object' ? deepCopy(obj[key]) : obj[key];
	}
	return newObj;
}
```
2. [Object.create()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/create)，需要传入对象原型

## 关于引用，来看题：

> == 的 === 的区别

> 那么[1] == [1]的结果是什么

应该是false，不要被自己对于 == 和 === 的结论影响然后得出错误的结论。因为是两个不同的对象在比较。

关于==类型转换记住是优先转数字，然后就很好理解true=='true'结果为false了。

![看到一张挺有意思的图，贴在这儿](/compare.png)

> 面试题：JS中判断是否为数组

1. Array.isArray(arr)

2. arr instanceof Array

3. `arr.__proto__.constructor === Array`

> 说到引用，不可不提c++中指针和引用的区别，ps：C语言中没有引用：

1. 指针是一个实体，引用只是个别名

2. 引用只能在定义时初始化一次，之后引用所指向的内容不变；指针可变。

3. 引用不能为空，指针可以为空。

4. sizeof引用拿到的事所指向变量的大小，而sizeof指针拿到的是指针本身(所指向变量的地址)的大小。


## Reference

[call-by-sharing](https://en.wikipedia.org/wiki/Evaluation_strategy#Call_by_sharing)

[判断JS变量类型](http://harttle.com/2015/09/18/js-type-checking.html)

