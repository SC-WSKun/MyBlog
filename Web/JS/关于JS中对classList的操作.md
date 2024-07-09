---
title: 关于JS中对classList的操作
date: 2022-03-04 09:03:47
tags: JavaScript
categories: JavaScript
---

# 什么是classList

`classList`是H5新增的访问dom结点的CSS类的方式，我们可以**通过对`classList`进行操作**，替换增加删除dom结点的CSS类，**实现更改dom结点样式的效果**

# classList方法

- `classList.add()`：添加新的类名，如已经存在，取消添加
- `classLIst.contain()`：确定元素中是否包含指定的类名，返回值为true、false;
- `classList.remove()`：移除已经存在的类名
- `classList.toggle()`：如果classList中存在给定的值，删除它，否则添加它
- `classList.replace()`：类名替换

# 注意

支持`classList`属性的浏览器有FirxFox3.6+和chrome
