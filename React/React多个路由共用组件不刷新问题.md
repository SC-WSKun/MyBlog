---
title: React多个路由共用组件不刷新问题
date: 2022-04-09 11:57:47
tags: React
categories: React
---

# 情况描述

因为有多个信息页面的样式相同，所以做了个组件进行复用，但是跳转路由的时候，页面只进行了`render`，而没有执行`constructor`等生命周期

# 解决方案

导致不刷新的原因是`react`的`diff`算法只要判断`Props`没有变化就不会进行组件更新，所以我们要给每一个link传一个参数，使props发生变化即可

然而在6.x版本之后，React取消了`props`的`match,history,location`，我们只能拥抱函数式组件，使用`useParams()`这个钩子来获取参数。
