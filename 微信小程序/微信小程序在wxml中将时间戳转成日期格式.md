---
title: 微信小程序在wxml中将时间戳转成日期格式
date: 2021-10-07 15:58:00
tags: 微信小程序
categories: Web开发
---
# 问题描述
在wxml中想直接使用`new Date(timestamp)`进行转换，发现报错了，后来发现wxs不支持new Date()
# 写一个有时间转换函数的wxs
```
var filter = {
  getDateTime: function (value) {
    //不能使用 new Date()
      var time = getDate(value);
      var year = time.getFullYear();
      var month = time.getMonth() + 1;
      var date = time.getDate();
      var hour = time.getHours();
      var minute = time.getMinutes();
      var second = time.getSeconds();
      month = month < 10 ? "0" + month : month;
      date = date < 10 ? "0" + date : date;
      hour = hour < 10 ? "0" + hour : hour;
      minute = minute < 10 ? "0" + minute : minute;
      second = second < 10 ? "0" + second : second;
      return year + "-" + month + "-" + date + " " + hour + ":" + minute + ":" + second;
  }
}
// 导出对外暴露的属性
module.exports = {
  getDateTime: filter.getDateTime
}
```
# 引入wxs
在wxml的顶部引入
```
<wxs module="filter" src="./fun.wxs"></wxs>
```
# 使用引入的wxs进行转换
```
{{filter.getDateTime(timestamp)}}
```