---
title: MD5
date: 2017-10-17 22:45:46
categories: 
- Front-End
tags:
- JavaScript
---

**MD5(Message Digest Algorithm 5)**是一种信息摘要算法，它可以从任意长度的明文字符串生成128位的哈希值，是不可逆的。

## MD5算法原理：

### 处理原文

计算原文长度(bit)对512取余的结果，如果不等于448，就需要填充原文使得原文对512求余的结果等于448。填充的方法是第一位填1，其余位填0。填充后，信息长度为512*N+448(N为每512位为一组的组数)。用剩余位置(512-448=64位)记录原文的真正长度，将长度的二进制值补在最后。信息长度变为512(N+1)。

### 设置初始值

MD5的哈希结果长度为128位，按每32位一组分成4组，这4组结果是由4个初始值A、B、C、D经过不断演变得到。MD5的官方实现中这4个32位的整数参数称为链接变量，设置如下：

<!-- more -->

```
A=0x67452301
B=0xefcdab89
C=0x98badcfe
D=0x10325476
```

按小端规则，数据在内存中的排列就是：`01 23 45 67 89 ab cd ... 32 10`。

### 循环加工

1. 循环的次数是分组的个数(N+1)

2. 官方MD5所用到的函数有四种非线性函数：每次循环有四轮运算，每轮16次，F、G、H、I交替使用。

	```
	F(X, Y, Z) =(X&Y) | ((~X) & Z)
	G(X, Y, Z) =(X&Z) | (Y & (~Z))
	H(X, Y, Z) =X^Y^Z
	I(X, Y, Z)=Y^(X|(~Z))
	```

3. 四种操作：

	```
	FF(a, b, c, d, M[j], s, Ki)表示 a = b + ((a + F(b, c, d) + Mj + Ki) <<< s)
	GG(a, b, c, d, M[j], s, Ki)表示 a = b + ((a + G(b, c, d) + Mj + Ki) <<< s)
	HH(a, b, c, d, M[j], s, Ki)表示 a = b + ((a + H(b, c, d) + Mj + Ki) <<< s)
	II(a, b, c, d, M[j], s, Ki)表示 a = b + ((a + I(b, c, d) + Mj + Ki) <<< s)
	```

4. Mi是第一步处理后的原文，原文长度是512的整数倍，把每512位再分成16等分，命名为M0~M15，每轮运算中交替用到M0~M15。

5. Ki是常量，每次运算都不相同。

6. 左移S位，S也是常量，每次运算都不相同。

7. 每次循环后将ABCD分别加上abcd，然后进入下一循环。
    
### 拼接结果

把循环加工最终产生的ABCD四个值拼接在一起转换成字符串即可。

## JS实现MD5算法

