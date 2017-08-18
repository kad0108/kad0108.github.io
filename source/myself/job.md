## 2017-08-18 百度凤巢一面

1. 自我介绍

2. CSS单位，除了px、em、rem还有什么？vh、vw。em和rem区别。

3. CSS3用过哪些属性.

4. box-sizing

5. 隐藏元素

6. CSS选择器优先级

7. CSS3的选择器有哪些，属性选择器。`:nth-child,:last-child,[attribute^=value],[attribute$=value],[attribute*=value]`

8. JS原型链，怎么体现链形

9. 闭包，什么情况下用到

10. 事件绑定，兼容

11. call和apply，bind

12. 继承

13. websocket

14. web安全

15. 跨域

16. ES6用过哪些特性

17. 介绍一下vue

18. 后台语言掌握哪些，php有用吗？说一下nodejs做过的事情

19. 实现动画可以用什么方式？requestAnimationFrame和setInterval的区别。如何判断当前tab也是否可见。

20. 排序有哪些方法，说一下快排

21. 说一下hackathon自己做的事情

22. 你知道service worker吗？那你说一下webworker

23. 用canvas做过什么？说了一下手势解锁的实现

24. 最近在看什么书。


## 2017-08-14 百度网页搜索二面

1. 自我介绍。为什么双方要合作？你重构的游戏的项目是单独的吗？面试官有关注微软小英，去玩单词消消乐，是头一个会关注我做了什么事情并去体验的面试官。手指滑动时怎么识别滑到了哪个字母？是多少个人在做？我换一个浏览器能正常显示玩吗？微软小英里面你做的内容在哪里能看？都会有什么bug？重构使用vue是谁决定用的框架？

2. 做的这个调查问卷可以访问吗？爬虫网页分析这个分析了什么内容？用phantomJS和selenium，用phantomJS被封用selenium是什么情况？selenium的webdriver会去驱动firefox、chrome、phantomJS是吧，然后我突然想起来phantomJS是headless的。然后你是递归的去爬吗？得到数据后怎么分析的？

3. 看你算法经历很多，怎么会想要做前端？

4. 上一面觉得自己哪道题答的不太好？etag对于集群了解吗？第一次访问落到这台机器上返回一个标识，又一次访问落到另一台机器上返回另一个标识。对etag原理了解一下。

5. 向后端提交数据，一种是ajax，一种是form，现在要提交一个文件，要提交之后不刷新页面。写代码。

   ```
   <form action="" method="post" enctype="multipart/form-data" target="myiframe">
   	<input type="file" name="file" />
   	<button type="submit">submit</button>
   </form>
   <iframe name="myiframe" hidden></iframe> 

   // form提交将target指向iframe的name
   // form提交文件，编码应该是multipart/form-data
   ```

6. 一个输入框，用户在输入字母时向后台发送请求返回推荐搜索关键字，但是不能每次输入都发送，应该在用户停止输入1s后发送。写代码。

   ```
   var $input = $("input");
   var timer;
   $input.addEventListener("keydown", function(e){
     clearInterval(timer);
   })
   $input.addEventListener("keyup", function(e){
     timer = setInterval(function(){
       // ajax request
     }, 1000);
   })
   ```

7. 及时通信，websocket降级解决方案是什么？ajax轮询是不能做到及时的。

8. 实现这样一个场景，一个列表，每行可以向左滑动，而且动画要流畅。应该是transition吧？面试官说的transform，问比直接修改width好在哪儿。z-index只对定位元素有效，就是设置了position的元素。

9. 给一个根文件，里面可能有很深的文件，要求将全部文件读取之后执行回调函数。我说了一个想法，面试官说你的这个做法是同一时间只用了一个进程，而实际上读取文件可以是并行的。promise.all应该可以做。

10. 面试官他们在做一个叫商桥的产品，有实时通信窗口。在用modx替换redux。会先都尝试一些框架，然后根据需求决定要使用什么。在考虑用GraphQL来替代RESTFUL API。

## 2017-08-14 百度网页搜索一面

1. 自我介绍。IFE你都做了哪些任务？

2. 写个快排

3. 对html语义化的理解。Html5有哪些新标签？用的多的有哪些？

