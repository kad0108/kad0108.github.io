---
title: You Don't Know JavaScript II —— This & Object & Prototypes
date: 2017-08-10 23:02:04
categories: 
- Reading Notes
tags:
- JavaScript
---


## 题记

其实很多时候看书都有点心浮气躁，总是看一会儿就被别的事情打断了，最近很难静下心有一个完整的时间段来不思考任何烦心事好好地看书。


# 第二部分 this和对象原型

## 第一章 关于this

### 1.1 为什么要用this

如果不使用this就需要给函数体显式地传入一个上下文对象，this提供了一种更优雅的方式来隐式传递一个对象的引用，让api设计更简洁而且易于复用。

<!-- more -->

```
function bar(context){
  return context.name.toUpperCase();
}
bar({name: 'kad'}); // 'KAD'


VS

function bar(){
  return this.name.toUpperCase();
}
bar.call({name: 'kad'}); // 'KAD'
```


### 1.2 误解

#### 1.2.1 指向自身

❌this指向函数自身。看下面例子
```
function foo(num){
  this.count++;
}
foo.count = 0;
for(var i = 0; i < 10; i++){
  if(i > 5) foo(i);
}
console.log(foo.count); // 0 ???
```

很明显foo被调用了4次，但是`foo.count`的值为0，也就说明this并不指向函数自身。

**在函数内部引用自身**： 具名函数在它内部可以使用函数名来引用自身，匿名函数可以使用`arguments.callee`来引用当前正在运行的函数对象。所以可以在foo函数内修改为`foo.count++`，但这样回避了this问题，完全依赖与变量foo的词法作用域。

实际可以强制this指向foo函数对象：

```
function foo(num){
  this.count++;
}
foo.count = 0;
for(var i = 0; i < 10; i++){
  if(i > 5) foo.call(foo, i); // 使用call确保this指向函数对象foo本身
}
console.log(foo.count); //4
```

#### 1.2.2 它的作用域

❌this指向函数的作用域。

this在任何情况下都不指向函数的词法作用域。作用域“对象”无法通过JS代码访问，它存在于JS引擎内部。

```
function foo(){
  var a = 2;
  this.bar();
}
function bar(){
  console.log(this.a);
}
foo(); // undefined， 如果直接console a就会报错ReferenceError，很奇怪难道this.a不是RHS查询？
```

这段代码尝试使用this连通foo和bar的词法作用域，让bar可以访问foo作用域中的变量a，这是不可能实现的。**你不能使用this来引用一个词法作用域内部的东西。**

### 1.3 this到底是什么

this是在运行时进行绑定的，并不是在编写时绑定的，它的上下文取决于函数调用时的各种条件。this的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式。

当一个函数被调用时，会创建一个活动记录（有时也称为执行上下文）。这个记录会包含函数在哪里被调用（调用栈）、函数的调用方法、传入的参数等信息。this就是记录的其中一个属性，会在函数执行的过程中用到。

### 1.4 小结

this既不指向函数自身也不指向函数的词法作用域。

this实际上是在函数被调用时发生的绑定，它指向什么完全取决于函数在哪里被调用。


## 第2章 this全面解析

### 2.1 调用位置

分析好函数在代码中被调用的位置，this到底引用的是什么，也就是分析调用栈（为了到达当前执行位置所调用的的所有函数）。

查看调用栈可以使用浏览器吊事工具，设置断点或者在代码中插入一条debugger语句。运行代码时，调试器会在那个位置暂停，同时展示当前位置的函数调用列表，这就是调用栈。栈中第二个元素就是真正的调用位置。

![callstack](callstack.png)

### 2.2 绑定规则

在函数的执行过程中调用位置决定this的绑定对象。

#### 2.2.1 默认绑定 

直接使用不带任何修饰的函数引用进行调用，this的默认绑定指向全局对象window。

```
function foo(){
  console.log(this.a);
}
var a = 2;
foo(); // 2
```

foo调用时this指向的是全局对象window。严格模式下会输出undefined，全局对象无法使用默认绑定。

#### 2.2.2 隐式绑定

调用位置是否有上下文对象。隐式绑定规则会把函数调用中的this绑定给这个上下文对象。

```
var a = 1;
function foo(){
	console.log(this.a);
}
var obj = {
  a: 2,
  foo: foo
};
obj.foo(); // 2 -----(1)
var f = obj.foo;
f(); // 1 -----(2)
```

(1)处的foo调用位置会使用obj上下文来引用函数。

(2)处的`obj.foo()`引用的是foo函数本身，所以此时f是不带任何修饰的函数调用，应用了默认绑定，this指向window。如果是严格模式则绑定到undefined上。

