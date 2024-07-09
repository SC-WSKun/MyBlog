---
title: React将时间戳转化为字符串
date: 2021-11-08 20:27:32
tags: React
categories: Web开发
---

# 问题描述

在使用React进行开发的时候，发现不能直接使用`new Date(时间戳)`进行转换了

# 解决方案

使用moment模块进行转换

1. npm下载moment模块

```
npm install moment
```

2. 引入moment模块

```
import moment from 'moment'
```

3. 使用moment模块进行转换

```
moment(时间戳).format(YYYY-MM-DD)
```

