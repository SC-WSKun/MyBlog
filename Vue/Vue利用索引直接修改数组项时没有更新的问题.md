---
title: Vue利用索引直接修改数组项时没有更新的问题
date: 2021-09-22 22:10:50
tags: Vue
category: Vue
---
# Vue不能检测到以下数组的变动
1. 利用索引直接设置一个数组项，例如：`arr[index] = value`
2. 直接修改数组的长度时，例如：`arr.length = newLength`
# 解决方案
1. 使用数组的方法，比如set(arr,index,value), arr.splice(index,1,value)
2. 强制页面刷新