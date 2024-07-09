---
title: JS数组的sort
date: 2021-12-11 00:21:30
tags: JavaScript
categories: JavaScript
---

# sort

定义一个数组arr，sort有以下特点：

```
let arr = [1,2,10,12];

arr.sort();  //[1,10,12,2] 默认是字典排序

arr.sort((a,b)=> {return a.length-b.length}; )//当返回值为负数时不交换，正数则交换
```

