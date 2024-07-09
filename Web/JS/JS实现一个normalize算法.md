---
title: 实现一个normalize算法
date: 2021-09-06 14:43:52
tags: JavaScript
---

# 需要用到的方法

## split（）

根据传入的参数把字符串分割成一个数组，可以用正则表达式使分割更加灵活

## filter（）

将得到数组进行过滤，filter 传入一个函数，返回一个布尔值，根据布尔值判断是否保留这个元素

## reduce（(prev,cur,index,arr)=>{},init）

一个循环遍历函数，prev 的初始值为 init，后续值为上一个函数的返回值，cur 是当前数组元素的值，arr 是原数组

# 实现 normalize 函数

```
let result = {};
arr.split('/[\[\]]/g')
.filter(Boolen)
.reduce((obj,item,index,arr)=>{
    obj.value = item;
    if(index !  == arr.length-1){
        return obj.children={}
    }
},result)
return result
```
