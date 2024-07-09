---
title:  '解决git clone出现fatal: unable to access ''https://...''的问题'
date:  2021-07-16 01:58:02
tags:  Git
categories: Git
---
# 引入
网上大部分教程都是直接输入下面的代码，但是尝试了一直没有效果
```
git config --global --unset http.proxy 
git config --global --unset https.proxy 
```
后来尝试了先配置代理
```
git config --global http.proxy http://127.0.0.1:1080
git config --global https.proxy http://127.0.0.1:1080
```
再取消代理
```
git config --global --unset http.proxy 
git config --global --unset https.proxy 
```
就可以用了。[黑人问号]