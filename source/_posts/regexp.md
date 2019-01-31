---
title: RegExp
date: 2017-07-17 21:58:41
categories: 
- Front-End
tags:
- JavaScript
---


最近遇到几个很类似的题目，都用到正则做字符串操作，就想总结一下。

## 知识点

### 正则创建方式

1. `var reg = new RegExp(/are/, 'g');`

2. `var reg = /are/i;`

注意使用构造函数时字符的转义规则。


### 正则模式匹配符

`\g`：在字符串中查找所有可能的匹配，返回结果是多个

`\i`：匹配时不区分大小写

`\m`：多行匹配

<!-- more -->

### 正则匹配查询：

1. `str.search(reg)`返回值为str中第一个与reg匹配的位置，不匹配返回-1

2. `str.match(reg)`返回匹配数组，依赖全局标志g

3. `reg.exec(str)`返回一个数组，数组里存储的是第一个匹配项的相关信息

4. `reg.test(str)`返回值Boolean


### 正则常用语法

#### 特殊代码

代码 | 说明
---|---
. | 匹配除换行符以外的任意字符
\w | 匹配字母或数字
\s | 匹配任意的空白符
\d | 匹配数字
\b | 匹配单词的开始或结束
^ | 匹配字符串的开始
$ | 匹配字符串的结束

#### 重复

代码 | 说明
---|---
* | 重复零次或更多次
+  | 重复一次或更多次
? | 重复零次或一次
{n} | 重复n次
{n,} | 重复n次或更多次
{n,m} | 重复n到m次

#### 反义

代码 | 说明
---|---
\W | 匹配任意不是字母和数字的字符
\S | 匹配任意不是空白符的字符
\D | 匹配任意非数字的字符
\B | 匹配不是单词开头或结束的位置
[^x] | 匹配除了x以外的任意字符
[^aeiou] | 匹配除了aeiou这几个字母以外的任意字符

## Regex Golf

### Warmup

![](regexp.png)

### Anchors

可以发现需要match的字符串都以ick为结尾

![](regexp1.png)

### It never ends

不允许通过`$`匹配字符串结尾，可以用`\b`来匹配单词的开头和结尾

![](regexp2.png)

### Ranges

考察字母的范围以及字符串的边界

![](regexp3.png)

### Backrefs

考察反向引用，可以发现，开头出现的三个字母之后还会出现，后向引用用于重复搜索前面某个分组匹配的文本。例如，`\1`代表分组1匹配的文本。

![](regexp4.png)

### Abba

可以发现要求不要match类似abba这样的字符串，先构造abba，还是用到后向引用`(\w)(\w)\2\1`，不匹配就用到零宽断言`(?!exp)`，匹配后面不是exp的串。然而`(?!(\w)(\w)\2\1)`只匹配了左边的，右边的也匹配上了。。。不应该match的串是`xxxabbaxxx`这样子的，那么应该就是`^(?!.*(\w)(\w)\2\1)`。

![](regexp5.png)

### 持续更新...

## 有关正则的题目

### 题目一

前不久同学问了一个问题，**将一个字符串中所有中文符号转为对应的英文符号。**

```
var str = "（孔）。，）";
console.log(str);
var obj = {
	'（': '(',
	'）': ')',
	'，': ',',
	'。': '.'
};
for(var key in obj){
	if(str.indexOf(key) != -1){
		str = str.replace(new RegExp('\\' + key, 'g'), obj[key]);
	}
}
console.log(str);
```

### 题目二

**按所给的时间格式输出指定的时间。**

格式说明:
```
对于 2014.09.05 13:14:20
yyyy: 年份，2014
yy: 年份，14
MM: 月份，补满两位，09
M: 月份, 9
dd: 日期，补满两位，05
d: 日期, 5
HH: 24制小时，补满两位，13
H: 24制小时，13
hh: 12制小时，补满两位，01
h: 12制小时，1
mm: 分钟，补满两位，14
m: 分钟，14
ss: 秒，补满两位，20
s: 秒，20
w: 星期，为 ['日', '一', '二', '三', '四', '五', '六'] 中的某一个，本 demo 结果为 五
```

输入:
```
new Date(1409894060000), 'yyyy-MM-dd HH:mm:ss 星期w'
```

输出:
```
2014-09-05 13:14:20 星期五
```

```
function formatDate(d, sFormation) {
    var obj = {
        yyyy: d.getFullYear(),
        yy: d.getFullYear().toString().slice(-2),
        MM: ('0' + (d.getMonth()+1)).slice(-2),
        M: d.getMonth()+1,
        dd: ('0' + d.getDate()).slice(-2),
        d: d.getDate(),
        HH: ('0' + d.getHours()).slice(-2),
        H: d.getHours(),
        hh: ('0' + d.getHours() % 12).slice(-2),
        h: d.getHours() % 12,
        mm: ('0' + d.getMinutes()).slice(-2),
        m: d.getMinutes(),
        ss: ('0' + d.getSeconds()).slice(-2),
        s: d.getSeconds(),
        w: ['日','一','二','三','四','五','六'][d.getDay()]
    }
    return sFormation.replace(/([a-z]+)/ig, function(match, p1){
    	console.log(match, p1);
    	return obj[p1]
    });
}
```

### 题目三

**实现模版解析函数。**

```
var str = 'I am {name}，my job is {job}.';
var obj = {
	name: 'kad',
	job: 'Front-End Development'
};
function tmpl(str, obj){
	return str.replace(/{([^}]+)}/ig, function(match, $1){
		console.log(match, $1);
		return obj[$1];
	})
}
console.log(tmpl(str, obj));
```

### 题目四

**CSS中经常有类似`background-image`这种通过`-`连接的字符，通过JS设置样式的时候需要将这种样式转换成backgroundImage驼峰格式，请完成此转换功能**

1. 以`-`为分隔符，将第二个起的非空单词首字母转为大写
2. `-webkit-border-image`转换后的结果为 webkitBorderImage

```
function cssStyle2DomStyle(sName) {
    var arr = sName.split('-');
    var mark = false;
    var ans = '';
    for(var i = 0; i < arr.length; i++){
        if(arr[i].trim()){
            if(!mark) {
                mark = true;
                ans += arr[i];
            }else{
                ans += arr[i].charAt(0).toUpperCase() + arr[i].slice(1);
            }
        }
    }
    return ans;
}
```

```
function cssStyle2DomStyle(sName) {
    return sName.replace(/(?!^)\-(\w)(\w+)/g, function(a, b, c){
            return b.toUpperCase() + c.toLowerCase();
        }).replace(/^\-/, '');
}
```

### 题目五

**将 rgb 颜色字符串转换为十六进制的形式，如`rgb(255, 255, 255)` 转为 `#ffffff`.**

```
function rgb2hex(sRGB) {
    var reg = /rgb\((\d+),\s*(\d+),\s*(\d+)\)/;
    var arr = sRGB.match(reg);
    console.log(arr);
    var ans = '#';
    if(arr){
        for(var i = 1; i < arr.length; i++){
            var num = parseInt(arr[i]);
            ans += num < 16 ? ('0' + num) : num.toString(16);   
        }
    }else{
        ans = sRGB;
    }
    return ans;
}
```


## Reference

[正则表达式30分钟入门教程](https://deerchao.net/tutorials/regex/regex-1.htm)

[RegExp MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp)

[Regex Golf](https://alf.nu/RegexGolf)

[replace MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace)

