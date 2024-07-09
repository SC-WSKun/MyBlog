---
title: uni-app+unicloud转微信小程序时跨域配置注意事项
date: 2021-09-06 00:41:38
tags: ["uni-app","微信小程序"]
category: Web开发
---
```
参考链接：https://uniapp.dcloud.io/uniCloud/quickstart?id=useinh5
```
# 问题描述
使用uni-app转到微信小程序时，因为后端使用的是unicloud的云函数，在上传到体验版后无法访问云函数。
# 解决方案
登录微信公众平台，在开发管理中点击服务器配置，配置域名白名单。

关于request请求需要配置：
1. 腾讯云：https://tcb-api.tencentcloudapi.com
2. 阿里云：https://api.bspapp.com

如果需要download跟upload操作也需要配置响应的域名