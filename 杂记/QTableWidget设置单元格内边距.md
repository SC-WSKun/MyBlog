---
title: QTableWidget设置单元格内边距
date: 2022-05-16 10:21:25
tags: Qt
categories: C++
---

直接使用`padding-left`是无效的，需要同时使用`border:0`，如下：

```css
this->setStyleSheet("QTableWidget::item{border:0;padding-left:20px;}");
```

