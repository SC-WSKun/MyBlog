---
title: CSS实现边框滚动效果
date: 2021-12-06 12:42:43
tags: 偷懒用
categories: CSS
---

# 效果图

![滚动边框-圆](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/%E6%BB%9A%E5%8A%A8%E8%BE%B9%E6%A1%86-%E5%9C%86.gif)

# HTML

```html
<!DOCTYPE html>
<html>
  <head>
    <title>滚动边框</title>
    <link rel="stylesheet" type="text/css" href="test.css" />
  </head>
  <body>
    <div class="box1"></div>
  </body>
</html>

```



# CSS

```css
*{
    margin: 0;
    padding: 0;
}

body{
    display: grid;
    place-items: center;
    min-height: 100vh;
    background: #181818;
}


.box1{
    width: 200px;
    height: 200px;
    background: #0000008c;
    position: relative;
    overflow: hidden;
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius: 15px;
    clip-path: circle();
}

.box1::before{
    content: "";
    /* 这个动画核心的地方，使用了conic-gradient去生成扇形渐变的效果 */
    background-image: conic-gradient(#39ce4d 20deg,transparent 120deg);
    width: 150%;
    height: 150%;
    position: absolute;
    border-radius: 15px;
    animation: rotate 2s linear infinite;
    clip-path: circle();
}

@keyframes
rotate{
    0%{
        transform: rotate(0deg);
    }
    100%{
        transform: rotate(-360deg);
    }
}

.box1::after{
    content: "";
    background: #000;
    width: 90%;
    height: 90%;
    position: absolute;
    border-radius: 15px;
    content: "Animation";
    color: white;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 1.5em;
    font-weight: 500;
    clip-path: circle();
}
```

