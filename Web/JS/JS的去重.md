---
title: JS的去重
date: 2022-02-15 00:36:53
tags: JavaScript
categories: JavaScript
---

> 参考链接：https://github.com/mqyqingfeng/Blog/issues/27

# 双层循环去重

# 排序去重

```js
var array = [1,2,1,3,2];
function unique(arr){
    let sortedArray = array.concat().sort()
    let res = [];
    let seen;
    for(let i=0;i<sortedArray.length;i++){
        if(!i||sortedArray[i]!==seen){
            res.push(sortedArray[i])
        }
        seen = sortedArray[i];
    }
    return res; 
}
```

# 结合filter

```js
var array = [1,2,1,3,2];
function unique(arr,isSorted){
    if(isSorted){
        return arr.filter((item,index,arr)=>{
        	return !index || item!=arr[index-1]
    	})
    }else{
        return arr.filter((item,index,arr)=>{
            return arr.indexOf(item)===index
        })
    }
}
```

# 使用ES6的Set

```js
function unique(arr){
	return Array.from(new Set(arr));
}
```

使用拓展运算符和箭头函数简化

```js
var unique = (arr) => [...new Set(arr)];
```

# 关于去重的特殊情况

- `indexOf()`本质上是使用`===`进行判断，所以当遇到对象或者`NaN`时，会出现永远不相等的情况，所以无法对这两种进行去重，直接使用`===`进行判断也是一样。
- Set虽然也是用`===`判断，但是对于`NaN`做了特别的优化，也会判断是相同的元素，但是对于对象不会
- 键值对去重对于所有数据类型都能进行判断，可以参考顶部的参考链接

