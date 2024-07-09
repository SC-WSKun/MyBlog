---
title: React设置锚点滚动到页面指定位置
date: 2021-11-09 08:39:23
tags: React
categories: React
---

# 问题描述

开发Demo时遇到一个问题，如何实现点击按钮，使页面滚动到页面指定位置。比如点击评论，页面滚动到页面底部的评论区的位置。

# 解决方案

1. 可以使用<a>标签

2. 可以使用scrollIntoView API进行跳转

   1. 绑定触发事件

   ```
   <div onClick={()=>{this.scrollToAnchor}}></div>//绑定触发事件
   ```

   2. 定义函数

   ```react
   scrollToAnchor=(authorName)=>{
   	let anchorElement = document.getElementById(anchorName);
       if(anchorElement){
           anchorElement.scrollIntoView({block:"start",behavior:'smooth'})
       }
   }
   ```

   - block：决定滚动到锚点的位置，取值有start/end，start表示滚动到锚点顶部，end表示滚动到锚点底部
   - behavior：决定滚动的效果，取值有auto/instant/smooth，instant是直接跳转，smooth是滚动效果