```
function foo(){
  console.log(this.a);
}
function doFoo(fn){
  fn();
}
var obj = {
  a: 2,
  foo: foo
};
var a = 1;
dooFoo(obj.foo); // 1
```

上面这样传入回调函数和(2)处一样，都会丢失this绑定。还有像setTimeout这样传入回调函数的丢失this绑定是很常见的。

#### 2.2.3 显式绑定

如果不想在对象内部包含函数引用，而想在某个对象上强制调用函数，可以使用函数的**call和apply**方法。JS中所有的函数包括自己创建的函数都可以调用call和apply方法，因为这两个方法都是函数原型上的方法：`Function.prototype.call`。

call和apply的第一个参数是对象，会在函数调用时强制将this绑定给这个对象。

```
function foo(){
  console.log(this.a);
}
var obj = {a: 2};
foo.call(obj); // 2
```

也可以通过一个辅助绑定函数来显示绑定：

```
function foo(b){
  console.log(this.a, b);
  return this.a + b;
}
function bind(fn, obj){
  return function(){
    return fn.apply(obj, arguments);
  }
}
var obj = {a: 2};
var bar = bind(foo, obj);
var c = bar(3); // 2 3
console.log(c); // 5
```

当然在ES5中也提供了内置的方法`Function.prototype.bind`

```
var bar = foo.bind(obj);
var c = bar(3); // 2 3
console.log(c); // 5
```

硬绑定的bar不能再修改它的this了，让我想到箭头函数也是不能修改this 的。

JS一些内置函数也可以确保回调函数使用指定的this。

```
function foo(){
  console.log(this.val);
}
var obj = {val: 'ok'};
[1,2,3].forEach(foo, obj); // ok*3
```

#### 2.2.4 new绑定

在JS中使用new操作符时被调用的函数才是构造函数调用。

使用new发生构造函数调用时，会自动执行下面的操作：

1. 创建一个全新的对象`var fn = new Object();`
2. 设置对象原型链`fn.__proto__ = F.prototype;`
3. 函数调用的this绑定给新对象`F.call(fn);`
4. 如果函数没有返回其他对象，那么new表达式中的函数调用会自动返回这个新对象

```
function foo(a){
  this.a = a;
}
var bar = new foo(2);
console.log(bar.a); // 2
```

使用new调用foo时，会构造一个新对象并把它绑定到foo调用的this上。new是最后一种可以影响函数调用时的this绑定行为的方法。

### 2.3 优先级

* 默认绑定优先级最低。
* 显式绑定比隐式绑定优先级高。
* new绑定比隐式绑定优先级高。
* new和call/apply无法一起使用。
* polyfill代码主要用于旧浏览器的兼容（原意是刮墙的腻子来抹平差异）。

#### [Curry(柯里化)](https://llh911001.gitbooks.io/mostly-adequate-guide-chinese/content/ch4.html#总结)

先看一个curry的简单例子：

```
var add = function(x){
	return function(y){
		return x + y;
	}
}
var increment = add(1);
increment(2); // 3
```

**curry的概念：只传递给函数一部分参数来调用它，让它返回一个函数去处理剩下的参数。这样的局部调用能够大量减少代码。**

文中提到在new中使用硬绑定函数，目的是预先设置函数的一些参数，这样在使用new进行初始化时就可以只传入其余的参数，bind的功能之一就是可以把除了第一个参数（第一个参数用于绑定this）之外的其他参数都传给下层的函数。看例子：

```
function foo(a,b){
  this.val = a+b;
  console.log(this.val);
}
var bar = foo.bind(null, 'a'); // 使用null是因为在本例中并不关心硬绑定的this是什么，反正new时this会被修改
var baz = new bar('b'); // 'ab'
```

#### 判断this

1. 函数是否在new 中调用（new绑定）？如果是的话this绑定的是新创建的对象。`var bar = new foo();`
2. 函数是否通过call、apply（显式绑定）或者硬绑定调用？如果是的话，this绑定的是指定的对象。`var bar = foo.call(obj) ;`
3. 函数是否在某个上下文对象中调用（隐式绑定）？如果是的话，this绑定的是那个上下文对象。`var bar = obj.foo();`
4. 如果都不是，使用默认绑定，绑定到全局对象。如果在严格模式下，就绑定到undefined。`var bar = foo();`

### 2.4 绑定例外

#### 2.4.1 被忽略的this

当把null或undefined作为this的绑定对象传入call、apply或bind时应用的是默认绑定。

```
function foo(){
  console.log(this.a);
}
var a = 2;
foo.call(null); // 2
```

什么情况下会传入null？

