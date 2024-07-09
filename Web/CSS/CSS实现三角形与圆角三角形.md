---
title: CSS实现三角形与圆角三角形
date: 2021-09-02 14:08:58
tags: CSS
categories: CSS
---

# 三角形实现原理（1）

调整 box 的大小为 0，此时上下左右的 border 是 4 个三角形

当只有 bottom/top 或者 left/rigth 只有一边时，整个 border-box 是一个长方形

## CSS 代码

```
.triangle{
  width: 0px;
  height: 0px;
  border-bottom: 50px solid black;
  border-left: 50px solid blue;
  border-right: 50px solid red;
  border-top: 50px solid yellow;
}
```

# 三角形实现原理（2）

不使用border，使用clip-path对块进行裁剪

## CSS代码

```
.triangle{
    width: 100px;
    height: 100px;
    background: url('http://picsum.photos/100'); //一个可以随机生成图片的网站
    clip-path: polygon(0% 0%,100% 0%,0% 100%);
    transform: rotate(45deg);
}
```

# 圆角三角形实现原理

设置一个块的 border-radius 使其一角为圆角，然后使用skew进行倾斜转换，使其从正方形变成菱形，然后使用 before 和 after 的伪类复制两个相同的三角形，使用 transfrom的rotate旋转不同的角度，skew进行倾斜旋转，scale进行放大缩小，最后使用translate让图形平移，使三个圆角菱形成为圆角三角形的三个角

# CSS 代码

```
.triangle {
  position: absolute;
  top: 100px;
  left: 50px;
  height: 50px;
  width: 50px;
  border-top-right-radius: 30%;
  background: black;
  transform: rotate(-60deg) skewX(-30deg) scale(1, 0.866);
}

.triangle::before,
.triangle::after {
  position: absolute;
  height: 50px;
  width: 50px;
  border-top-right-radius: 30%;
  content: "";
}

.triangle::before {
  transform: rotate(-135deg) skewX(-45deg) scale(1.414, 0.707) translate(0, -50%);
  background: red;
}

.triangle::after {
  background-color: yellow;
  transform: rotate(135deg) skewY(-45deg) scale(0.707, 1.414) translate(50%);
}
```
