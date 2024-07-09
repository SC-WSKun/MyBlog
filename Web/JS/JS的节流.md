---
title: JS的节流
date: 2022-03-16 15:41:30
tags: JavaScript
categories: JavaScript
---

> 参考链接：https://github.com/mqyqingfeng/Blog/blob/master/articles/%E4%B8%93%E9%A2%98%E7%B3%BB%E5%88%97%E6%96%87%E7%AB%A0/JavaScript%E4%B8%93%E9%A2%98%E4%B9%8B%E8%B7%9F%E7%9D%80underscore%E5%AD%A6%E8%8A%82%E6%B5%81.md

# 节流是什么

在Web应用中我们经常需要添加监听事件，比如滚动事件，鼠标移动事件。这些事件有时候触发频率会非常高，如果没有做限制的话，短时间内的大量事件触发会给页面性能带来负面影响。所以我们使用节流技术来限制一定时间内事件发生的次数。

# 实现节流

- **使用时间戳进行节流**

  原理：使用变量存取之前的时间戳，与当前时间戳进行比较，如果差值大于设置的时间周期，就执行函数

  ```js
  function throttle(func, wait){
  	var context,args;
      var previous = 0;
      return function(){
          var now = +new Date();
          context = this;
          args = arguments;
          if(now - previous > wait){
              func.apply(context,args);
              previous = now;
          }
      }
  }
  ```

- **使用定时器**

  原理：使用定时器定时触发，当定时器存在时屏蔽触发，触发后销毁定时器。

  ```js
  function throttle(func, wait){
      var timeout;
      var previous = 0;
      return function(){
          context = this;
          args = arguments;
          if(!timeout){
              timeout = setTimeout(function(){
                  timeout = null;
                  func.apply(context,args)
              },wait)
          }
      }
  }
  ```

  