1. 使用apply展开一个数组

   ```
   function foo(a, b){
     console.log(a, b);
   }
   foo.apply(null, [1,2]); // 1 2
   ```

   ES6中有提供扩展运算符`...`可以代替apply来展开数组`foo(...[1,2])`

2. 使用bind进行柯里化

  ```
  var bar = foo.bind(null, 1);
  bar(2); // 1 2
  ```

  如果函数并不关心this的话，使用bind仍需要传入一个占位值，就传入null。


#### 更安全的this

使用null来忽略this绑定可能产生一些副作用，比如使用第三方库中的一个函数中的this如果应用了默认绑定规则会把this绑定到全局对象，会导致不可预计的后果（比如修改全局对象）。

一个更安全的做法是传入一个特殊的空对象`Object.create(null)`，和`{}`（等价于 new Object()）不同的是，前者是没有原型链的，比`{}`更空。

#### 2.4.2 间接引用

```
function foo(){
  console.log(this.a);
}
var a = 1;
var o = {a: 2, foo: foo};
var p = {a: 3};
o.foo(); // 2
(p.foo = o.foo)(); // 1
```

赋值表达式`p.foo = o.foo`的返回值是目标函数的引用，此时调用位置是foo，而不是`p.foo`或`o.foo`，会应用默认绑定。

#### 2.4.3 软绑定

因为使用硬绑定之后无法修改this，软绑定就是实现和硬绑定相同的效果同时保留隐式绑定或显式绑定修改this的能力。

注：然而代码没太看懂，也不太想细钻了。

### 2.5 this词法

ES6中的箭头函数不使用this的上面这几种规则， 而是根据外层（函数或全局）作用域来决定this。

```
function foo(){
	setTimeout(function () {
		console.log(this.a);
	}, 0);
}
var a = 1;
var obj = {a: 2};
foo(); // 1
foo.call(obj); // 1
```

箭头函数最常用于回调函数中：

```
function foo(){
	setTimeout(() => {
		console.log(this.a);
	}, 0);
}
var a = 1;
var obj = {a: 2};
foo(); // 1
foo.call(obj); // 2
```

箭头函数会继承外层函数调用的this绑定， 这和ES6之前代码中的`self = this`机制一样。

### 2.6 小结

如果要判断一个运行中函数的this绑定，就需要找到这个函数的直接调用位置。找到之后就可以顺序应用下面四条规则来判断this 的绑定对象。

1. 由new调用？绑定到新创建的对象。
2. 由call或者apply（或者bind）调用？绑定到指定的对象。
3. 由上下文对象调用？绑定到那个上下文对象。
4. 默认：在严格模式下绑定到undefined，否则绑定到全局对象。

一定注意，有些调用可能在无意中使用默认绑定规则，如果想“更安全”地忽略this绑定，你可以使用一个DMZ对象，比如`Object.create(null)`，以保护全局对象。

ES6中的箭头函数并不会使用四条标准的绑定规则，而是根据当前的词法作用域来决定this，具体来说，箭头函数会继承外层函数调用的this绑定（无论this绑定到什么）。这其实和ES6之前代码中的`self=this`机制一样。



## 第3章 对象

### 3.1 语法

对象定义的两种形式：

```
// 声明形式
var obj = {
  key: val,
  // ...
}

// 构造形式 （不常用）
var obj = new Object();
obj.key = val;
```

 ### 3.2 类型

JS中的六种主要类型：`string,number,boolean,null,undefined,object`.

#### 内置对象

```
String
Number
Boolean
Object
Function
Array
Date
RegExp
Error
```

内置对象不同于基础类型，可以当做构造函数使用。

```
var str1 = "I am a string";
typeof str1; // "string"
str1 instanceof String; // false

var str2 = new String("I am a string");
typeof str2; // "object"
str2 instanceof String; // true

Object.prototype.toString.call(str2); // [object String]
```

str1只是一个字面量，并且是一个不可变的只。如果在字面量上执行一些操作，比如获取长度，访问某个字符等，语言会自动把字符串字面量转换成一个String对象，也就是说你并不需要显式创建一个对象。

null和undefined没有对应的构造形式，只有文字形式。Date只有构造，没有文字形式。

对于Object、Array、Function和RegExp来说，无论哪种形式都是对象。

Error对象很少在代码中显式创建，一般是在抛出异常时自动创建。

### 3.3 内容

存储在对象内部的是这些属性的名称，它们指向值真正的存储位置。

```
var obj = {
  a: 2
};
obj.a; // 2
obj['a']; // 2
```

访问obj中a的值，可以使用`.`操作符（属性访问）或者`[]`操作符（键访问）。两种语法的区别在于：`.`操作符要求属性名满足标识符的命名规范，而`[".."]`语法可以接受任意UTF-8/Unicode字符串作为属性名。