```
/**
 *
 * MD5 (Message-Digest Algorithm)
 *
 **/
var MD5 = function(string) {
	function RotateLeft(lValue, iShiftBits) {
		return (lValue << iShiftBits) | (lValue >>> (32 - iShiftBits));
	}
	function AddUnsigned(lX, lY) {
		var lX4, lY4, lX8, lY8, lResult;
		lX8 = (lX & 0x80000000);
		lY8 = (lY & 0x80000000);
		lX4 = (lX & 0x40000000);
		lY4 = (lY & 0x40000000);
		lResult = (lX & 0x3FFFFFFF) + (lY & 0x3FFFFFFF);
		if (lX4 & lY4) {
			return (lResult ^ 0x80000000 ^ lX8 ^ lY8);
		}
		if (lX4 | lY4) {
			if (lResult & 0x40000000) {
				return (lResult ^ 0xC0000000 ^ lX8 ^ lY8);
			} else {
				return (lResult ^ 0x40000000 ^ lX8 ^ lY8);
			}
		} else {
			return (lResult ^ lX8 ^ lY8);
		}
	}
	function F(x, y, z) {
		return (x & y) | ((~x) & z);
	}
	function G(x, y, z) {
		return (x & z) | (y & (~z));
	}
	function H(x, y, z) {
		return (x ^ y ^ z);
	}
	function I(x, y, z) {
		return (y ^ (x | (~z)));
	}
	function FF(a, b, c, d, x, s, ac) {
		a = AddUnsigned(a, AddUnsigned(AddUnsigned(F(b, c, d), x), ac));
		return AddUnsigned(RotateLeft(a, s), b);
	};
	function GG(a, b, c, d, x, s, ac) {
		a = AddUnsigned(a, AddUnsigned(AddUnsigned(G(b, c, d), x), ac));
		return AddUnsigned(RotateLeft(a, s), b);
	};
	function HH(a, b, c, d, x, s, ac) {
		a = AddUnsigned(a, AddUnsigned(AddUnsigned(H(b, c, d), x), ac));
		return AddUnsigned(RotateLeft(a, s), b);
	};
	function II(a, b, c, d, x, s, ac) {
		a = AddUnsigned(a, AddUnsigned(AddUnsigned(I(b, c, d), x), ac));
		return AddUnsigned(RotateLeft(a, s), b);
	};
	function ConvertToWordArray(string) {
		var lWordCount;
		var lMessageLength = string.length;
		var lNumberOfWords_temp1 = lMessageLength + 8;
		var lNumberOfWords_temp2 = (lNumberOfWords_temp1 - (lNumberOfWords_temp1 % 64)) / 64;
		var lNumberOfWords = (lNumberOfWords_temp2 + 1) * 16;
		var lWordArray = Array(lNumberOfWords - 1);
		var lBytePosition = 0;
		var lByteCount = 0;
		while (lByteCount < lMessageLength) {
			lWordCount = (lByteCount - (lByteCount % 4)) / 4;
			lBytePosition = (lByteCount % 4) * 8;
			lWordArray[lWordCount] = (lWordArray[lWordCount] | (string.charCodeAt(lByteCount) << lBytePosition));
			lByteCount++;
		}
		lWordCount = (lByteCount - (lByteCount % 4)) / 4;
		lBytePosition = (lByteCount % 4) * 8;
		lWordArray[lWordCount] = lWordArray[lWordCount] | (0x80 << lBytePosition);
		lWordArray[lNumberOfWords - 2] = lMessageLength << 3;
		lWordArray[lNumberOfWords - 1] = lMessageLength >>> 29;
		return lWordArray;
	};
	function WordToHex(lValue) {
		var WordToHexValue = "",
			WordToHexValue_temp = "",
			lByte, lCount;
		for (lCount = 0; lCount <= 3; lCount++) {
			lByte = (lValue >>> (lCount * 8)) & 255;
			WordToHexValue_temp = "0" + lByte.toString(16);
			WordToHexValue = WordToHexValue + WordToHexValue_temp.substr(WordToHexValue_temp.length - 2, 2);
		}
		console.log(lValue, WordToHexValue);
		return WordToHexValue;
	};
	function Utf8Encode(string) {
		string = string.replace(/\r\n/g, "\n");
		var utftext = "";
		for (var n = 0; n < string.length; n++) {
			var c = string.charCodeAt(n);
			if (c < 128) {
				utftext += String.fromCharCode(c);
			} else if ((c > 127) && (c < 2048)) {
				utftext += String.fromCharCode((c >> 6) | 192);
				utftext += String.fromCharCode((c & 63) | 128);
			} else {
				utftext += String.fromCharCode((c >> 12) | 224);
				utftext += String.fromCharCode(((c >> 6) & 63) | 128);
				utftext += String.fromCharCode((c & 63) | 128);
			}
		}
		return utftext;
	};
	var x = Array();
	var k, AA, BB, CC, DD, a, b, c, d;
	var S11 = 7,
		S12 = 12,
		S13 = 17,
		S14 = 22;
	var S21 = 5,
		S22 = 9,
		S23 = 14,
		S24 = 20;
	var S31 = 4,
		S32 = 11,
		S33 = 16,
		S34 = 23;
	var S41 = 6,
		S42 = 10,
		S43 = 15,
		S44 = 21;
	string = Utf8Encode(string);
	x = ConvertToWordArray(string);
	a = 0x67452301;
	b = 0xEFCDAB89;
	c = 0x98BADCFE;
	d = 0x10325476;
	for (k = 0; k < x.length; k += 16) {
		AA = a;
		BB = b;
		CC = c;
		DD = d;
		a = FF(a, b, c, d, x[k + 0], S11, 0xD76AA478);
		d = FF(d, a, b, c, x[k + 1], S12, 0xE8C7B756);
		c = FF(c, d, a, b, x[k + 2], S13, 0x242070DB);
		b = FF(b, c, d, a, x[k + 3], S14, 0xC1BDCEEE);
		a = FF(a, b, c, d, x[k + 4], S11, 0xF57C0FAF);
		d = FF(d, a, b, c, x[k + 5], S12, 0x4787C62A);
		c = FF(c, d, a, b, x[k + 6], S13, 0xA8304613);
		b = FF(b, c, d, a, x[k + 7], S14, 0xFD469501);
		a = FF(a, b, c, d, x[k + 8], S11, 0x698098D8);
		d = FF(d, a, b, c, x[k + 9], S12, 0x8B44F7AF);
		c = FF(c, d, a, b, x[k + 10], S13, 0xFFFF5BB1);
		b = FF(b, c, d, a, x[k + 11], S14, 0x895CD7BE);
		a = FF(a, b, c, d, x[k + 12], S11, 0x6B901122);
		d = FF(d, a, b, c, x[k + 13], S12, 0xFD987193);
		c = FF(c, d, a, b, x[k + 14], S13, 0xA679438E);
		b = FF(b, c, d, a, x[k + 15], S14, 0x49B40821);
		a = GG(a, b, c, d, x[k + 1], S21, 0xF61E2562);
		d = GG(d, a, b, c, x[k + 6], S22, 0xC040B340);
		c = GG(c, d, a, b, x[k + 11], S23, 0x265E5A51);
		b = GG(b, c, d, a, x[k + 0], S24, 0xE9B6C7AA);
		a = GG(a, b, c, d, x[k + 5], S21, 0xD62F105D);
		d = GG(d, a, b, c, x[k + 10], S22, 0x2441453);
		c = GG(c, d, a, b, x[k + 15], S23, 0xD8A1E681);
		b = GG(b, c, d, a, x[k + 4], S24, 0xE7D3FBC8);
		a = GG(a, b, c, d, x[k + 9], S21, 0x21E1CDE6);
		d = GG(d, a, b, c, x[k + 14], S22, 0xC33707D6);
		c = GG(c, d, a, b, x[k + 3], S23, 0xF4D50D87);
		b = GG(b, c, d, a, x[k + 8], S24, 0x455A14ED);
		a = GG(a, b, c, d, x[k + 13], S21, 0xA9E3E905);
		d = GG(d, a, b, c, x[k + 2], S22, 0xFCEFA3F8);
		c = GG(c, d, a, b, x[k + 7], S23, 0x676F02D9);
		b = GG(b, c, d, a, x[k + 12], S24, 0x8D2A4C8A);
		a = HH(a, b, c, d, x[k + 5], S31, 0xFFFA3942);
		d = HH(d, a, b, c, x[k + 8], S32, 0x8771F681);
		c = HH(c, d, a, b, x[k + 11], S33, 0x6D9D6122);
		b = HH(b, c, d, a, x[k + 14], S34, 0xFDE5380C);
		a = HH(a, b, c, d, x[k + 1], S31, 0xA4BEEA44);
		d = HH(d, a, b, c, x[k + 4], S32, 0x4BDECFA9);
		c = HH(c, d, a, b, x[k + 7], S33, 0xF6BB4B60);
		b = HH(b, c, d, a, x[k + 10], S34, 0xBEBFBC70);
		a = HH(a, b, c, d, x[k + 13], S31, 0x289B7EC6);
		d = HH(d, a, b, c, x[k + 0], S32, 0xEAA127FA);
		c = HH(c, d, a, b, x[k + 3], S33, 0xD4EF3085);
		b = HH(b, c, d, a, x[k + 6], S34, 0x4881D05);
		a = HH(a, b, c, d, x[k + 9], S31, 0xD9D4D039);
		d = HH(d, a, b, c, x[k + 12], S32, 0xE6DB99E5);
		c = HH(c, d, a, b, x[k + 15], S33, 0x1FA27CF8);
		b = HH(b, c, d, a, x[k + 2], S34, 0xC4AC5665);
		a = II(a, b, c, d, x[k + 0], S41, 0xF4292244);
		d = II(d, a, b, c, x[k + 7], S42, 0x432AFF97);
		c = II(c, d, a, b, x[k + 14], S43, 0xAB9423A7);
		b = II(b, c, d, a, x[k + 5], S44, 0xFC93A039);
		a = II(a, b, c, d, x[k + 12], S41, 0x655B59C3);
		d = II(d, a, b, c, x[k + 3], S42, 0x8F0CCC92);
		c = II(c, d, a, b, x[k + 10], S43, 0xFFEFF47D);
		b = II(b, c, d, a, x[k + 1], S44, 0x85845DD1);
		a = II(a, b, c, d, x[k + 8], S41, 0x6FA87E4F);
		d = II(d, a, b, c, x[k + 15], S42, 0xFE2CE6E0);
		c = II(c, d, a, b, x[k + 6], S43, 0xA3014314);
		b = II(b, c, d, a, x[k + 13], S44, 0x4E0811A1);
		a = II(a, b, c, d, x[k + 4], S41, 0xF7537E82);
		d = II(d, a, b, c, x[k + 11], S42, 0xBD3AF235);
		c = II(c, d, a, b, x[k + 2], S43, 0x2AD7D2BB);
		b = II(b, c, d, a, x[k + 9], S44, 0xEB86D391);
		a = AddUnsigned(a, AA);
		b = AddUnsigned(b, BB);
		c = AddUnsigned(c, CC);
		d = AddUnsigned(d, DD);
	}
	var temp = WordToHex(a) + WordToHex(b) + WordToHex(c) + WordToHex(d);
	return temp.toLowerCase();
}
```

