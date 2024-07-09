---
title: CSS的Grid布局
date: 2021-11-11 01:02:55
tags: CSS
categories: CSS
---

# Grid布局是什么

> Grid布局是一种二维布局。与Flex布局不同，Flex布局是轴线布局，是一种一维布局，当我们要写网格布局的时候，用Grid布局肯定是要比Flex更加强大
>
> 这篇博客参考了廖雪峰的博客：https://www.ruanyifeng.com/blog/2019/03/grid-layout-tutorial.html
>
> 介绍一个CSS的小游戏，可以帮你理解Grid布局强大的地方：https://codepip.com/games/grid-garden/



# Grid基本概念



## 行、列

![image-20211111010549211](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211111010549211.png)

上图中，水平的深色区域就是“行”，垂直的深色区域就是”列“



## 单元格

> 行列交叉生成单元格



## 网格线

> 划分网格的线，称为"网格线"（grid line）。水平网格线划分出行，垂直网格线划分出列。

正常情况下，**n**行有**n+1**根水平网格线，**m**列有**m + 1**根垂直网格线，比如三行就有四根水平网格线。



## Grid的填充顺序

划分完网格后，Grid默认按照先行后列的顺序，将子元素按照顺序自动放入每一个网格



# 使用Grid布局

## 容器布局

### 定义display属性

```css
div{
	display: grid;
}
```



### 划分行列（重点）

```css
div{
	display: grid;
	grid-template-columns: 100px 100px 100px;
	grid-template-rows: 100px 100px 100px;
    //也可以用repeat( )
  	grid-template-columns: repeat(3, 33.33%);
  	grid-template-rows: repeat(3, 33.33%);
    //也可以使用auto-fill
    grid-template-columns: repeat(auto-fill, 100px);	
}
```



### fr

grid提供了一个非常强大的关键字叫fr（片段），它能够按倍数自适应分配大小，重点在**自适应**！！！而且还**可以跟固定长度搭配使用**

下面举个例子，这个例子可以在左右预留固定长度为100px的列，然后对剩余的中间部分以1:2的大小分割：

```css
div{
	grid-template-columns: 100px 1fr 2fr 100px;
}
```



### 行列间距

```
div{
	//旧版
	grid-row-gap: 20px;
	grid-column-gap: 20px;
	//新版
	row-gap: 20px;
	column-gap: 20px;
}
```



### 给网格线命名

我们可以用`[ ]`给每条网格线命名，方便以后使用。一条网格线可以有多个名字，使用空格隔开即可

```
div{
	grid-template-columns: [c1]100px [c2]100px [third-column c3]100px 100px;
}
```



### 给网格命名

上面是给线命名，我们也可以对格子进行命名

```
div{
	grid-template-columns: 100px 100px 100px;
	grid-template-rows: 100px 100px 100px;
	grid-template-areas: 	'a b c'   //这样写是九个单元格
							'd e f'
							'g h i'
							
	grid-template-areas: 	'a a d'   //这样写是带侧边栏的三行
							'b b d'		
							'c c d'
							
	grid-template-areas: 	'a . d'   //可以用.表示不需要用的区域
							'b . d'		
							'c c d'	
}
```



### 改变填充顺序

前面提到Grid默认先行后列填充，我们可以用`grid-auto-flow`来修改这个顺序

```
div{
	grid-auto-flow: column //先列后行
	
	grid-auto-flow: row dense //先行后列，但是尽量不出现空格，他会从第一行开始遍历，找到空位就填充进去
	grid-auto-flow: column dense //先列后行，但是尽量不出现空格，他会从第一列开始遍历，找到空位就填充进去
}
```



### 针对所有单元格内容的对齐（重点）

与flex的属性很相似，`justify-items`设置单元格内容的水平位置，`align=items`设置单元格内容的垂直位置

```
div{
	justify-items: start | end | center | stretch;
  	align-items: start | end | center | stretch;
}
```

![image-20211129142822238](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211129142822238.png)

### 针对所有单元格区域的对齐

`justify-content`和`align-content`控制内容区域在容器里面的水平位置和垂直位置

```
div {
	justify-content: start | end | center | stretch | space-around | space-between | space-evenly;
  	align-content: start | end | center | stretch | space-around | space-between | space-evenly;  
}

```

![image-20211129142837121](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211129142837121.png)

相信看图能够看出对内容对齐和对区域对齐的区别

然后我们也可以用`place-content`将`align-content`和`justify-content`合并起来

```
div{
    place-content: start start; //省略第二个值的话，会默认第二个值等于第一个值
}
```



## 项目属性

如何定义每个单元格的大小呢，这就需要我们对项目属性进行定义了

### 定义项目的四个边框（定义大小）(重点)

注意这里的start是从1开始，不是从0开始算的，然后这个**数字代表的是线而不是格子**

```
div{
	grid-column-start: 2;
	grid-column-end: 4;
	grid-row-start: 2;
	grid-row-end: 4;
}
```

这里前面定义的网格线的别名就起作用了，我们也可以用别名来定义边框的位置

```
div{
	grid-column-start: c1;
	grid-column-end: c2;
}
```

### span

span关键字表示边框跨域几个网格

```
div{
	grid-column-start: 1;
	grid-column-end: span 2;
}
与下面等效
div{
	grid-column-start: 1;
	grid-column-end: 3;
}
```

### 项目放在哪个区域

前面对网格命名在这里派上用场，我们可以用`grid-area`来定义这个项目应该放在哪个区域。如果没有别名，也可以使用定义4条边框的形式来指定位置

```
.item-1{
	grid-area: a; //别名形式
	grid-area: <row-start> / <column-start> / <row-end> / <column-end>; //四边框形式
}
```

### 设置单独单元格内容的对齐

`justify-self`和`align-self`这个属性有点像容器属性中的`justify-items`和`align-items`，但是它只是单独作用于这个项目

```
.item {
	justify-self: start | end | center | stretch;
	align-self: start | end | center | stretch;
}
```