在对象中，属性名一定都是**字符串**。如果你使用string意外的其他值作为属性名，那它会首先被转换为一个字符串。

```
var obj = {};
obj[true] = "foo";
obj['true']; // 'foo'

obj[3] = 'bar';
obj['3']; // 'bar'

obj[obj] = 'baz';
obj["[object Object]"]; // 'baz'
```

#### 3.3.1 可计算属性名

```
var prefix = "foo";
var obj = {
  [prefix + "bar"]: 'hello',
}
obj['foobar']; //hello
```

可计算属性名最常用的场景是ES6的符号（Symbol）。

#### 3.3.2 属性与方法

```
function foo(){
  console.log('foo');
}
var someFoo = foo;
var obj = {
  someFoo: foo
};
foo; // function foo(){..}
someFoo; // function foo(){..}
obj.someFoo; // function foo(){..}
```

someFoo和obj.someFoo只是对于同一个函数的不同引用，不能说明这个函数是属于这个对象。

#### 3.3.3 数组

数组支持`[]`访问形式，期望的是数值下表，也就是值存储的位置（通常被称为索引）是整数，不同于对象的访问。

可以对数组添加属性，访问方式同对象，但是数组的length值并未发生变化。完全可以把数组当作一个普通的键值对象来使用，并且不添加任何数值索引，但这并不是一个好主意。

注意：如果向数组添加的属性看起来像一个数字，它会变成数值下标，会修改数组的内容而不是添加一个属性。

```
var arr = ['a', 'b', 'c'];
arr["3"] = 3;
arr; //["a", "b", "c", 3]
```

#### 3.3.4 复制对象

浅复制：`var newObj = Object.assign({}, oldObj)`，Object.assign就是使用=操作符来赋值。

深复制：`var newObj = JSON.parse(JSON.stringify(oldObj))`。深复制可能会有循环引用的问题。

#### 3.3.5 属性描述符

从ES5开始，所有属性都具备属性描述符。

```
var obj = {a: 2};
Object.getOwnPropertyDescriptor(obj, 'a');
//
{
  value: 2,
  writable: true,
  enumerable: true,
  configurable: true
}
```

创建属性时属性描述符会使用默认值，可以使用`Object.defineProperty(..)`来添加新属性或修改已有属性。

writable设置为不可写后，严格模式下修改对象的属性值会报TypeError，相当于定义了一个空操作setter，被调用时抛出一个TypeError错误。

configurable(可配置)修改为false是单向操作，无法撤销，除了无法修改，还会禁止删除这个属性。delete只用来删除对象的可删除属性，若删除的属性是某个对象/函数的最后一个引用者，则这个未引用的对象可以被垃圾回收。不要把delete看作释放内存的工具。

enumerable(可枚举)设置为false，这个属性就不会出现在for...in循环美剧中，但仍可以正常访问。

#### 3.3.6 不变性

如果希望属性或对象不可改变，可以通过以下方法来实现：

1. 对象常量

   ```
   var obj = {};
   Object.defineProperty(obj, "PROPERTY", {
     value: 2,
     writable: false,
     configurable: false
   })
   ```

2. 禁止扩展

   ```
   var obj = {};
   Object.preventExtensions(obj);
   obj.a = 2;
   obj.a; // undefined
   ```

3. 密封

   ```
   Object.seal(); // 这个方法实际上会调用Object.preventExtensions并把所有现有属性标记为configurable:false
   ```

4. 冻结

   ```
   Object.freeze(obj); // 这个方法会在现有对象上调用Object.seal并把所有数据访问属性标记为writable:false。这个方法是级别最高的不可变性，会禁止对象本身及其任意直接属性的修改，但这个对象引用的其他对象是不受影响的。
   ```

#### 3.3.7 [[Get]]

```
var obj = {a:2};
obj.a; // 2
```

对属性a的访问实际上是实现了get操作，首先在对象中查找是否有名称相同的属性，如果没有找到会遍历可能存在的prototype链，如果都没有找到则返回undefined。

访问属性和访问变量是不一样的，如果引用了一个当前词法作用域中不存在的变量，会抛出一个ReferenceError异常。

#### 3.3.8 [[Put]]

如果已经存在这个属性，[[Put]]算法大致会检查下面的内容：

1. 属性是否是访问描述符，如果是并且存在setter就调用setter。
2. 属性的数据描述符中writable是否是false？如果是，在非严格模式下默认失败，在严格模式下抛出TypeError异常。
3. 如果都不是，将该值设置为属性的值。

如果对象中不存在这个属性，[[Put]]操作会更加复杂。涉及到后面的[[Prototype]]。