## MD5应用

### 一致性验证

MD5的典型应用是对一段文本信息产生信息摘要，以防止被篡改。常常在某些软件下载站点的某软件信息中看到其MD5值，它的作用就在于我们可以在下载该软件后，对下载回来的文件用专门的软件（如Windows MD5 Check等）做一次MD5校验，以确保我们获得的文件与该站点提供的文件为同一文件。

### 数字证书

如果有一个第三方的认证机构，用MD5还可以防止文件作者的“抵赖”，这就是所谓的数字签名应用。

### 安全访问认证

在Unix系统中用户的密码是以MD5（或其它类似的算法）经Hash运算后存储在文件系统中。当用户登录的时候，系统把用户输入的密码进行MD5 Hash运算，然后再去和保存在文件系统中的MD5值进行比较，进而确定输入的密码是否正确。通过这样的步骤，系统在并不知道用户密码的明码的情况下就可以确定用户登录系统的合法性。

## MD5破解

所谓的破解并非把摘要还原成原文，对MD5的破解实际上都属于碰撞。比如原文A通过MD5可以生成摘要M，我们并不需要把M还原成A，只需要找到原文B，生成同样的摘要M即可。

设MD5的哈希函数是H(x)，那么：
```
H(A)= M
H(B)=M
```
任意B即为破解结果。

