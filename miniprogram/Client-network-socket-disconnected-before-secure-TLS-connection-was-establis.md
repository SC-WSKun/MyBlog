---
title: Client network socket disconnected before secure TLS connection was established
date: 2021-09-07 12:26:43
tags: 微信小程序
categories: Web开发
---
# 问题描述
在微信小程序触发Unicloud云函数进行request请求时报错，原因是开了VPN

```
Error: Client network socket disconnected before secure TLS connection was established
```

# 解决方案
在微信小程序的代理设置里面设置为不是用系统代理，勾选后直连网络