#### 3.3.9 Getter和Setter

对象默认的[[Put]]和[[Get]]操作分别可以控制属性值的设置和获取。

```
var obj = {};
Object.defineProperty(obj, 'a', {
  get: function(){
    return 4;
  },
  enumerable: true
});
obj.a; // 4
```

Cannot both specify accessors and a value or writable attribute.

对象文字语法中的`get a() {}`和`defintProperty()`显示定义等价。

```
var obj = {
  get a(){
    return this._a;
  },
  set a(val){
    this._a = val;
  }
}
```

#### 3.3.10 存在性

判断对象中是否存在这个属性:

1. `("a" in obj)`             in操作符会检查属性是否在对象及其[[Prototype]]原型链中。 
2. `obj.hasOwnProperty("a");`      hasOwnProperty只会检查属性是否在对象中。

所有普通对象都是通过Object.prototype的委托来访问hasOwnProperty，但是有的对象可能没有连接到Object.prototype（通过Object.create(null)来创建）。这种情况下，obj.hasOwnProperty就会失败。可以通过`Object.prototype.call(obj, 'a')`来判断。

**注意**：`4 in [2,4,6]`的结果并不是期待的true，因为这个数组中包含的属性名是0,1,2，这是in操作符检查属性时对象与数组的区别。

`Object.keys()`返回数组，包含所有可枚举属性。`Object.getOwnPropertyNames()`返回数组包含所有属性，无论是否可枚举。但这两种方法都只会查找对象直接包含的属性。

### 3.4 遍历

ES5中增加了一些数组的辅助迭代器，包括forEach、every、some，有意思的是forEach中不能return和break，一定会全部执行完，而every和some都类似于for循环中break会提前终止遍历。

遍历数组是按下标顺序，遍历对象属性的顺序是不确定的，不同JS引擎可能不一样。

ES6中增加了`for..of`来遍历数组的循环语法，如果对象本身定义了迭代器也可以遍历对象。

```
var arr = [1,2,3];
for(var v of arr){
  console.log(v);
}
// for..of循环首先会向被访问对象请求一个迭代器对象，然后通过调用迭代器对象的next方法来遍历所有返回值，数组有内置的@@iterator，可以使用内置的@@iterator来手动遍历数组
var arr = [1,2,3];
var it = arr[Symbol.iterator]();
it.next(); // {value: 1, done: false}
it.next(); // {value: 2, done: false}
it.next(); // {value: 3, done: false}
it.next(); // {done: true}
```

普通对象没有内置的@@iterator，无法自动完成for..of遍历。可以自定义：

```
var obj = {a: 2, b: 3};
Object.defineProperty(obj, Symbol.iterator, {
  enumerable: false, // 定义自己的@@iterator主要是不可枚举
  writable: false,
  configurable: true,
  value: function(){
    var o = this;
    var idx = 0;
    var ks = Object.keys(o);
    return {
      next: function(){
        return {
          value: o[ks[idx++]],
          done: (idx > ks.length)
        }
      }
    }
  }
})
// 手动遍历
var it = obj[Symbol.iterator]();
it.next(); // {value: 2, done: false}
it.next(); // {value: 3, done: false}
it.next(); // {value: undefined, done: true}
// 用for..of遍历
for(var v of obj){
	console.log(v)
}
// 2
// 3
```

### 3.5 小结

JS中的对象有字面形式（比如var a = {...}）和构造形式（比如var a = new Array(..)）。字面形式更常用，不过有时候构造形式可以提供更多选项。

许多人都认为“JS中万物都是对象”，这是错误的。对象是6个（或者7个，取决于你的观点）基础类型之一。对象包括function在内的子类型，不同子类型具有不同的行为，比如内部标签[object Array]表示这是对象的子类型数组。

对象就是键值对的集合。可以通过`.propName`或者`["propName"]`语法来获取属性值。访问属性时，引擎实际上会调用内部的默认[[Get]]操作（在设置属性值时是[[Put]]），[[Get]]操作会检查对象本身是否包含这个属性，如果没找到还会继续查找[[Prototype]]链。

属性的特性可以通过属性描述符来控制，比如writable和configurable。此外，可以使用`Object.preventExtensions(..)`、`Object.seal(..)`和`Object.freeze(..)`来设置对象（及其属性）的不可变性级别。

属性不一定包含值，他们可能是具备getter/setter的访问描述符，此外，属性可以是可枚举或者不可枚举的，这决定了他们是否会出现在for..in循环中。

你可以使用ES6的for..of语法来遍历数据结构（数组、对象等等），for..of会寻找内置或自定义的@@iterator对象并调用他的next方法来遍历数据值。