**MD5碰撞的方法有很多，主要包括暴力枚举法、字典法、彩虹表法等等。**

暴力枚举就是枚举出所有原文并计算哈希值，对比与给定的信息摘要是否一致。时间复杂度太高。

字典法就是存储尽可能多的原文和对应的哈希值，这样可以快速找到碰撞结果，但是需要巨大的空间存储。

### 彩虹表

两个基本函数：

* H(X)：生成信息摘要的哈希函数，比如MD5
* R(X)：从信息摘要转换成另一个字符串的衰减函数(Reduce)。其中R(X)的定义域是H(X)的值域，R(X)的值域是H(X)的定义域。注意R(X)并非H(X)的反函数。

通过交替运算H和R若干次，可以形成一个原文和哈希值的链条。假设原文为`aaaaaa`，哈希值长度32bit，那么哈希链表如下：

![h & r](/h&r.png)

假设H(X)和R(X)的交替重复K次，链条长度为2K+1，我们只需把链表的首部和尾部存入哈希表中。

演示一次破解过程：

> 给定信息摘要：`920ECF10`，进行R(X)运算得到`kiebgt`，查询哈希表可以找到末端`kiebgt`对应的首端是`aaaaaa`，因此摘要`920ECF10`的原文“极有可能”在`aaaaaa`到`kiebgt`的这个链条当中。

