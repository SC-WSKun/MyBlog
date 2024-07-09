---
title: ES6的let和const变量
date: 2021-07-31 18:02:10
tags: [JavaScript,ES6]
category: JavaScript
---
# ES6的let和const变量
## let
let 是ES6用来取代var的一种变量类型，与var相比，let多了块级作用域这个特性，即let定义的变量只能在{ }内使用。let有以下四个特性
1. 块级作用域，var是一个函数作用域，虽然写代码时感觉var更方便，但是在代码规范、bug排查上，let会更加舒适，因此更推荐使用let
   ```js
   {
       let temp = 1
       console.log(temp) //1
   }
   consolo.log(temp) //undefined
   ```
   
2. 变量不能重复声明
   ```js
   let temp = 0
   let temp = 1 //false
   ```
   
3. 不存在变量提升，必须在定义变量后才可以使用该变量
   ```js
   console.log(temp1) //1
   var temp1 = 1
   
   console.log(temp2) //undefined
   let temp2 = 2
   ```
   
4. 不影响作用域链，即子块可以用父块里面的let变量

### let的暂时性死区

虽然`let`不存在变量提升，但是有一个**暂时性死区**的特性。

> 当块级作用域内存在`let`命令，它所声明的变量就“绑定”了这个区域，不再受外部的影响

举个例子：

```js
var tmp = 123;

if (true) {
  tmp = 'abc'; // ReferenceError
  let tmp;
}
```

这里虽然全局变量中定义了一个`tmp`，但是在`tmp = 'abc'`这个语句中，`tmp`并不会沿着作用域链向上找到全局中的`tmp`，因为`let`导致`tmp`绑定了这个块级作用域，所以我们在`let`声明变量前都无法使用`tmp`这个变量

## const
const是ES6用来定义常量的一种变量类型，可以充当类似C语言的宏的作用，通常编写代码时，变量名使用大写，它具有以下特点：
1. 也具有块级作用域

2. 定义时必须要有初始值
   ```js
   const TEMP1 = 1 //right
   const TEMP2     //false
   ```
   
3. 定义后就不能修改（一般情况下）

4. 如果定义的是数组或者对象，是可以更改数组或者对象里面的字段或元素值的，原因是常量定义数组和对象时，保存的其实是数组和对象的内存地址，对于内存地址指向的值并没有作出限制

# 块级作用域

> 对于上面块级作用域的补充说明

## 什么叫块级作用域

对于`var`的函数作用域来说，`var`定义的变量不会穿透函数，但是会穿透一些，比如条件表达式或者for循环的块：

```js
function(){
	var a = 1;
}
console.log(a) //undefined

for(var i = 0; i < 10; i++){ //非常典型的for循环穿透问题
    var b = 1;
}
console.log(i) //10
console.log(b) //1
```

而对于`let`和`const`定义的变量，则不会穿透条件表达式或者for循环，会更加安全。

# 顶层对象

在ES5中的`var`定义的全局变量，与window中定义的变量是等价的，比如下面的代码

```js
var a = 1;
console.log(window.a) //1
```

而ES6中为了避免全局变量与window挂钩，全局定义的let和const是不会挂在顶层对象window上的

```js
let a = 1;
console.log(window.a) //undefined
```

