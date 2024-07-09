---
title: SpringBoot链接Mysql时报错
date: 2022-03-10 01:00:47
tags: Java
categories: Java
---

# SpringBoot链接Mysql时报错

```
The server time zone value ... is unrecognized or represents more than one time zone
```

# 解决方法

1. 进入MySQL命令模式
2. 输入`set persist time_zone='+8:00';`
3. 输入`flush privileges;`
