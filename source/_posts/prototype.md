---
title: JS Prototype
date: 2017-02-20 20:56:26
categories: 
- Front-End
tags:
- JavaScript
---

JS原型是前端面试时总会问到的知识点，这里具体总结一下。

缘于问题：**对JS原型有什么了解，用原型创建对象和普通new一个对象的区别**，下面具体说明创建对象的几种方法。

## 创建对象

### 工厂模式

```
function person(name){
	var o = new Object();
	o.name = name;
	return o;
}

var p = person("kad");
```
<!-- more -->

工厂模式解决了使用Object一个接口创建很多对象产生大量重复代码的问题，但是无法识别对象类型。

### 构造函数模式

```
function Person(name){
	this.name = name;
	this.sayName = function(){alert(this.name);}
}

var person = new Person("kad");
alert(person.constructor == Person); // true
alert(person instanceof Person); // true
```

构造函数以大写字母开头。对象的constructor属性用来识别对象类型。

但是构造函数的每个方法都要在每个实例上重新创建一遍。不同实例上的同名函数不相等。`person1.sayName != person2.sayName`

### 原型模式

```
function Person(){}
Person.prototype.name = "kad";
Person.prototype.sayName = function(){alert(this.name);}
var person = new Person();
```

使用原型对象的好处是可以让所有对象实例共享原型对象所包含的属性和方法。`person1.sayName == person2.sayName`

## 理解原型对象

![原型对象](prototype.png)

从图中可以发现：

```
1. Person.prototype.constructor == Person

2. person.__proto__ == Person.prototype
```

**isPrototypeOf()**用来判断原型对象和实例之间的关系： `Person.prototype.isPrototypeOf(person)`。

**hasOwnProperty()**检测实例自身的属性，会忽略从原型链上继承到的属性。

**delete**操作符可以删除实例属性。

**in**操作符用来判断实例是否含有某个属性，不管是不是本地属性。

### `__proto__`是连接实例与构造函数的原型对象之间。

> **new操作符做了什么**

```
function F(){
	
}

var f = new F();
```

new操作符的心路历程：

1. 新建一个对象 `f = new Object()`

2. 设置对象原型链 `f.__proto__ = F.prototype`

3. 改变构造函数内this的指向，让F中的this指向实例，执行F函数体 `F.call(f)`


## JS继承

### 原型链继承：将父类的实例作为子类的原型

```
function Father(name){
	this.name = name;
}
Father.prototype.sayName = function(){
	return this.name;
}
function Child(){}
Child.prototype = new Person("kad");
var child = new Child();
child.sayName();//'kad'
```

缺点：所有子类共享父类实例，如果某个子类修改了父类，其他子类继承会出问题。在构造子类型实例时不能给父类传参。

### 构造函数继承

```
function Parent(name){
	console.log(this);
	this.name = name;
	this.sayName = function(){
		console.log(this.name);
	}
}
function Child(name){
	this.parent = Parent;//此时Parent中的this指向的是Child，也就是把Parent中的属性和方法都给了Child
	this.parent(name);
	//这里直接delete this.parent也可以
}
var child = new Child("kad");
child.sayName(); //'kad'
console.log(child); 
/**
Child
-name: "kad"
-parent: Parent
-sayName: ()
*/
```

缺点：无法继承父类原型链上的属性和方法。

### call、apply实现继承

```
function Parent(name){
	console.log(this);
	this.name = name;
	this.sayName = function(){
		console.log(this.name);
	}
}
function Child(name){
	Parent.call(this, name);
}
var child = new Child("kad");
child.sayName();//'kad'
console.log(child);
/**
Child
-name: "kad"
sayName: ()
*/
```

> **为什么子类的原型要指向父类的实例，而不直接指向父类的原型呢？**

```
function Person(){
	this.name = 'kad';
}
Person.prototype.sayName = function(){
	console.log(this.name);
}

function Test(){
	this.name = 'test';
}
Test.prototype = Person.prototype;

Test.prototype.tt = function(){

}
```
![看图说话](/prototype3.png)

可以看出，当子类的原型指向父类的原型时，子类就不是继承父类了，因为此时对子类原型的修改会反映到父类上。


## 区别静态、私有、共享属性和方法

```
function Person(){
	this.name = "kad";
	this.privFn = function(){
		console.log("I'm private.");
	}
}
Person.staticFn = function(){
	console.log("I'm static.");
}
Person.prototype.shareFn = function(){
	console.log("I'm public and shareable.");
}
var p = new Person();

function Stu(){
	this.name = "student";
}
Stu.prototype = new Person();
var s = new Stu();
```

![member](/prototype2.png)

区别静态属性/方法、私有属性/方法、共享属性/方法：

1. 静态属性/方法只能通过构造函数直接调用，即Person.staticFn()

2. 私有属性/方法只能通过实例化调用，即p.privFn()

3. 共享属性/方法可以通过原型对象调用，也可以实例化调用，即Person.prototype.shareFn()或p.shareFn()

4. 私有属性/方法和共享属性/方法都可以被子对象继承，而静态属性/方法不可以被继承


## 改变函数内部this指针的指向

总是挺难理解call和apply，来点自己的记忆方法。

> 比如`fn.call(obj)`，obj说：hi，fn，把你的this借我用用（即把fn的this指向obj）。

> 再比如`fn.sayName.call(obj)`，obj说：hi，fn，把你的sayName借我用用（把sayName的this指向obj）。

call方法应用于Function对象，测试了普通{}对象是不可以调用call的。

call返回什么：比如animal.sayname.call(cat);语句返回的是修改了sayName的this，执行cat.sayName()的结果。

call和apply的区别就只是第二个参数啦。bind的返回值是函数。

bind最常用的场景：

```
function Fn(){
	
}
Fn.prototype.click = function(){}
Fn.prototype.init = function(){
	document.addEventListener('click', this.click.bind(this));//也就是在监听事件函数中的this指向了Fn
}
```

**看下面的问题，加深对context上下文和this的理解**

```
var User = {
	count: 1,
	getCount: function (){
		console.log(this);
		return this.count;		
	}
}
console.log(User.getCount());//1
var fn = User.getCount;
console.log(fn());//undefined,此时fn的上下文是window
```

**如何确保fn的上下文始终和User关联?**

正确答案是使用Function.prototype.bind

```
var func = User.getCount.bind(User);  
console.log(func());
```

兼容老浏览器

```
Function.prototype.bind = Function.prototype.bind || function(context){
	var self = this;
	return function(){
		return self.apply(context, arguments);		
	}
}
```
