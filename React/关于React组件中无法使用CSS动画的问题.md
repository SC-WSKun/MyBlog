---
title: 关于React组件中无法使用过渡动画的问题
date: 2022-03-25 21:19:48
tags: React
categories: React
---

> 先记录下，后面找时间研究原理，感觉跟渲染原理有点关系

在写组件的时候添加了`transform 和 transition`，但是发现并没有生效。

查了下原因是：

transition这个属性只有在width属性发生改变的时候才会产生作用。**而React是一边计算宽度，一边渲染节点的，也就是说它节点生成的时候，宽度就已经定好了**。所以此时会直接显示在目标位置。。。

解决方案：

- 可以用JS实现动画
- 改用`animation`
