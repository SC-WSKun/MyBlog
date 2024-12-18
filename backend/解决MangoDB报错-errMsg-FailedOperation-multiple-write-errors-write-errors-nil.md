---
title: >-
  解决MangoDB报错 errMsg: [FailedOperation] multiple write errors: [{write errors: 
  {<nil>}];
date: 2022-03-10 00:56:37
tags: MangoDB
categories: Web开发
---

# 问题描述

在使用 unicloud 的云函数 update 更新数据时报错

```
 errMsg: [FailedOperation] multiple write errors: [{write errors: [{'$set' is empty. You must specify a field like so: {$set: {<field>: ...}}}]}, {<nil>}];
```

即使删除了 set 方法依然存在

# 解决方法

这里其实不是 set 的问题，而是 update 没有进行任何操作，也就是没有接收到更新的数据。

请排查下是否传入了想要更新的数据，变量名是否一致，update 里面是否有对应需要更新的字段。

比如我的报错是因为虽然传入了 username 但是 update 里面没有{username:event.username}，所以报错。
