---
title: JS的记忆函数
date: 2022-03-16 08:55:37
tags: JavaScript
categories: JavaScript
---

# 什么是记忆函数

对于一些复杂度比较高的函数，如果我们将已经得到的结果保存下来，下次遇到同样的参数时就可以直接返回结果，能够节省很多时间。属于用空间换时间的一种优化方式。

# 记忆函数

记忆函数使用了函数闭包的特性，通过闭包保存函数的参数和结果。

代码示例：

```js
function memorize(func,hasher){
    let cache = {};
	return function(key){
        let address = "" + (hasher ? hasher.apply(this,arguments) : key);
        if(!cache[address]){
            cache[address] = func.apply(this.arguments)
        }
        return cache[address];
    }
}
```

单参数时直接使用key进行传值

多参数时要使用hasher函数自定义存储的address值

```js
let memoizedAdd = momerize(add,function(){
    let args = Array.prototype.slice.call(arguments)
    return JSON.stringify(args)
})
```

