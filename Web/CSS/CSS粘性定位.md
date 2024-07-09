---
title: CSS粘性定位
date: 2022-03-25 17:19:16
tags: CSS
categories: CSS
---

> 今天写web作业的时候想使用粘性定位，但是发现`header`并没有像预想中的一样粘在顶上，后来发现是因为父元素上有`overflow:hidden`，所以记录下

# 粘性定位

我们在浏览一些网站的时候，会发现当我们向下滚动时，会发现导航栏一直固定在顶部。这个就是粘性定位的作用。

使用方法，设置position: sticky同时给一个(top, right, bottom, left)之一即可：

```css
.div{
	position: sticky;
	top:0;
}
```

# sticky VS fixed

粘性定位和固定定位看起来好像很像，都是实现一个元素固定在一个位置。

其实`sticky`相当于`fixed + relative`，当我们位于目标窗口的时候，他呈现的是`relative`的效果，也就是说，它会跟着父元素一起滑动，但是当触发了定位条件，它就会呈现`fixed`的效果。

比如：

我们对一个元素设置粘性定位`top: 100px`

当这个元素距离顶部大于100px的时候，它会随着页面滚动；

当我们滚动到距离顶部100px时，我们向下滑动，他就会停滞不动。

而`fixed`定位则是，从头到尾都不会随着页面滚动，所以在业务中其实使用`sticky`更多一点。

# 注意点

- 父元素不能overflow: hidden或者overflow: auto属性
- 必须指定top,right, bottom, left4个值之一，否则只会处于相对定位
- 父元素的高度不能低于sticky元素的高度
- sticky元素仅在其父元素内生效
