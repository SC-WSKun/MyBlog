---
title: React将字符串转成JSX
date: 2021-10-28 20:57:09
tags: React
category: React
---

# 问题描述

在写字节实践项目的时候，官方给的api返回的是一串HTML字符串，类似`"<div>...</div>"`，直接渲染的话在页面中显示的是字符串。这时候就需要将字符串转化成JSX了，React提供了一个**dangerouslySetInnerHTML**的属性来应对这种情况

# 解决代码

```
<div dangerouslySetInnerHTML={{__html: 你的字符串}}></div>
```



