---
title: React对一个元素绑定两个Ref
date: 2022-06-15 15:50:12
tags: React
categories: React
---

# 问题

写Web课设的时候遇到这么个问题，一个元素需要有一个Ref来实现创建时自动聚焦，还需要另一个forwardRef传给父组件，使选中父组件时对这个元素自动聚焦。

# 解决方案

ref可以接收一个回调函数，这个函数会在组件挂在或者卸载时调用，函数参数为这个组件的实例或者DOM元素。

```React
let setRef = (ref, value) => {
    if (typeof ref === "function") {
        ref(value);
    } else if (ref) {
        ref.current = value;
    }
};

<div
    ref={(e) => {
        setRef(forwardedRef,e);
        setRef(itemBox,e)
    }}
></div>
```