4. 行内元素和块级元素有哪些？区别。怎么转化？display还有哪些属性？

5. css加载的方式。行内样式和important哪个优先级高？

6. CSS3用过哪些？低版本如何兼容？IE呢？

7. 你做了日历组件，可以选择区间吗？

8. MongoDB和MySQL的区别，为什么选择MongoDB

9. PhantomJS和selenium区别。抓取数据做了什么分析。

10. 瀑布流布局这个你是怎么设计实现的。图片资源放哪儿？

11. ERP项目说一下，你做了什么工作。

12. Ajax说一下。写一下。GET和POST的区别

13. 输入一个url之后的过程，越具体越好。

14. Http缓存头部

15. Http状态码

16. 跨域，iframe现在都不用了为啥？

17. 盒子模型

18. 有移动端开发经验吗？有没有遇到自适应问题。em在元素多层嵌套时是很有问题的。

19. flex有用过吗？

20. cookie、session、localStorage区别。你都用什么本地存储？IE6呢？userData

21. 你写的组件有给npm发包吗？

22. 写一个继承

23. 生成3～10的随机值

24. 事件的绑定

25. 在两个div之间插入一个div

26. 如何实现三个div并排分布，除了用flex。

27. 在微软的工作，有遇到什么问题？


## 2017-08-08 阿里一面

1. 简单介绍前端相关工作

2. 二叉堆的维护过程？二叉堆是不是一颗完全二叉树？

3. 布局的时候浏览器的机制，bfc了解吗？怎么触发bfc，它有什么作用？

4. 介绍同源策略，跨域的技术方案。

5. CSS属性继承：文本相关的属性`text-*, font-*, color`，列表相关的属性`list-*`.

6. 清楚浮动，怎么实现

7. 闭包概念，怎么形成一个闭包。实现一个功能， 外层是ul，里面是一堆li，希望点击第i个li的时候alert(i)。发现这里面试官好像有误导我，让我去向内层返回函数中传值。

   ```
   var lis = document.getElementsByName('li');
   for(var i = 0; i < lis.length; i++){
     lis[i].onclick = (function(i){
       return function(){ // onclick必须是一个函数，匿名函数立即执行如果不return一个function，onclick是做不了任何事
         alert(i); // 这里就是闭包的效果了，可以直接访问上层作用域中的i
       }
     })(i);
   }
   ```

8. 用ES5之前的语法怎么实现继承？原型继承。`Object.create`实现的继承叫做原型继承。

   ```
   function Foo(){}
   Foo.prototype = ...

   function Bar(){
     Foo.call(this);
   }
   Bar.prototype = Object.create(Foo.prototype);
   ```

9. 实例的`__proto__`指向谁？

10. vue的MVVM双向绑定怎么原生JS实现的？

11. 期望工作地点。

## 2017-08-05 百度运维二面

* 36匹马，6个跑道，没有计时器，让找出其中最快的三匹马，需要比赛多少次。

## 2017-08-05 百度运维一面

1. 自我介绍。

2. 微软实习到什么时间？微软那边转正？对微软的看法。你mentor对你的评价。发现可能面试官会很关注你能在微软那边转正，还会来这里？

3. 有没有投递百度别的部门？有没有面试别的公司？

4. 除了在微软做的项目， 还有哪些项目是你觉得做的很好的？数据库有用过了解过哪些？MongoDB和MySQL的区别？Echarts在请求数据还没响应时你是怎么绘制的？Vue和React的区别？说几个webpack的loader。ES5和ES6平常用哪个？

5. css选择器优先级判断标准。

   * `id > class > tag`
   * 选择范围越小，优先级越高。!important优先级最高。
   * 引入方式优先级：`行内 > 内嵌style标签  > 外部引用link > 导入@import,import是不能在html中直接导入的,需要通过style标签`

6. 元素定位的几种方式，之间的区别。

7. DocType是什么？如果不加doctype呢？应该是不能正常的显示内容吧。

8. JS类型有哪些？基本类型和引用类型的区别？

   * 赋值，基本类型值传递。
   * 存储，基本类型存储在栈中，引用类型栈中存储对象在堆内存中的访问地址。

