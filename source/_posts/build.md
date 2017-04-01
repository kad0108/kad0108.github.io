---
title: 完善博客功能
date: 2017-01-26 23:08:32
categories: 
- Geek
tags: 
---

## 域名绑定

使用Hexo搭建了静态博客，托管到了github上可通过[kad0108.github.io](https://kad0108.github.io/)访问。然而GitHub 屏蔽了百度爬虫，GitHub Pages 中的内容无法被百度索引。

然后自己也申请了域名，下面是github绑定域名时间——————

1. source文件夹中新建CNAME文件（无后缀名），首行添加域名，我的是[kadong.space](http://kadong.space/)，注意前面不加`http://或www`，然后使用`hexo g && hexo d`部署。

2. 在域名解析提供商，我的域名是在万网购买。步骤：进入工作台－》域名－》解析－》域名解析－》解析设置－》添加解析。

<!-- more -->

![domain](/build/domain.png)

## 评论功能

Hexo评论模块支持Disqus, 多说和友言。

[创建多说站点](http://duoshuo.com/create-site/)，然后后面[配置](http://dev.duoshuo.com/threads/541d3b2b40b5abcd2e4df0e9)会用到站点名称。

## 留言板功能

参考[这里](http://blog.csdn.net/shenshanlaoyuan/article/details/52774473)，我把留言板功能和关于我页面集合在一起。

在多说控制台，修改多说CSS

```
#ds-recent-visitors .ds-avatar {
	display: inline;
	padding: 0 !important;
	margin: 4px !important;
}
#ds-recent-visitors div img {
    display: inline-block !important;
    width: 56px !important;
    height: 56px !important;
    border-radius: 50%;
    border: 1px solid #ddd;
    padding: 2px;
    /* box-shadow: 1px 1px 1px rgba(0,0,0, .15); */
}
```


## 音乐功能

使用网易云音乐，生成外链播放器，后续继续探索如何集成多个歌曲。



<br>