## 第四章 混合对象“类”

### 4.5 小结

类是一种设计模式。许多语言提供了对于面向类软件设计的原生语法。JS中也有类似的语法，但是和其他语言中的类完全不同。

类意味着复制。

传统的类被实例化时，他的行为会被复制到实例中。类被继承时，行为也会被复制到子类中。

多态（可以把子类的实例赋给父类）看起来似乎是从子类引用父类，但是本质上引用的其实是复制的结果。

```
#include <iostream>
using namespace std;

struct A
{
    virtual void f()
    {
        cout << "A" << std::endl;
    }
};
struct B: public A
{
    void f()
    {
        cout << "B" << std::endl;
    }
};
void test(A* p)
{
    p->f();
}

int main()
{
    B b;
    test(&b); // B
    return 0;
}
```

JS并不会像类那样自动创建对象的复本。

混入模式一节没有太看，是为了描述在JS中模拟类得不偿失，会带来很多隐患。




## 第五章 原型

### 5.1 [[Prototype]]

JS中的对象有一个特殊的`[[Prototype]]`内置属性，其实是对于其他对象的引用。几乎所有的对象在创建时`[[Prototype]]`属性都会被赋予一个非空的值。

在第3章中提到，当试图引用对象的属性时会触发`[[Get]]`操作，对于默认的`[[Get]]`操作来说，第一步是检查对象本身是否有这个属性，有的话使用它，没有的话就需要使用对象的`[[Prototype]]`链。

```
var anotherObj = {
  a: 2
};
var obj = Object.create(anotherObj);
obj.a; // 2
```

`Object.create()`会创建一个对象并把这个对象的`[[Prototype]]`关联到指定的对象。

`[[Get]]`操作会持续找到匹配的属性名或者查找完整条`[[Prototype]]`链返回undefined。

使用`for...in`遍历对象时原理和查找`[[Prototype]]`链类似，任何可以通过原型链访问到（并且是enumerable）的属性都会被枚举。

使用in操作符来检查属性在对象中是否存在时，同样会查找对象的整条原型链（无论属性是否可枚举）。

#### 5.1.1 Object.prototype

到哪里是`[[Prototype]]`的尽头呢？

所有普通的`[[Prototype]]`链最终都会指向内置的Obejct.prototype。

```
对象原型链的终点：
Object.getPrototypeOf(Object.prototype); // null
```

由于所有的普通对象都源于这个Object.prototype对象，它包含JS中很多通用功能，比如`.toString(), .valueOf(), hasOwnProperty(), isPrototypeOf()`。

#### 5.1.2 属性设置和屏蔽

```
myObj.foo = "bar";
```

如果myObj对象中包含名为foo的普通数据访问属性，这条赋值语句就会修改已有的属性值。

如果foo不是直接存在于myObj中，`[[Prototype]]`链就会被遍历，类似`[[Get]]`操作。如果原型链上找不到foo，foo就会被直接添加到myObj上。

如果foo存在于原型链上层，会出现三种情况：

1. 如果在`[[Prototype]]`链上层存在名为foo的普通数据访问属性，没有被标记为只读，也就是`(writable:true)`，就会直接在myObj中添加一个名为foo的新属性，它是**屏蔽属性**。

2. 如果在`[[Prototype]]`链上层存在foo，但是被标记为只读`(writable:false)`，那么无法修改已有属性或者在myObj上创建屏蔽属性。如果运行在严格模式下回报错，否则这条赋值语句会被忽略。总之不会发生屏蔽。

3. 如果在`[[Prototype]]`链上层存在foo并且它是一个setter，就一定会调用这个setter。foo不会被添加到myObj，也不会重新定义foo这个setter。

4. 如果希望在第二种和第三种情况下也屏蔽foo，就不能使用`=`操作符，而是使用`Object.defineProperty(..)`来想mObj添加foo。

如果foo即出现在myObj中也出现在myObj的原型链上层，那么会发生屏蔽。myObj中包含的foo属性会屏蔽原型链上层的所有foo属性。



有些情况下会产生隐式屏蔽：

```
var anotherObj = {a: 2};
var myObj = Object.create(anotherObj);
anotherObj.a; // 2
myObj.a; // 2
anotherObj.hasOwnProperty('a'); // true
myObj.hasOwnProperty('a'); // false

myObj.a++; // 隐式屏蔽！

anotherObj.a; // 2
myObj.a; // 3

myObj.hasOwnProperty('a'); // true
```

尽管`myObj.a++`看起来是查找并增加`anotherObj.a`属性，但是`++`操作相当于`myObj.a = myObj.a + 1`，也就是先`[[Get]]`再`[[Put]]`。

