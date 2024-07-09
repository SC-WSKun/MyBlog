---
title: JS数组元素类型快速转换
date: 2021-12-03 18:23:02
tags: JavaScript
categories: JavaScript
---

# map方法

map()会对原始数组元素调用参数内的函数处理后的值，所以我们可以使用map对所有元素进行类型转换

# 解决方案

```
arr = arr.map(String);//转换成字符串
arr = arr.map(Number);//转换成数字
...
```

