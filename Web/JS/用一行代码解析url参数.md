---
title: 用一行代码解析url参数
date: 2021-09-16 00:23:54
tags: JavaScript
categories: Web开发
---
```
url.split('?')[1].split('&').reduce((obj,item)=>({...obj,[item.split('=')[0]]:item.split('=')[1]}),{})
```