接下来从`aaaaaa`开始，重新交替运算R(X)与H(X)，看一看摘要值920ECF10是否是其中一次H(X)的结果。从链条看来，答案是肯定的，因此`920ECF10`的原文就是`920ECF10`的前置节点`sgfnyd`。

需要补充的是，如果给定的摘要值经过一次R(X)运算，结果在哈希表中找不到，可以继续交替H(X) R(X)直到第K次为止。如果重复了K次之后，仍然没有再末节点中找到对应的值，则可以断定所需明文不在集合中。再计算无意义。

哈希链表代表了一组映射关系，其中每组包含K对映射，但只需要存储链条首位两个字符串。假设K=10，那么存储空间只有上述字典的十分之一，代价则是破解一个摘要的运算次数提高了十倍。

但是哈希链条存在一个致命的缺陷：R(X)函数的可靠性，虽然尽量吧R(X)设计成结果均匀分布的函数，但是再完美的函数也难免有碰撞的情况，比如如下情况：

> 给定信息摘要：`FB107E70`，经过多次R(X)，H(X)运算，得到结果`kiebgt`，通过哈希表查找末端`kiebgt`，可以找出首端`aaaaaa`。

但是，`FB107E70`并不在`aaaaaa`到`kiebgt`的哈希链条当中，这就是R(X)的碰撞造成的。

这个问题看似没什么影响，既然找不到就重新生成一组首尾映射即可。但是想象一下，当K值较大的时候，哈希链很长，一旦两条不同的哈希链在某个节点出现碰撞，后面所有的明文和哈希值全都变成了一毛一样的值。

这样造成的后果就是冗余存储。原本两条哈希链可以存储2K个映射，由于重复，真正存储的映射数量不足2K。

由此彩虹表诞生，彩虹表对哈希链进行了改进，把原先R(X)函数改进成了R1(X)到R(X)一共k个衰减函数。这样一来虽然也可能发生碰撞，但是碰撞只会发生在同一级运算，如R1和R1碰撞，R3和R3碰撞，大大减小了存储重复的几率。

![rainbow](/rainbow.png)



## Reference

[MD5 Wiki](https://en.wikipedia.org/wiki/MD5)

[MD5算法——梦见微信公众号](https://mp.weixin.qq.com/s/k-ToL356asWtS_PN30Z17w)

[MD5算法步骤详解](http://blog.sina.com.cn/s/blog_6fe0eb1901014cpl.html)

[如何给自己各种帐号编一个安全又不会忘记的密码？](https://www.zhihu.com/question/38369521)

[彩虹表](https://www.zhihu.com/question/19790488)

[计算机的大小端规则](http://blog.csdn.net/github_35681219/article/details/52743048)