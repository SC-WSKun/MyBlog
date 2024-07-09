---
title: '关于overflow:hidden失效的问题'
date: 2022-03-31 10:59:46
tags: CSS
categories: CSS
---

> 今天遇到一个问题，在进行页面过渡的时候使用`translateX`进行过渡，但是父元素上的`overflow: hidden`并没有生效

# overflow: hidden失效的原因

- 子元素上有绝对定位：`position: absolute;`,而父元素上没有定位

  原理是：绝对定位的子元素会往外寻找第一个带定位的父元素，所以携带`overflow:hidden`的父元素如果没有定位会被跳过
