---
title: 关于页面间传布尔值的坑
date: 2021-11-26 18:45:51
tags: 浏览器
categories: Web开发 
---

# 问题描述

今天遇到了一个问题，写微信小程序时wx:if没有起作用，代码如下

```
<---wxml--->
<view wx:if="{{ifHot}}">...</view>


<---目标界面js--->
onLoad: function(options){
	this.setData({
		ifHot: options.ifHot
	})
}

<---原界面js--->
wx.navigateTo({
	url: "../newPage?ifHot=false"
})
```

但是此时会发现wx:if判断ifHot一直为**true**，因为url传参的时候把false转换成字符串了

此时wx:if对一个非空字符串肯定返回true

# 解决方案

1. 可以改下wx:if的写法

```
<view wx:if="{{ifHot=='false'}}">...</view>
```

2. 可以进行类型转换

```
onLoad: function(options){
	let temp = JSON.parse(options.ifHot)
	this.setData({
		ifHot: temp
	})
}
```

