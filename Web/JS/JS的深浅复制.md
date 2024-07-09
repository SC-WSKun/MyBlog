---
title: JS的深浅复制
date: 2022-02-14 20:38:04
tags: JavaScript
categories: JavaScript
---

# 为什么有深浅复制之分

众所周知，JS有两种数据类型：基本类型和引用类型。

这两种类型在存储中的存储方式是不一样的，基本类型作为局部变量时存储在栈中，引用类型的地址作为局部变量储存在栈中，实体则存在堆里

![image-20220214210213614](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20220214210213614.png)

当我们将一个存放引用类型数据的引用对象（比如：二维数组）赋值给一个变量时，其实是将元素的地址复制了一份（浅复制），所以此时会出现两个变量的元素指向同一个堆空间里的实体的情况，当我们修改其中一个引用对象的元素时，另一个引用对象的元素也会跟着改变，因为是同一个实体。

深复制就是，对于每个元素在堆空间中重新分配一个新的实体，实现变量的分离。

# 如何进行深浅复制

## 浅复制

- Array.concat()
- Object.appach()
- Array.slice()

## 深复制

- 使用`JSON.parse()`和`JSON.stringify()`进行转换

  ```js
  arr2 = JSON.parse(JSON.stringify(arr1))
  ```

- 递归调用

- ```
  arr2 =  Array.of(...arr1)
  ```

