---
title: 自用微信小程序NavigationBar
date: 2021-11-10 19:23:51
tags: [偷懒用，微信小程序]
categories: Web开发
---

微信小程序的navigationBackgroundColor只能使用十六进制的写法，因此不支持透明度。如果想使用透明度或者更灵活的导航栏，可以使用自定义的方式，自己写一个。

# wxml

```html
<view class="navigation">
  <view class="backIcon" bindtap="goBack"></view>
  <view class="navigationTitle">每日一练</view>
</view>
```

# wxss

```css
.navigation {
  width: 750rpx;
  height: 172rpx;
  background-color: rgba(6, 161, 254, 0.98);
  position: sticky;
  top: 0rpx;
  z-index: 5;
  display: flex;
  justify-content: center;
}

.backIcon {
  position: absolute;
  left: 32rpx;
  top: 108rpx;
  width: 20rpx;
  height: 20rpx;
  border-top: 4rpx solid #FFFFFF;
  border-left: 4rpx solid #FFFFFF;
  transform: rotate(-45deg);
  z-index: 6;
}

.navigationTitle {
  color: rgba(255, 255, 255, 1);
  font-size: 34rpx;
  font-family: "webfont";
  margin-top: 96rpx;
}
```

# json

```json
{
  "usingComponents": {},
  "navigationStyle": "custom"
}
```

