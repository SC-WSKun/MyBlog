---
title: CSS的Flex布局
date: 2021-11-11 01:03:01
tags: CSS
categories: CSS
---

# 简述

Flex布局是CSS的一种轴线式布局，使用Flex我们可以非常方便地对元素进行定位。

> 推荐一个CSS小游戏，可以帮你理解Flex布局：http://flexboxfroggy.com/

# 定义Flex布局

既然是网页的轴线式布局，那么Flex就得有水平和竖直两条轴线，flex的方向决定了主轴线的方向

需要注意的是，设置了Flex布局，**子元素的`float`、`clear`、`vertical-align`会失效**

# Flex布局的使用

# 容器属性

## 声明flex

```
display: flex;
```

## flex的主轴线方向

```
flex-direction: row/column; //默认为row
```

## flex的换行

```
flex-wrap: no-wrap/wrap/wrap-reserve (不换行/换行（第一行在上面）/换行（第一行在下面）)
```

## flex的主轴对齐方式

```
justify-content: flex-start | flex-end | center | space-between | space-around
```

![image-20211117130142047](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211117130142047.png)

## flex的交叉轴对齐方式

```
align-items: flex-start | flex-end | center | baseline | stretch
```

![image-20211118002133771](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211118002133771.png)

# 项目属性

## 排列顺序

```
order: <integer> //默认为0
```

## 放大比例

```
flex-grow: <number> //默认为0
```

## 缩小比例

```
flex-shrink: <number> //默认为1
```

## 主轴空间

```
flex-basis: <length> | auto //默认auto
```

## 项目对齐方式，可以摆脱容器的约束

```
align-self: auto | flex-start | flex-end | center | baseline | stretch;
```

