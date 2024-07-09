---
title: CSS中使用var重复使用变量
date: 2021-11-30 23:40:16
tags: CSS
categories: CSS
---

# 语法介绍

之前一直使用less定义变量，今天看别人源码的时候才发现CSS3提供了一种定义变量，便于使用多次重复引用的值的方法。

我们可以在`:root`伪类中定义我们想要的变量，记得必须要以`--`开头

在编写代码时，使用`var()`来引用变量，`var()`可以携带两个参数，第一个是我们定义的变量名，第二个是变量名不存在时使用的值，可以理解为默认值

# 使用范例

```
:root{
	--temp-width: 1px;
}

div{
	width: var(--temp-width)
	height: var(--temp-height, 2px) //因为我们没有定义--temp-height，所以这里的值会使用第二个参数2px
}
```