### 5.2 "类"

#### 5.2.1 类函数

JS中所有**函数**(Foo)默认都拥有一个名为prototype的公有并且不可枚举的属性，它指向另一个对象，这个对象被称为Foo的原型。

```
function Foo(){}
var a = new Foo();
Object.getPrototypeOf(a) === Foo.prototype; // true
```

`new Foo()`会生成一个新对象，这个新对象内部`[[Prototype]]`关联的就是`Foo.prototype`对象。

**自己测试了一下`Object.create()`和`new Object()`的区别：**

1.  ```
	var obj = {a: 1};

	var newObj = Object.create(obj);
	newObj.__proto__ === obj; // true

	// 这里obj不是构造函数所以不能通过new的方式生成新对象
	```

2.  ```
	// 一定是函数才有prototype属性
	var obj = function(){};

	var newObj2 = Object.create(obj);
	newObj2.__proto__ === obj; // true

	var newObj = new obj();
	newObj.__proto__ === obj.prototype; // true
	```

创建类的实例的过程是复制行为，但是JS一直在模仿类的行为，却并没有类似的复制机制，而是通过`[[prototype]]`让对象之间关联。

#### ⚠️注意：

1. 所有的对象都有`__proto__`内置属性，称为隐式原型，除了`Object.prototype`。这个属性指向创建这个对象的函数（constructor）的prototype。

2. 所有的函数都有`prototype`公有属性，称为显式原型，除了通过`Function.prototype.bind`构造出来的函数。这个属性指向函数的原型对象。

