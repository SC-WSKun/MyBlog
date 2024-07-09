---
title: ES6偷懒代码
date: 2021-12-19 12:28:53
tags: 偷懒用
categories: 
---

# 隐藏指定元素

```js
const hide = (...el) => [...el].forEach((e) => (e.style.display = "none"));

```

# 确认元素是否具有指定的类

```js
const hasClass = (el, className) => el.classList.contains(className);

```

# 获取当前页面的滚动位置

```js
const getScrollPosition = (el = window) => ({
  x: el.pageXOffset !== undefined ? el.pageXOffset : el.scrollLeft,
  y: el.pageYOffset !== undefined ? el.pageYOffset : el.scrollTop,
});
```

# 滚动到页面顶部

```js
const scrollToTop = () => {
  const c = document.documentElement.scrollTop || document.body.scrollTop;
  if (c > 0) {
    window.requestAnimationFrame(scrollToTop);
    window.scrollTo(0, c - c / 8);
  }
};
```

# 确认父元素是否包含子元素

```js
const elementContains = (parent, child) =>
  parent !== child && parent.contains(child);
```

# 确认指定元素在窗口是否可见

```js
const elementIsVisibleInViewport = (el, partiallyVisible = false) => {
  const { top, left, bottom, right } = el.getBoundingClientRect();
  const { innerHeight, innerWidth } = window;
  return partiallyVisible
    ? ((top > 0 && top < innerHeight) ||
        (bottom > 0 && bottom < innerHeight)) &&
        ((left > 0 && left < innerWidth) || (right > 0 && right < innerWidth))
    : false;
};
```



