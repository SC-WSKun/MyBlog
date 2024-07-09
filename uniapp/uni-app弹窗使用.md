---
title: uni-app弹窗使用
date: 2021-08-11 23:27:20
tags: uni-app
category: Web开发
---
# uni-app弹窗简述
在写前端交互的时候经常需要用到弹窗来实现二次确认，操作确认，完成确认。可以给用户带来一个良好的反馈，同时某种程度上也方便了程序的调试
# uni-app弹窗种类
## 可编辑icon弹窗
使用uni.Toast()来设计可编辑icon弹窗，这个弹窗会自动关闭
```
uni.showToast({
    title:"",  //弹窗文字
    icon:"",    //弹窗图标，可以是"success","error","loading"，"loading"可以使用另一个api
})
```
## 加载弹窗
使用uni.showLoading()来弹出加载框，可以设置mask属性来设置模态加载框.
注意加载框
```
uni.showLoading({
    title:" //弹窗文字
})
```
## 选择弹窗