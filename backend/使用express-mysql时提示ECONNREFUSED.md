---
title: 使用express+mysql时提示ECONNREFUSED
date: 2023-01-11 15:02:43
tags: [nodejs,mysql]
categories: nodejs
---

# 问题描述

启动express项目后，用postman访问对应接口，返回的errMsg显示“ECONNREFUSED”

# 解决方案

在后端的mysql连接的config中添加一个字段`socketPath: '/var/run/mysqld/mysqld.sock'`

例如：

```
var conn = mysql.createConnection({
    host:'',
    port:3306,
    user:'',
    password:'',
    database:'',
    socketPath: '/var/run/mysqld/mysqld.sock'
})
```

