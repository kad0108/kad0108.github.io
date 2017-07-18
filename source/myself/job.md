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
