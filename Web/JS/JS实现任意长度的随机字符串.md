---
title: JS实现任意长度的伪随机字符串
date: 2021-08-31 15:13:08
tags: JavaScript
categories: JavaScript
---

```
funtion fn(len){
    let stringA = 'abcd.....890';
    let length = stringA.length;
    let temp = '';
    for(let i=0;i<len;i++){
        temp += stringA.CharAt(Math.floor(Math.random( )*length));
    }
    return temp;
}
```