9. call和apply的作用，区别。

10. 原生ajax。知道readyState吗？

11. http的请求方法。知道Restful吗？

12. 闭包了解过吗？写一下。闭包会有什么问题。

13. cookie和session的区别。比如我同时打开几个都访问这个url的窗口，那这时cookie和session是否是共享的？

14. 了解模块化吗？AMD、CMD、CommonJS。区别：

    ```
    AMD、CMD是运行在客户端的，加载的文件可以是网络上的，所以是异步。
    CommonJS运行在服务器端，加载的文件都是本地的，所以不存在网络请求，是同步读取的。
    ```

15. 写一下快排。复杂度

16. 给一个数组，找出其中出现次数最多的数。用JS写一下。

17. 让你做一个实时通信的产品，好像是想让我设计这个产品，我特意引到了技术上说了ajax轮询和websocket，问我还有什么方式，HTTP1.1的长连接。问http请求和websocket的区别：

    * http请求需要来回传输header，占耗带宽
    * ajax轮询增加服务器的压力
    * ajax轮询需要用定时器来间隔发送请求

18. 给两个正整数，要求不借助额外的存储空间实现两个数交换。

    ```
    a = a + b;
    b = a - b;
    a = a - b;
    ```



## 2017-07-14 搜狐二面

* 微软小英是什么？你做了哪些功能？为什么要重构？vue怎么把数据界面做模块化？vue数据是怎么维护的？vue数据双向绑定？vue怎么操作dom？vue怎么发ajax？有用ES6吗？vue组件你怎么划分？vue里面怎么做事件绑定的？node你熟悉吗？

* 瀑布流布局怎么实现的？

* 你参加过ACM，为什么做前端？

* 如果你遇到一些问题解决不了，你一般怎么解决？

* 你觉得你前端还有什么地方需要加强？

* 你觉得写的不好的代码是什么样的？为什么把变量暴露给window不好？

* vue里面做重用？component能继承吗？你说的是模块组合

* 你代码风格有什么参照吗？或者有用过代码检查的工具吗？

* 日历组件，怎么知道这个月的最后一天？应该是new Date(year, month, 0)这样就会拿到上一个月的最后一天



## 2017-07-14 搜狐一面

1. 自我介绍

2. 写个函数实现js对象key和value反转，考察递归思想。这儿有个面试官没想到的地方，如果value是对象obj，反转为key会是什么情况？是`JSON.stringify(obj)`吗？并不是。实际上是调用了obj的toString方法变为`"[object Object]"`作为key。

3. **给一个无序数组，找中位数。用快排的划分操作做，分析时间复杂度，`n + n/2 + n/4 + ... = O(2n)`, partition操作时间复杂度O(n)，每次都要遍历所以总的平均复杂度是O(n)。**

4. vue的特点。

5. **diff算法具体思想。**

6. **说说虚拟DOM，为什么要这么做？**

7. **vue-router中一个url对应到一个component，这个过程是怎么做的？对于这个路径`/xxx/{id}/stu`呢？**

8. 跨域有哪些方式？

9. 两个垂直div都设置margin会怎么样？怎么解决margin重叠？

10. postion各个值及区别。relative相对于谁？

11. node有了解？

12. es6了解实践过哪些特性？箭头函数和普通函数的区别？promise中哪些部分是同步的哪些部分是异步。

   ```
   下面代码的执行顺序，js中还有哪些类似的情况，是想让我说setTimeout，我说setTimeout会放在任务队列中，promise.then会放在主线程的尾部执行。
   new Promise(function(resolve, reject){
   	// do sth ---> 1
   	resolve();
   }).then(function(){
     	// do sth ---> 3
   })
   // do sth  ---> 2
   ```

13. 在微软实习做的事情。

14. **js中重要的编程模型。对函数式编程和面向对象编程的实践。函数式编程有哪些特点？** 面向对象怎么实现，原型链继承和java继承有什么不同。

15. 怎么开始学习前端？你觉得做前端相对做后端有什么特色？你有了解一些新的前沿的技术吗？PWA？平常怎么学习？有看什么书籍？你觉得You don't know JS好在哪里？你接下来有什么学习计划？
