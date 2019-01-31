---
title: Base64
date: 2018-08-04 14:58:00
categories: 
- Front-End
tags: 
- JavaScript
---

## Base64

Base64是一种基于64个可打印（可见）字符来表示二进制数据的表示方式。

用记事本去打开`exe`、`jpg`等文件时，会看到一大堆乱码，这是因为二进制文件中包含很多无法显示和打印的字符（计算机中任何数据都是按ascii码存储的，而ascii码的128个字符中有32个是不可见字符）。世界上存在着多种编码方式，同一个二进制数字可以被解释成不同的符号。因此，要想打开一个文本文件，就必须知道它的编码方式，否则用错误的编码方式解读，就会出现乱码。Base64是一种任意二进制到文本字符串的编码方法，常用于在URL、Cookie、网页中传输少量二进制数据。

<!-- more -->

Base64的可打印字符包括有`A-Z`、`a-z`、`0-9`、`+`、`/`共64个字符（还有一个垫字符`=`）。Base64编码就是将其他所有符号都转换成这个字符集中的字符。

对二进制数据进行处理，每3个字节一组，一共`3*8=24`bit，将其以`6bit`进行切分划分为4组，如图。这样每`6`个二进制位的十进制值就可以得到，也就是ASCII码值。

![](./base64.jpg)

当字节数不足3时，两个字节的情况，二进制后面补两个0，得到的Base64编码末尾补一个`=`；一个字节的情况，二进制后面补四个0，得到的Base64编码末尾补两个`=`。

## ArrayBuffer

JS中提供`ArrayBuffer`对象代表储存二进制数据的一段内存，它不能直接读写，只能通过视图（`TypedArray`视图和`DataView`视图)来读写，视图的作用是以指定格式解读二进制数据。

一个场景需求是将二进制数据进行Base64编码，因为ArrayBuffer的兼容性，不能使用，但是ArrayBuffer的视图读写方式提供了思路，如何通过整数来处理二进制。维护的数组`view`其实相当于是ArrayBuffer的一个视图，数组中每个值都不超过`2^8`，也就是二进制位不超过`8`位，当然数组需要提前计算好需要开多大且每个值置为`0`。对于需要将数据的二进制形式放在第`i~j`位之间的操作，就转变成将数据的每个二进制位塞到数组`view`的第`index / 8`个值的第`index % 8`个二进制位的操作。

```
var view = [];
for(var i = 0; i < len; i++) view[i] = 0;

function getBoolNum(num){
    if(!!x) return 1;
    return 0;
}
function setBit(bitId, bit){
    var index = parseInt(bitId / 8);
    var mod = bitId % 8;
    if(getBoolNum(view[index] & (1 << mod)) == bit) return;
    view[index] ^= (1 << mod);
}
function setVal(bitBegin, bitEnd, val){
    for(var i = 0; i < bitEnd - bitBegin; i++){
        setBit(i + bitBegin, (val >> i) & 1);
    }
}
```

处理得到记录了二进制数据的数组`view`，通过转换为字符串（这样得到的字符串打印出来一般会是乱码，所以才需要Base64编码）来进行Base64编码。

```
var str = '';
for(var i = 0; i < view.length; i++){
    str += String.fromCharCode(view[i]);
}
window.btoa(str);
```

## 在URL中的应用

采用Base64编码因为比较简短，同时不具有可读性，可用于在HTTP环境下传递较长的标识信息。然而，标准的Base64并不适合直接放在URL里传输，因为URL编码器会把标准Base64中的`/`和`+`字符变为形如`%XX`的形式，而这些`%`号在存入数据库时还需要再进行转换，因为SQL中已将`%`号用作通配符。

为解决此问题，可采用一种**用于URL的改进Base64**编码，它不在末尾填充`=`号，并将标准Base64中的`+`和`/`分别改成了`-`和`_`，这样就免去了在URL编解码和数据库存储时所要作的转换，避免了编码信息长度在此过程中的增加，并统一了数据库、表单等处对象标识符的格式。

```
function encodeUrl(str){
    return str.replace(/\+/g, '-').replace(/\//g, '_').replace(/\=+$/, '');
}
function decodeUrl(str){
    var exLen = (4 - str.length % 4) % 4;
    str = (str + '===').slice(0, str.length + exLen);
    return str.replace(/-/g, '+').replace(/_/g, '/');
}
```

为什么URL需要编码？因为网络标准RFC有硬性规定只有`[0-9a-zA-Z]`以及一些特殊符号`$-_+!*()`等才可以不经过编码直接用于URL。同时，由于在URL编码中空格与加号是等价的，因为空格无法表示会替换为加号，所以也需要对加号进行处理。

### Reference

[字符编码笔记：ASCII，Unicode 和 UTF-8](http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html)

[Base64笔记](http://www.ruanyifeng.com/blog/2008/06/base64.html)