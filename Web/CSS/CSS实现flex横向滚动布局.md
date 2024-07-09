---
title: CSS实现flex横向滚动布局
date: 2021-09-28 13:21:08
tags: CSS
categories: CSS
---
# 代码
```
.good_list {
  width: 100%;
  display: flex;
  flex-direction: row;
  overflow: scroll;
  white-space:nowrap;
}

.good_item {
  width: 302rpx;
  height: 422rpx;
  margin-right: 32rpx;
  flex-shrink: 0;
}
//隐藏滚动条
.good_list::-webkit-scrollbar{
  display: none;
}
```