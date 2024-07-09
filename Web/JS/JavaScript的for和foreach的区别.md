---
title: JavaScript的for和forEach的区别
date: 2022-03-04 15:41:13
tags: JavaScript
categories: JavaScript
---

> 参考链接：https://www.cnblogs.com/echolun/p/11544045.html

# 前言

在面试写笔试题实现`Promise.all`的时候遇到一个神奇的问题：

因为题目要求按顺序输出每个Promise的结果，所以我想使用await阻塞每个promise，但是在`forEach`中却无法使用`await`阻塞`promise`调用，而for循环却可以。

在之前博主一直以为`forEach`就是for循环的一种实现，现在发现`forEach`可能不是for循环这么简单。

# `forEach`的工作原理

首先我们回顾下`forEach`的函数模板：

```js
arr.forEach(function(self,index,arr){},this);
//self：数组当前遍历的元素
//index：数组当前元素的索引
//arr：当前遍历的数组
//this：回调函数中this指向
```

从这里可以发现，`forEach`第一个参数是一个函数。这就是`forEach`与`for循环`不一样的地方，`forEach`会对数组的每项元素执行提供的**回调函数**

看到回调应该能意识到，其实**`forEach`不是一个同步的实现！它是异步的！**

# `forEach`异步实现带来的坑

1. **break问题**

   相信每个使用`forEach`的人一开始都会想在`forEach`中使用`break`，但是会发现`break`是不起作用的！当时博主也困惑于这个问题，但是没有多想。现在发现它是一个异步实现就茅塞顿开了

   {% note info %}

   那是不是就没办法在forEach里面跳出循环了呢？

   其实可以用`try{}catch(){}`实现：

   ```js
   try{
   	var arr = [1,2,3];
   	arr.forEach((item,index)=>{
   		if(item==2){
               throw new Error("Stop")
           }
   	})
   }carch(e){
       if(e.message!="Stop"){
           throw e;
       }
   }
   ```

   

   {% endnote %}

2. **return问题**

   同理，因为function其实是一个回调函数，可以联想到`Promise.then()`里面的`return`同样不生效，这两者是一个道理的。

3. **splice问题**（这个跟异步无关，但是也提一下）

   使用`forEach`的人可能有这个经历，在`forEach`中使用`splice()`删除数组的元素，结果发现它的`index`不会对应的进行重置，举个例子：

   ```js
   let arr = [1, 2];
   arr.forEach((item, index) => {
       arr.splice(index, 1);
   });
   console.log(arr)//arr是空的吗？
   ```

   这段代码的运行结果是：arr并没有清空。

   因为index不会重置的原因，在进行第二次循环的时候，`index`的值为1，而此时`arr`的`length`为1，当`index`大于等于`arr.length`时，`forEach`就不会执行了。

4. **await问题**

   因为`forEach`是异步实现的，所以想在回调函数里使用await实现前言中的按顺序输出是不行的。正确的方法是使用`for循环`