具体区别[参考这里](https://www.zhihu.com/question/34183746)

#### 5.2.2 构造函数

```
function Foo(){}
Foo.prototype.constructor === Foo; // true

var a = new Foo();
a.constructor === Foo; // true
a.__proto__.constructor === Foo; // true
```

`Foo.prototype`默认有一个公有并且不可枚举的属性`.constructor`，这个属性引用的是对象关联的函数Foo。

**构造函数还是调用？**

```
function NothingSpecial(){
  console.log('hello');
}
var a = new NothingSpecial(); // "hello"
a; // {}
```

NothingSpecial只是一个普通函数，但是使用new调用是，它会构造一个对象并赋值给a，这是new的副作用（无论如何都会构造一个对象）。这个调用是一个构造函数调用。

所以，函数不是构造函数，当且仅当使用new时，函数调用会变成“构造函数调用”。

**`a.constructor`是一个非常不可靠并且不安全的引用，要尽量避免使用这些引用。**

```
function Foo(){};
Foo.prototype = {}; // 创建一个新的原型对象
// 需要在Foo.prototype上修复丢失的.constructor属性
Object.defineProperty(Foo.prototype, "constructor", {
  enumerable: false,
  writable: true,
  configurable: true,
  value: Foo // 让.constructor指向Foo
})
```

`.constructor`并不是一个不可变属性，他是不可枚举的，但是是可写的。你可以给任意`[[prototype]]`链中的仁义对象添加一个名为constructor的属性或者对其进行修改，你可以任意对其赋值。

### 5.3 (原型)继承

```
function Foo(name){
  this.name = name;
}
Foo.prototype.myName = function(){
  return this.name;
}
function Bar(name){
  Foo.call(this, name);
}
Bar.prototype = Object.create(Foo.prototype);
var a = new Bar('a');
a.myName(); // "a"
```

这段代码的核心就是`Bar.prototype = Object.create(Foo.prototype);`。调用`Object.create()`会创建一个新对象并新对象内部的`[[prototype]]`关联到你指定的对象`Foo.prototype`。

**注意：下面这两种方式是常见的错误做法，因为它们都存在一些问题**

1. `Bar.prototype = Foo.prototype`

	这个做法并不会创建一个关联到`Bar.prototype`新对象，它只是让`Bar.prototype`直接饮用`Foo.prototype`对象。因此当执行雷西`Bar.prototype.myName = ...`的赋值语句时会直接修改`Foo.prototype`对象本身。

2. `Bar.prototype = new Foo()`

	这个做法的确会创建一个关联到`Bar.prototype`的新对象。但是它使用了Foo的构造函数调用，如果函数Foo有一些副作用（比如写日志、修改状态、注册到其他对象、给this添加数据属性等等）的话，就会影响到Bar的后代。

因此，要创建一个合适的关联对象，必须使用`Object.create()`而不是使用具有副作用的`Foo()`。这样做唯一的缺点就是需要创建一个新对象a并把它关联到希望的对象上，然后把旧对象Bar抛弃掉，抛弃的对象需要进行垃圾回收。



在ES6之前，只能通过设置`__proto__`属性来实现修改对象的`[[Prototype]]`关联，ES6添加了`Object.setPrototypeOf()`，可以用标准并可靠的方法来修改关联。

```
// ES6之前需要抛弃默认的Bar.prototype
Bar.prototype = Object.create(Foo.prototype);
// ES6开始可以直接修改现有的Bar.prototype
Object.setPrototypeOf(Bar.prototype, Foo.prototype);
```

**检查“类”关系**

在传统面向类的观景中，检查一个实例（JS中国年的对象）的继承祖先（JS中的委托关联）通常被称为内省（或反射）。

```
function Foo(){}
Foo.prototype.blah = ...;
var a = new Foo();
```

1. instanceof

	```
	a instanceof Foo; // true
	```

	instanceof操作符的左操作数是一个普通的对象，右操作数是一个函数。instanceof回答的问题是：在a的整条`[[prototype]]`链中是否有指向`Foo.prototype`的对象。

	instanceof智能处理对象（a）和函数（带prototype引用的Foo）之间的关系，如果想判断两个对象（比如a和b）之间是否通过`[[Prototype]]`链关联，只用instanceof无法实现。

2. isPrototypeOf

	```
	Foo.prototype.isPrototypeOf(a); // true
	```

	isPrototypeOf回答的问题是：在a的整条`[[prototype]]`链中是否出现过`Foo.prototype`。

	```
	b.isPrototypeOf(c); // b是否出现在c的[[prototype]]链中
	```

3. getPrototypeOf

	```
	Object.getPrototypeOf(a) === Foo.prototype; // true
	```

	通过getPrototypeOf可以直接获取一个对象的`Prototype`。

4. `__proto__`

	绝大多数（不是所有）浏览器也支持一种非标准的方法来访问内部`[[Prototype]]`属性：

	```
	a.__proto__ === Foo.prototype; // true
	```

	`__proto__`的实现大致是：

	```
	Object.defineProperty(Object.prototype, "__proto__", {
	get: function(){
	  return Object.getPrototypeOf(this);
	},
	set: function(o){
	  Object.setPrototypeOf(this, o);
	  return o;
	}
	})
	```

	访问`a.__proto__`时，实际上是调用了getter函数。虽然getter函数存在于`Object.prototype`对象中，但是它的this指向对象a，所以和`Object.getPrototypeOf(a)`结果相同。

### 5.4 对象关联

`[[Prototype]]`这个连接的作用是：如果在对象上没有找到需要的属性或方法引用，引擎就会继续在`[[Prototype]]`关联的对象上进行查找。

`Object.create()`会创建一个新对象，并把它关联到指定的对象，这样就可以充分发挥`[[Prototype]]`机制的威力（委托）并且避免不必要的麻烦（比如使用new的构造函数调用会生成`.prototype`和`.constructor`引用）。

`Object.create(null)`会创建一个拥有空（null）`[[Prototype]]`链接的对象，这个对象无法进行委托。由于这个对象没有原型链，所以instanceof操作符无法进行判断，总是会返回false。

`Object.create()`的polyfill（抹平差异化：代码判断当前浏览器有无这个功能，没有写一些支持代码）代码：

```
if(!Object.create){
  Object.create = function(o){
    function F(){}
    F.prototype = o;
    return new F();
  }
}
```

这段polyfill代码使用了一个一次性函数F，通过改写它的prototype属性使其指向要关联的对象，然后再使用`new F()`来构造一个新对象进行关联。

### 5.5 小结

如果要访问对象中并不存在一个属性，`[[Get]]`操作就会查找对象内部`[[prototype]]`关联的对象。这个关联关系实际上定义了一条原型链（有点像嵌套作用域链），在查找属性时会对它进行遍历。

所有普通对象都有内置的`Object.prototype`，指向原型链的顶端（比如全局作用域），如果在原型链中找不到指定的属性就会停止。toString、valueOf和其他一些通用的功能都存在于`Object.prototype`对象上，因此所有对象都可以使用它们。

关联两个对象最常用的办法就是使用new关键词进行函数调用，在调用中会创建一个关联到其他对象的新对象。

使用new调用函数时会把新对象的prototype属性关联到其他对象，带new 的函数调用通常被称为构造函数调用，尽管它们实际上和传统的面向类语言中的类构造函数不一样。

虽然这些JS机制和传统面向类语言中的“类初始化”和“类继承 ”很相似，但是JS中的机制有一个核心区别，那就是不会复制，对象之间时通过内部的`[[prototype]]`链关联的。

对象之间的关系不是复制而是委托。


## Reference

[附上这本书的英文Github版](https://github.com/getify/You-Dont-Know-JS)