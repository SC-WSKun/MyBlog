---
title: CORS
date: 2021-09-21 12:30:58
tags: 计算机网络
categories: 计算机网络
---

基于 unicloud 开发的 web 管理系统进行前端网页托管后发现无法调用云函数，控制台报了 CORS 错误，于是学了下 CORS

# 首先了解一下跨域 HTTP 请求

跨域 HTTP 请求是指发起请求的资源所在域与请求指向的域不同。

出于安全考虑，浏览器会限制脚本发起跨站请求，XHR 发起的 HTTP 请求需要遵守同源策略，即 XHR 对象只能向其加载的源域名发起 HTTP 请求，而不能向任何其它域名发起请求。

# CORS 是什么

CORS（Cross-Origin Resourse Sharing），跨域资源共享。他可以让服务器来确定是否允许跨域访问。这个机制会阻止一些恶意网站的 js 脚本发起的**非简单请求**，但是**无法阻止简单请求**。

CORS 触发的情况有四种：

1. 不同的域名
2. 不同的子域名
3. 不同的端口
4. 不同协议

# 预检查

浏览器发起一个非简单请求时，会先发送一个 OPTIONS 请求到服务器，企图

# CORS 有关的 HTTP 头

- Access-Control-Allow-Origin

  这个头部信息由服务器返回，用来指定哪些客户端的域名可以访问这个资源，有两个值

  1. \*：允许任意域名（一些插件通过修改返回的头部变成\*来实现跨域）
  2. 一个完整的域名：当需要传递验证信息时（比如 cookie），必须使用完整的域名

- Access-Control-Allow-Credentials 

  这个头部信息的唯一有效值为 true，表示是否可以将请求的响应暴露给页面。
  
  Credentials可以是cookies, authorization headers 或 TLS client certificates

  可以作为预检请求的响应的一部分时，表示真正的请求能否使用credentials。举个例子：POST请求的预检返回的Access-Control-Allow-Credentials为false，则真实请求时就不能使用cookies等credentials。简单的Get请求因为没有预检，则如果Access-Control-Allow-Credentials没有随资源返回，响应就会被浏览器忽视。

  Credentials必须在前后端都被配置（即the Access-Control-Allow-Credentials header 和 XHR 或Fetch request中都要配置）才能使带credentials的CORS请求成功

- Access-Control-Allow-Headers

  提供一个逗号分隔的列表表示服务器支持的请求数据类型（例如：GET，POST）

- Origin
  
  这个值表示这个请求是从哪个域名发出的。出于安全原因，浏览器不允许修改这个值。
