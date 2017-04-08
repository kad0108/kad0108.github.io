## 2017-04-07 腾讯四面

* 你现在还有在做ACM吗？

* 浏览器访问后台的整个过程。DNS协议查询过程。DNS协议底层是用什么协议啊？UDP。查询劫持。整个url渲染过程最了解哪个？描述http协议，post数据大小。服务器怎么处理http请求，webserver了解过吗？渲染的过程中如果返回的资源有先后顺序，怎么优化，提高渲染效率？我说css的@import和link，那JS文件怎么解决？我说require和module.exports。

* 字符串匹配都用哪些算法。kmp算法复杂度多少。AC自动机实现原理。trie树每个节点都需要开一个数组吗？

* 单链表判断是否有环。

* 给一个数组寻找第一个不在数组中的最小正整数。给出O(nlogn)的，要更快的

* 一个矩阵，从左上走到右下，只能向右向下，求最大路径和。给出最优空间时间复杂度。如果要求走过的路径中不允许权值小于0，需要做什么特殊处理？

* 欧几里得算法复杂度

* 你一直在做算法，为什么要做前端？

## 2017-04-07 腾讯三面

* 自我介绍

* session和cookie的区别

* get和post的区别，post表单有什么格式？[post提交数据格式](https://imququ.com/post/four-ways-to-post-data-in-http.html)，[发送表单数据](https://developer.mozilla.org/zh-CN/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data)
```
HTTP的请求报文包括三部分：起始行(method url version)、首部(header)、主体(body)。
POST提交的数据必须放在HTTP的body中，服务器端根据http header中的content-type字段来判断数据是用哪种编码方式编码的，然后进行解码。

先看form表单的enctype属性：
1. 默认编码方式发送前编码所有字符：application/x-www-form-urlencoded
2. 不对字符编码，在使用包含文件上传的表单时必须使用该值：multipart/form-data
3. 空格转换为+加号，不对特殊字符编码：text/plain
```

* amd和cmd
```
amd:依赖前置，提前执行
cmd：依赖就近，延迟执行
```

* 介绍盒子模型

* 冒泡分几个阶段，我觉得是面试官说错了。怎么禁止冒泡

* 网页要加载很多资源，不同浏览器对用一个域下的并发数是多少？

* 有做过单页的应用吗？介绍一下MVC、MVVM

* 用过jsonp吗？是get还是post请求？跨域有哪些方法？

* 以前有实习过吗？

## 2017-04-07 百度二面

* 说一下做过的项目遇到哪些难点

* 你怎么理解单页应用，你自己实现你从哪几方面考虑。路由切换怎么实现？我说用history API，问HTML5有兼容问题，又怎么解决？
```
hash(#)：不会被爬虫当作不同内容来抓取
hash bang(#!):用作Ajax URL的分段标识符，用于浏览器的状态保存，会为爬虫索引方便SEO。
```

* 你怎么理解MVX的框架？我说了MVC和MVVM的区别。
```
MVC的所有通信都是单向的
MVVM是数据双向绑定
```

* 实现文字超长出省略号。text-overflow:ellipsis。

* CSS选择器优先级，两个类相同属性作用于同一个元素优先级。就近原则

* get和post区别，post其实也不安全，怎么安全些？我说编码。

* http状态码301,302,304，301和302的区别
```
302搜索引擎会抓取新的内容同时保留旧的网址。
301搜索引擎在抓取新的内容的同时也将旧网址替换为重定向之后的网址。
```

* 浏览器缓存机制。[http强缓存协商缓存](http://www.cnblogs.com/wonyun/p/5524617.html)

* 本地存储，说一下localStorage、sessionStorage、cookie有效期。两个网站www.baidu.com和baike.baidu.com如何使cookie、localstorage实现通信。

* 怎么理解web安全？我说了xss和csrf以及防御

* 你有什么问题


## 2017-04-01 微软亚研Offer

## 2017-04-01 微软二面

* 给n个链表，链表中元素递增，链表平均长度l，给一个数k，找出链表中元素重复次数大于k的。时间空间复杂度。优化优化再优化

## 2017-04-01 微软一面

* 自我介绍

* 说一下做过的项目

* 一个数组，把里面的0挪到后面，其它数字挪到前面。

* 给一个字典，里面很多字符串，找到两个串长度乘积最大而且没有重复字符。空间时间复杂度。优化优化再优化


## 2017-03-29 美团二面

* 自我介绍

* 你有后台经验为什么考虑做前端？说一下调查问卷。遇到什么难点和挑战？状态管理有涉及到吗？有接触过其他的前端的框架吗？你移动端项目偏少一些。说一下搜索引擎系统。说一下acm竞赛过程、解题思路。前端学习有什么渠道？前端学习计划？

* 浏览器在渲染过程中的[repaint和reflow](http://harttle.com/2015/08/11/reflow-repaint.html)。通过js去获取元素的宽度会触发什么？reflow。为什么呢？如果是通过transform的translate那会触发什么呢？

	[渲染树构建之后是reflow->repaint->GPU图像处理](http://www.kejik.com/article/292980.html)
	
	[GPU](https://isux.tencent.com/emancipate-gpu.html)
	```
	触发reflow必然会触发repaint
	什么情况下触发reflow：
	1. dom元素的添加、修改、删除
	2. 移动dom元素，动画
	3. resize窗口，滚动
	4. 读取元素某些属性(offsetxx, clientxx, getComputedStyle, top/left/width/height)

	CSS动画完全工作在GPU上，而js动画是需要计算每一帧的状态。GPU图像处理计算，使用硬件加速。FPS每秒帧数越高，画面越流畅。
	```

* CSS一种link进来，一种通过style直接写在head中，区别及优缺点
	```
	CSS引入方式：
	行内样式：<p style="..."></p>，缺点不利于SEO、不利于维护、html页面繁杂
	内嵌样式：<style></style>
	外部引用: <link> 表现与结构分离，维护方便，利于SEO，html与css文件同时加载网页打开速度快。
	导入：@import 等页面加载完才加载css，如果网页较大会先显示无样式的页面，再出现样式。而link是在页面加载时同时加载。

	理论上的优先级：行内>内嵌>外部引用>导入
	实际上：谁离相应的代码近，谁的优先级高。比如头部先style后link，那么link中的样式优先级就高于style中的。
	```

* js深拷贝、浅拷贝。slice、splice、递归遍历

* 跨域怎么解决？JSONP有什么缺点？
	```
	只支持GET请求，不支持其他HTTP请求
	JSONP无法确认请求域是否安全
	无法确定JSONP请求是否失败
	```

* function abc(){}, abc.length
	```
	length：函数接收的参数的个数
	arguments：伪数组，包含传入函数的所有参数。
		伪数组：1. 具有length属性
				2. 按索引方式存储数据
				3. 不具有数组的push、pop等方法
	rest参数：ES6引入，用于获取函数的多余参数，把参数转换为数组，测试了rest具有push等方法。
		function fn(a, ...vals){
			console.log(vals);
		}
		fn(1,2,3,4); // [2,3,4]
	...扩展运算符: 将一个数组转为用逗号分隔的参数序列，测试了只能用在函数参数中。
		function add(x, y){
			return x+y;
		}
		var nums = [1,2];
		add(...nums); // 3
	```

* [$.ready 和window.onload的区别](http://blog.csdn.net/xiebaochun/article/details/36375481)
	```
	1.执行时间 
	window.onload必须等到页面内包括图片的所有元素加载完毕后才能执行。 
	$(document).ready()是DOM结构绘制完毕后就执行，不必等到加载完毕。 

	2.编写个数不同 
	window.onload不能同时编写多个，如果有多个window.onload方法，只会执行最后一个 
	$(document).ready()可以同时编写多个，并且都可以得到执行 

	3.简化写法 
	window.onload没有简化写法 
	$(document).ready(function(){})可以简写成$(function(){});
	```

* cookie和本地存储，cookie和localStorage存取。a.meituan.com和b.meituan.com下是否可以获取cookie和localStorage
```
读取同一份localStorage数据必须在相同的协议、主机、端口下。也就是localStorage不允许跨域。
```

* 判断用户是不是在网的状态？轮询、navigator.onLine
* 若干根绳子，每根绳子都一定会从一端开始烧在一个小时之内烧完，粗细材质都不一定，问怎样能拿到一个小时15分钟。

* 给你一个篮球和是直径2/3左右的尺子，问怎么得到篮球的半径。

## 2017-03-28 腾讯二面

* 实习时间

* 你为什么做前端？通过什么样的渠道来学习？你以后把前端当作职业选择？你怎么看前端技术未来是绿野长青还是走到头？你怎么知道公司要做前后端开发。前端比重越来越大你怎么得出这样的结论？服务器端有什么优点？其他语言就没有高并发能力吗？那你觉得node有什么优点缺点，适合什么样的场景？阻塞和非阻塞什么区别？[阻塞非阻塞与同步异步的关系](https://www.zhihu.com/question/19732473)？
```
阻塞与非阻塞是程序在等待调用结果时的状态，是对发起请求方而言，需要去check接收方是否有结果返回。
同步与异步关注的是消息通信机制，是对请求接收方而言，接收方去决定多会儿返回结果。
```

* 你做的项目中做的最好的是哪个？为什么觉得它做的好？因为它是一个完整的过程你就觉得好？好的标准你怎么定义？那你为什么选mongodb？mongodb适合于哪些场景？你用js你做前端页面，node做后台服务，javac++适合效率更高的场景，那你觉得mongodb优点缺点是什么？对js这种语言你觉得缺点是什么？我说单线程，然后问单线程的问题是怎么解决？我说有事件轮询。然后面试官说这不就相当于解决了嘛。然后我就说我了解过js是无法使用CPU多核的，然后问那怎么才能使用多核呢？
```
单线程的问题是所有任务需要排队耗时，采用事件轮询执行还是在主线程。
js使用多核：可以加childProcess创建子进程，webworker创建子线程。
```

* 你对闭包怎么评价？我说一些特定的情况下需要使用闭包但是不能滥用，会有内存的问题。然后问为什么会有内存的问题？因为它把变量暴露在全局中延长了生命周期所以就会[内存泄漏](http://jinlong.github.io/2016/05/01/4-Types-of-Memory-Leaks-in-JavaScript-and-How-to-Get-Rid-Of-Them/)吗？java的垃圾回收机制会不会出现内存泄漏？js不是因为暴露在外面就造成内存泄漏吧，那怎么解决这个问题呢？那要是忘记清除它的引用呢？有没有具体的方法实现内存及时释放的机制？
```
内存泄漏的就是程序不再需要占用内存的时候，内存没被垃圾回收机制回收。
设置引用为null，GC就会自己清除，不需要自己管。
```

* 我看你做过网页爬虫。有这样一个场景，浏览网页会经常弹出小窗，会是[插播广告](http://quotation.github.io/web/2015/04/15/china-telecom-isp-hijack.html)或者联通移动的流量小球，怎么去检测？呈现出来之后通过页面的结构能不能判断？我说检测iframe，还可以升级为https。

* 平时上网前端会有DNS解析，你知道解析过程，怎么解析的吗？它为什么要设计成这样子？后端原来是ip为a的服务器，现在换到b了，你拿到了错误的域名解析ip地址会怎么样？应该怎么办？
```
分布式分层次的DNS解析可以覆盖所有域名系统，本地DNS缓存是为了加快速度。
出现域名解析错误，可以清除本地DNS缓存
```

* 你了解[CDN](https://www.incapsula.com/cdn-guide/what-is-cdn-how-it-works.html)吗？目的是什么？为什么能方便分发？怎么提高用户访问速度？服务器多了就能提高吗？
```
CDN把静态内容分发到距离用户近的缓存服务器，然后DNS托管。
DNS托管：把DNS解析的地址改成CDN提供的
```
* 你还有什么要了解的？


## 2017-03-27 腾讯

* 实习时间

* 说一下前端的项目经验

* 问卷平台遇到什么困难怎么解决的？我说父子组件通信和非父子组件通信。非父子组件通信中你事件怎么写的？不使用vue怎么实现这个功能？我说发布-订阅模式。

* 木桶布局是什么布局？问横向有滚动条吗？问你这个布局可以用css实现吗？然后我就说了一遍实现的原理，说都需要JS操作。

* 说一下网页抓取。如果有一个人在用phantomJS拉取你的网页，你怎么判断它是真的用户还是用的模拟器拉取的？我让面试官说了一下判别方法。
	```
	1. 一种方法phantomjs会带着自己的标记，可以去看这个标记。
	2. 用个工具监控你的调试比如窗口大小调整，标记你的行为。
	3. 还可以打一些console。
	4. 设备指纹。根据爬取频率。你能绕过ip，就找一些你绕不过的东西。
	5. phantom不是真正的浏览器，一定有一些浏览器中没有的方法，也有浏览器中有的phantom中没有的。这个你可以考虑解决一下写个博客。
	6. userAgent可以伪造，没什么大用。应该多维度结合。
	```
	你抓的是哪儿的数据？频限问题怎么处理？我说了解决ip被封的方法。

* JQuery用过吗？

* Ajax在什么情况下可以跨域？我说level2提供了CORS。通过设置origin那个头部。面试官说没听过2。说一下其他的跨域。我说jsonp，这是一个get请求，数据有限制的吧。数据限制是多少？chrome是4k，IE下是2k。如果我有个数据很大，用http get肯定不行，又想跨域应该怎么办。我说post请求，postMessage是不是可以？面试官说postMessage有兼容性问题，低版本浏览器怎么办？
```
http get数据有限制，超过这个限制怎么办。你写一个表单，表单有action指向post指向的地方，提交表单马上刷新页面，target可以指定刷新的页面，写个iframe，让target指向这个iframe就可以了。你可以试一下写在博客里对你有帮助。
```

* 问一个安全相关的问题。说一下csrf。它是怎么伪造的？怎么拿用户的cookie？我说钓鱼链接，问可以读取用户的cookie吗？我说可以读取header。
```
csrf即便你点了一个链接，然后你登录过对应链接的网站，带着你的cookie就走了，这个链接网站是打死都拿不到你的cookie的，它是带着你的cookie走了，这就是伪造，伪造你的请求，相当于是你自己发的。比如你登录了银行，网页上有一些很有诱惑性的图片，上面有href指向银行转账的请求，你自己点你就带着cookie走了。
```

* 你这个上海邀请赛是什么奖？ACM

* 你答的不够深入，再了解一下，再深入一些会更强。你有什么问题？跨域的post需要研究透。会把很多自己的东西嵌入到别人那里，做数据采集，涉及到大量的跨域。

## 2017-03-24 滴滴另一个部门

* 自我介绍

* 调查问卷的平台是个什么项目？介绍一下。多人管理是什么？mongodb中数据是怎么存储的？为什么选择使用vue？怎么考虑的？[对比react其它框架](https://cn.vuejs.org/v2/guide/comparison.html)

* [虚拟dom内部实现机制](https://github.com/livoras/blog/issues/13)？[diff算法](https://zhuanlan.zhihu.com/p/20346379?columnSlug=purerender)react是如何实现比其它框架更快？

* 看了我的博客，说一下cookie和session的区别。cookie会带来安全问题，有什么防范措施？XSS、CSRF。怎么预防XSS，怎么防止外部伪造用户？

* 还问你博客上的问题，解决跨域有哪几种手段？

* JS的内存机制，垃圾怎么回收。

* ES6中有什么新的特性。

* 开发用什么工具？sublime，调试用什么工具？chrome devtool

* 说一下webpack，用过哪些loader。如果要引用一个第三方的库，webpack打包需要注意什么？这个没查到相关资料。

* 实习时间。

* 解析url拿到查询参数的值`http://www.didichuxing.com/manage?a=1&b=2&c=3`
```
function parseUrl(url, key){
	var str = url.split('?')[1];
	var arr = str.split('&');
	obj = {};
	arr.forEach(function(item){
		var tmp = item.split('=');
		obj[tmp[0]] = tmp[1];
	})
	return obj[key];
}
```

* 对哪些数据结构熟悉？那就做个树的题吧，把一棵树转成双向链表对树有什么要求？二叉搜索树转双向链表。
```
struct TreeNode{
	int val;
	TreeNode *left;
	TreeNode *right;
	TreeNode(int val): val(val), left(NULL), right(NULL) {}
};

class Solution{
public:
	TreeNode* treeToList(TreeNode *root){
		if(root == NULL) return root;
		v.clear();
		dfs(root);
		for(int i = 0, len = v.size(); i++){
			if(i == 0) v[i]->left = NULL;
			else v[i]->left = v[i-1];
			if(i == len-1) v[i]->right = v[i+1];
			else v[i]->right = NULL;
		}
		return v[0];
	}
private:
	vector<TreeNode*> v;
	void dfs(TreeNode *root){
		if(root == NULL) return;
		dfs(root->left);
		v.push_back(root);
		dfs(root->right);
	}
}
```

* 自学前端遇到的最困难的是什么？

* 实现两列布局，左侧固定宽度，右侧自适应



## 2017-03-22 百度

* 自我介绍
* 说一下学习前端的过程
* 弹出层组件是你自己实现的吗？说一下实现的思路。
* 状态管理你是怎么做的？用localStorage和vuex有什么感受？存储本地可以手动删除吗？
* 你还用过node，你是怎么用的？
* 除了用框架，你有写过原生JS吗？
* ES6熟悉吗？
* 怎么理解标签语义化。说一下语义化标签。
```
表现与结构分离：没有css时，也能呈现很好的内容结构
有利于SEO：搜索引擎爬虫爬取
便于团队开发维护
header、footer、section、article、nav、aside
```
* img的title属性和alt属性的区别
* 如何在新页面打开链接。应该是`target＝"_blank"`
* input设置成只读和最多20个字符。readonly属性，maxlength属性规定输入字段中的字符的最大长度。
* 转义字符，空格`&nbsp;`,左尖括号`&lt;`,右肩括号`&gt;`
* 禁止a标签跳转
```
<a href="javascript:void(0);" ></a>
<a href="javascript:;" ></a>
<a href="#" onclick="return false;"></a>
```
* 怎么理解盒模型，分两种知道是哪两种吗？有什么区别？
* 有哪些css选择器以及优先级： 
```
优先级排序：
ID选择器 id
类选择器 class
属性选择器 [type="radio"]
伪类选择器 p:hover
标签选择器 p
伪元素选择器 p::before

还有通用选择器 *
子元素选择器 > 
后代选择器 ' '
兄弟选择器 ~
相邻兄弟选择器 +

!important 优先级最高
css选择器越有针对性，选择范围越小，优先级越高
```
* 内联元素和块级元素的区别。内联元素可以设置宽高大小吗？inline-block有兼容性问题吗？我说元素间有缝隙，面试官说这个不是兼容性。从IE几开始支持inline-block.
	[inline-block兼容及间隙问题](https://segmentfault.com/a/1190000004341343)
* 那你说一下其它的感受深的兼容性
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
* 清除浮动
```
本质是触发父元素的bfc，让容器中的子元素不会影响到外面的元素
1. 父元素使用伪元素
.father:before, .father:after{
	content: "";
	display: block;
	clear: both;
}
2. 父元素后加空元素
div.clearfix{
	clear: both;
	zoom: 1;//ie haslayout
}
```
* 固定宽高的元素让它[水平垂直居中](https://www.w3cplus.com/css/vertically-center-content-with-css)。
* absolute和fixed的区别
* 如何声明变量
```
1. var,let,const
2. 不使用关键字
3. window.a = 1
4. function
```
* 有哪些变量类型：Number、String、Null、Undefined、Object、Array、Boolean、Symbol
* 怎么[判断变量类型](http://harttle.com/2015/09/18/js-type-checking.html)
```
typeof
instanceof
实例.constructor === 类型
Object.prototype.toString.call(实例)
```
* 原生JS怎么实现继承
* 介绍ajax。怎么实现的？XMLHttpRequest对象是谁提供的？我说window，然后问window对象是谁提供的，浏览器
* 发一个ajax请求的步骤。代码有哪些方法。服务器响应怎么拿到数据？
* JS的事件：click、keydown、keyup、keypress
* 事件的传播分哪几个阶段？
* 跨域是怎么发生的？怎么跨域
* 在调用方法的时候怎么动态切换上下文？怎么指定this？call、apply区别。bind
* 数组去重，有更简便的方法吗？
* ES6提供了一种数据结构没有重复的数组这个知道吗？set也有迭代的接口。
```
const s = new Set();

[2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));

for (let i of s) {
  console.log(i);
}
// 2 3 5 4
```
* new操作符做了什么事情
```
1. 新建一个对象 f = new Object();
2. 设置对象原型链 f.__proto__ = F.prototype;
3. 改变构造函数内this的指向，让F中的this指向实例，F.call(f);
```
* 图片懒加载是怎么实现的？怎么判断什么时候应该切换src？你这个懒是怎么体现的？如果图片很多如何提高性能？
* 你有什么问题问我吗？


## 2017-03-21 携程Offer

## 2017-03-21 携程二面

* 垂直居中
```
单行内容垂直居中line-height | height
多行内容垂直居中：
1. display:table-cell; vertical-align: middle;
2. position:absolute; top:50%;
3. display:flex; align-items:center;
```

* CSS3新特性
```
边框：border-radius | box-shadow
背景：background-size | background-origin
文本效果：text-shadow ｜ word-wrap
字体：@font-face
变形：transform：缩放、平移、旋转、拉伸
过渡：transition：将效果添加到css属性上
动画：animation：@keyframes
多列：column-count ｜ column-gap ｜ column-rule
用户界面：resize | box-sizing
```

* 用代码画图有什么方法：canvas、svg、css

* 5个以上不同类型的HTML5标签。说一下H5的新特性。

* localStorage和sessionStorage的区别，localStorage的存储上限。

* ES6的新特性
```
let和const
模版字符串
箭头函数
新类型Symbol
set和map数据结构
promise
Iterator
Generator
async
class
module：import、export
```

* 动画的实现方法。css3的animation、js的setInterval和requestAnimationFrame

* Vue实现数据双向绑定。route是怎么实现的。我说window.location,面试官说这样页面会刷新，然后我说window.history，问有哪些方法，pushState、replaceState、go。

* webpack特性，用过哪些loader，有什么好处。有用过热更新吗？知道命令是什么吗？怎么[热部署启动](http://www.luckybird.me/webpack-%E7%83%AD%E9%83%A8%E7%BD%B2.html)？
```
安装 webpack-dev-server
启动 webpack-dev-server
开启热部署模式
启动热部署
```

* 还用过其它的框架吗？jquery，bootstap。有用过其它的库吗？zepto、backbone。有用过前端模版吗？ejs、jade

* 对jquery的方法进行分类：
```
jquery选择器：$()
jquery事件：$().click()
jquery效果：fadeIn等
jquery HTML：对元素的操作，比如设置css属性、添加元素等
jquery遍历：获取祖先元素、父元素、后代元素、同胞元素、过滤元素
jquery Ajax：$.get(),$.post(),$.load()
```

* 用node做过什么？

* 用过socket吗？我说用过websocket。
```
socket.io封装了websocket，同时包含了其它的连接方式，比如Ajax。原因在于不是所有的浏览器都支持websocket，通过socket.io的封装，你不用关心里面用了什么连接方式。你在任何浏览器里都可以使用socket.io来建立异步的连接。socket.io包含了服务端和客户端的库，如果在浏览器中使用了socket.io的js，服务端也必须同样适用。如果你很清楚你需要的就是websocket，那可以直接使用websocket。
```

* 数组去重。

* 说一些状态码：200,304,302,401,403,404,500,101

* 做的项目有遇到什么困难。

* 你现在在大连，你在上海会有问题吗？

* 你有什么问题吗？

## 2017-03-20 美团

* 介绍一下百度前端学院

* 期望做前端还是后端开发？移动端PC端偏好有吗？

* 学习Vue的渠道？和JQuery的差异。

* 使用过Vue的插件吗？

* CSS布局单位的使用，em和rem的区别。
```
em：相对浏览器默认字体尺寸，继承父元素字体大小
rem：相对html、body的字体尺寸，不继承父级
```
* 页面发起请求是使用ajax还是jsonp，有遇到过跨域吗？

* 跨域请求浏览器会执行什么样的操作，域请求有了解吗？

* 模仿星球大战字幕，用到哪些CSS属性？

* 用什么抓包？chrome dev还有用过其它面板工具吗？

* 用过vue的devtool吗？

* 用CSS实现过三角形吗？

* 用过哪些ES6的语法？

* 百度前端学院搭建的框架介绍一下？

* 使用css和js实现过动画，有过requestAnimationFrame吗？贪吃蛇怎么实现的？

* 对实习有什么期望？

* 对个人成长的期望？希望学习什么知识。对造轮子比较有兴趣对吧。

* 你还有什么问题？

## 2017-03-16 滴滴

* 研究生的研究方向，为什么面前端

* JavaScript学的怎么样？ES6有看过吗？你觉得ES6什么比较好用？

* 箭头函数和普通函数的区别。
```
1. 函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。箭头函数的this是不可变的，不可以修改执行上下文，看测试。
	var id = 1;
	var f = () => console.log(this.id);
	f(); // 1
	f.call({id: 4}); // 1
2. 不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。

3. 不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用Rest参数代替。

4. 不可以使用yield命令，因此箭头函数不能用作Generator函数。
```

* promise多异步流程怎么实现

* 用express搭建的多人博客。带用户管理吗？什么叫多人博客？用户管理是怎么写的？都是你自己写的吗？数据库用的是什么？

* 你还用过其他的数据库吗？mongodb和mysql的区别。

* mongodb能做笛卡尔积吗？就是[表的联合查询](http://chenzhou123520.iteye.com/blog/1637397)。
```
在关系型数据库中，通过join连接操作符可以实现多个表联合查询。
而非关系型数据库的特点就是表之间是弱关联，mongodb不建议对多collection之间关联处理。
```

* 后端用过什么语言？什么会用的好一点？

* 做web开发nodejs相对于python、java有什么优势？我说高并发，然后面试官问python、java不能解决高并发吗？node是怎么解决高并发的？我说异步，然后问python不能做异步吗？
* node做web开发有什么缺陷？

	上面两个关于Node的问题的回答：

	[使用 Node.js 的优势和劣势都有哪些？](https://www.zhihu.com/question/19653241)

	[为什么要用nodejs？](http://blog.jobbole.com/100058/)

* vue做的是单页应用吗？你觉得vue这种MVVM框架和之前的框架有什么区别？数据双向绑定是怎么实现的？vue有用虚拟dom概念吗？

* 你的单页能做到按需加载吗？首次加载很慢能做到切到哪个tap再加载能做到吗？[路由懒加载](https://router.vuejs.org/zh-cn/advanced/lazy-loading.html)
```
借助Vue的异步组件 和 Webpack的代码分割
```
* 单页会保存场景吗？localStorage和vuex存储数据哪个方便？
```
vuex存储的数据刷新就没了，localStorage存储的数据就算关闭浏览器只要你不清除就一直都在。
可以组合vuex和sessionStorage，刷新的时候判断sessionStorage有没有保存cache，有的话初始化状态的时候再填进去
```
* 归并和快排的区别。快排哪个地方比归并好？快排为什么不稳定？时间复杂度分析。快排怎么样能尽量做到平均复杂度？
```
快速排序是从头和尾开始对元素进行比较,有可能把关键值相同的两个元素调换了位置,所以说是不稳定的。
随机化枢轴的选取
```
* 你觉得做前端需不需要掌握算法？我说了数据可视化，排序的递归写法怎么变成非递归写法？

* sql语言熟吗？批量更新，更新内容不同，怎么用一个语句实现？

* 你用java做过抓包？你的检索是自己写的吗？
```
倒排索引就是存储某个单词在一组文档中存储位置的映射。
```
* echarts渲染使用canvas渲染，你觉得为什么不用svg渲染？[canvas和svg的区别](http://www.wyzu.cn/2016/0415/115522.html)

* echarts中的交互是怎么用canvas来实现的？比如鼠标hover线变粗。折线图是哪几个图层实现的？
```
貌似是对整个canvas进行监听，获得里面对象的一些信息索引，然后再绘制对应效果
```
* 你学算法有接触过动态规划和贪心吗？说一下区别。
```
动态规划必须有dp状态、状态转移方程，无后效性(当前状态只影响下一个状态而不会影响之后的状态)。
贪心就是按照某个策略每步取最优值，并且达到全局最优。
```

* C有引用吗？c没有引用，c++有。

* 栈存储和堆存储的区别。栈存储基本类型，堆内存为new出来的对象分配内存空间，栈内存存储对象在堆内存中的访问地址。

## 2017-03-14 携程

* 什么时间可以实习？你想做前端还是后端

* 有没有实习经历

* 做的前端开发的项目

* 闭包什么时候使用？闭包的定义。怎么处理内存泄露的问题？

* 说一下promise的基本用法。resolve和reject的作用。多个数据源的请求数据回来之后再处理，怎么用promise实现。

* js中的面向对象和java的面向对象区别? [JavaScript面向对象 MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Introduction_to_Object-Oriented_JavaScript)

* 函数式编程和面向对象编程区别。es6中的剪头函数就是函数式编程的。函数式编程就是定义一个函数然后调用它，这种开发快速直接。面向对象的话关系逻辑清晰，维护方便简单。

* 快速排序，是稳定还是不稳定排序？

* 你有问题或要求？

## 2017-03-14 搜狐Offer

## 2017-03-10 搜狐二面

1. 自我介绍

2. 为什么做前端，对前端的理解，我看你的简历是偏向算法。

3. 前端的路你觉得应该怎么走，哪些地方你觉得不足，前端也有很多方向，你怎么看？

4. vue最常用的双向绑定是怎么实现的？

5. 有做过移动端的页面吗？

6. 布局这块儿有什么好的方案？flex有没有遇到什么问题？

7. webpack代码打包是怎么做的？内部代码合并压缩丑化是怎么实现的？[webpack源码学习](https://github.com/youngwind/blog/issues/99)

8. phantomjs原理是什么？模拟用户交互

9. 你自学前端你感觉自己哪方面还需要增强？

10. 你什么时间能来实习？你以后的打算？

11. 你有什么问题问我吗？

## 2017-03-10 爱奇艺Offer

## 2017-03-09 阿里

1. 在项目中的角色和核心产出。你原来的服务层是怎么做的？

2. vue的数据双向绑定实现原理？让你用原生js实现你怎么做？

3. MongoDB大量数据如何提升查询效率？索引？

4. 除了vue其它框架还有了解吗？你可以告诉我你的加分项还有什么？

5. SAP工作内容？

6. [MVC、MVVM](http://www.ruanyifeng.com/blog/2015/02/mvcmvp_mvvm.html) 是什么？

7. 优化页面加载速度的方法

8. 性能优化离线缓存技术。

9. ES6相对于ES5新添了哪些属性？[ES6新特性概览](http://www.cnblogs.com/Wayou/p/es6_new_features.html)

10. 前端范围需要扩大，多了解一些框架、技术，多理解为什么这么实现，而不是直接用现有的框架。



## 2017-03-08 搜狐焦点

* 自我介绍

* 你有让自己印象深刻的项目或经历吗？

* 对vue体验最深的点。你怎么知道虚拟dom数据更新了。

* 前端的缓存策略。我说了http缓存和localStorage，问localStorage可以存什么样的数据。

* 单链表判环。我说快慢指针，面试官问为什么这样就可以判断有环。

* 数组去重。我说了两种方法，让我分析时间空间复杂度。hash值怎么计算，js中是弱类型，hash值的计算会复杂一点。

* 实现最小栈，时间空间复杂度分析。时间复杂度不变，降低空间复杂度。

* 虚拟dom中，dom树不同时间的快照，比较两个快照之间的差异。两个快照不是完全不同，只是通过dom操作才发生的差异。然后问那如果是两个树比较是否相同，时间复杂度分析。

	[如何实现一个 Virtual DOM 算法](https://github.com/livoras/blog/issues/13)

	1. 用JS对象模拟DOM树

	2. 比较两棵虚拟DOM树的差异

	3. 把差异应用到真正的DOM树上

* 你有什么问题吗？

* 你对前端的学习计划。

## 2017-03-06 爱奇艺二面

* 你为什么做前端开发？你觉得你在前端方面最突出的技能是什么？

* 介绍一下参加ACM的情况

* 你在参赛的过程中，你在团队中起到的角色？团队中合作方式是怎么确定的？

* 在这两年的比赛中你觉得其中有多少想法是来自于你？

* 你之前有做过实习吗？

* 介绍一下调查问卷这个项目

* 问卷的数据怎么保存？

* 这个是练习做的项目还是有上线的成品？

* 你在实现过程中有遇到什么难题？怎么解决的？

* 你正在改进的多人管理是怎么设计的？

* 你当时学vue的时候为什么选择做问卷调查这个项目？

* 你c++怎么样？有项目经验吗？

* erp这个项目中你主要做了什么？

* 前端技术HTML5，CSS3，JS中你最擅长的是哪一个？框架方面接触过Vue和Node。

* 三个箱子，里面分别放着苹果、梨、苹果和梨，标签也是一样，但是标签和箱子不对应，你只可以打开其中一个箱子，而且不可以看里面，拿出里面一个水果看是什么，然后迅速判断三个箱子装着什么？应该是挑贴着苹果和梨的箱子。不管是挑贴着苹果或梨的箱子答案是一样的，都不可以唯一确定。

* 你有什么问题问我吗？

## 2017-03-01 乐视金融

* jquery获取父元素：parent获取直接父元素，parents获取祖先元素

* jquery绑定事件：on、bind、live

* jquery中empty和remove的区别：empty移除元素文本，remove移除dom结点

* 原生js遇到的兼容问题：我说了绑定事件

* 原生js移除一个结点：removeChild

* 列举CSS hack：我说-webkit,-moz。下划线是哪个浏览器特有的？
面试官说：CSS3新属性有些浏览器不支持就加浏览器私有属性。而CSShack解决兼容问题是：在IE下实现1像素高度，可以使用IE下特有的hack下划线‘_’,然后font-size:0。

* CSS3实现动画几种方式：transition、animation，两者区别。

* 做过HTML5移动端页面吗？@media媒体查询，[rem](http://www.alloyteam.com/2016/03/mobile-web-adaptation-tool-rem/#prettyPhoto)，百分比，弹性盒子。

* 前端的构建工具：webpack，列举几个loader，json-loader,css-loader

* 列举node的系统模块：http，fs(I/O操作)，system(管理与运行环境相关的属性)

* mysql和mongoDB的区别。数据库怎么选择。

* 介绍你写的爬虫系统。批量爬取被封ip怎么解决？


## 2017-02-24 爱奇艺一面

* 问下面代码输出什么
```
for(var i = 0; i < 5; i++){
	setTimeout(()=>{
		console.log(i);
	}, 0);
}//5 5 5 5 5
```
解释为什么会出现上面这种情况，我回答了执行栈

然后下面这段代码输出什么

```
for(let i = 0; i < 5; i++){
	setTimeout(()=>{
		console.log("three:", i);
	}, 0);
}//0 1 2 3 4
```

* 给下面这样一个树结构，输出树结点
```
{
  left: { ... },
  right: { ... },
  value: 1
}
```

我的代码
```
function logTree(node){
  if(node == null) return;
  logTree(node.left);
  console.log(node.value);
  logTree(node.right);
}
```
然后说这是左－根－右，中序遍历

给下面这颗树，用上面代码输出的结果是什么
```
var tree = {
  left: {
    left: { value: 3 },
    right: { value: 4 },
    value: 1
  },
  right: {
    left: { value: 5 },
    right: { value: 6 },
    value: 2
  },
  value: 0
}
```
画了下树结构，输出3 1 4 0 5 2 6

* 介绍vue和vuex：

	[vue](http://www.csdn.net/article/1970-01-01/2825439)：组件化开发，[虚拟dom](http://www.cnblogs.com/lvyongbo/p/5931636.html)、数据双向绑定

	[vuex](http://vuex.vuejs.org/zh-cn/intro.html): 集中存储所有组件共享的状态。当有多个组件共享状态时，把共享状态抽取出来集中管理。

* webpack和其它打包工具比好在哪儿？

	webpack是一个模块化加载器兼打包工具，同时支持AMD和CMD加载规范。优势是：

1. 代码分割

	webpack支持两种依赖加载：同步和异步。同步的依赖会在编译时直接打包输出到目的文件中；异步的依赖会单独生成一个代码块，只有在浏览器中运行需要的时候才会异步加载该代码块。

2. Loaders

	在默认情况下，webpack只能处理JS文件，但是通过加载器我们可以将其他类型的资源转换为JS输出。

3. 插件机制

	webpack提供了强大的插件系统，当webpack内置的功能不能满足我们的构建需求时，我们可以通过使用插件来提高工作效率。

* 介绍mongodb

1. MongoDB的提供了一个面向文档存储，操作起来比较简单和容易。
2. 你可以在MongoDB记录中设置任何属性的索引 (如：FirstName="Sameer",Address="8 Gandhi Road")来实现更快的排序。
3. 你可以通过本地或者网络创建数据镜像，这使得MongoDB有更强的扩展性。
4. 如果负载的增加（需要更多的存储空间和更强的处理能力） ，它可以分布在计算机网络中的其他节点上这就是所谓的分片。
5. Mongo支持丰富的查询表达式。查询指令使用JSON形式的标记，可轻易查询文档中内嵌的对象及数组。
6. MongoDb 使用update()命令可以实现替换完成的文档（数据）或者一些指定的数据字段 。
7. Mongodb中的Map/reduce主要是用来对数据进行批量处理和聚合操作。
8. Map和Reduce。Map函数调用emit(key,value)遍历集合中所有的记录，将key与value传给Reduce函数进行处理。
9. Map函数和Reduce函数是使用Javascript编写的，并可以通过db.runCommand或mapreduce命令来执行MapReduce操作。
10. GridFS是MongoDB中的一个内置功能，可以用于存放大量小文件。
11. MongoDB允许在服务端执行脚本，可以用Javascript编写某个函数，直接在服务端执行，也可以把函数的定义存储在服务端，下次直接调用即可。
12. MongoDB支持各种编程语言:RUBY，PYTHON，JAVA，C++，PHP，C#等多种语言。
13. MongoDB安装简单。

## 2017-02-14 粉笔网

* CSS中相对定位与绝对定位的区别
```
relative相对元素本身位置定位
absolute相对于父元素position:relative，如果没有相对于body定位

绝对定位脱离文档流
```

* display的属性：block、inline、inline-block区别。还有什么属性，我说了flex，然后简单介绍flex。

* 对JS原型有什么了解，用原型创建对象和普通new一个对象有什么区别。
```
创建对象的三种方法：工厂模式、构造函数、原型

工厂模式——无法判断对象类型：
function person(name){
	var o = new Object();
	o.name = name;
	return o;
}

构造函数模式——每个方法都要在每个实例上重新创建一遍：
function Person(name){
	this.name = name;
	this.sayName = function(){alert(this.name);}
}
var person = new Person("kad");
alert(person.constructor == Person); // true
alert(person instanceof Person); // true
不同实例的同名函数是不相等的

原型——使用原型对象的好处是可以让所有对象实例共享它所包含的属性和方法
function Person(){}
Person.prototype.name = "kad";
Person.prototype.sayName = function(){alert(this.name);}
var person = new Person();
```

* JS继承的几种方法
```
1. 原型链继承：将父类的实例作为子类的原型
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

缺点：所有子类共享父类实例，如果某个子类修改了父类，其他子类继承会出问题。
在构造子类型实例时不能给父类传参

2. 构造函数继承
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

缺点：无法继承父类原型链上的属性和方法。

3. call、apply实现继承
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

* 怎么理解闭包，闭包用在什么地方
```
读取函数内部变量
让变量值始终保存在内存中
```

* 怎么理解同步异步。然后问谁来执行当前任务，我说了执行栈和任务队列。又问你认为ajax是在js线程中执行的？怎么理解线程？我说每次只做一件事情。
```
同步：阻塞
异步：非阻塞
```

* 支付宝pc端支付时出现二维码，手机端扫描二维码支付成功后，pc端会跳转到支付成功页面，如何实现的？
```
手机端支付成功返回给服务器一个标识，而pc端二维码页面加载完成，http连接会断开，这时服务器怎么把支付成功的标识返回给pc端？
我说了ajax轮询（这个方法服务器压力大），html5的websocket
```
然后接着问websocket和http的keep-alive的区别。
```
http长连接需要每次发送header
```

* http协议中get和post的区别

* 记住密码自动登录怎么实现？cookie

* [vue数据双向绑定实现机制](https://segmentfault.com/a/1190000006599500)

* CSS3中transition（过渡）和transform（变形）的区别

* AngularJS有没有学过


