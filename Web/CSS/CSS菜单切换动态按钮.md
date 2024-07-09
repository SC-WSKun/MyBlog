---
title: CSS菜单切换动态按钮
date: 2021-11-29 19:59:05
tags: CSS
categories: CSS
---

> 参考链接：https://www.bilibili.com/video/BV1pr4y1k7Hy

# 特点

这个样式的特点是巧妙运用了transform的平移旋转实现了这个图形变换功能，通过transformX把中间的横杠移出父容器，然后用`overflow: hidden`隐藏确实是一种很巧妙的思路

# 效果图

![动态Button](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/动态Button.gif)

# 代码

## HTML

```html
<!DOCTYPE html>
<head>
    <title>动态按钮</title>
    <link rel="stylesheet" type="text/css" href="changeButton.css">
</head>
<body>
    <div class="toggle">
        <span></span>
        <span></span>
        <span></span>
    </div>
    <script>
        let menutoggle = document.querySelector('.toggle');
        menutoggle.onclick = function(){
            menutoggle.classList.toggle('active')
        }
    </script>
</body>

```

## CSS

```
*{
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body{
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    background: #f5f5f5;
}

.toggle{
    position: relative;
    width: 70px;
    height: 70px;
    background: #fff;
    box-shadow: 0 10px 20px rgba(0, 0, 0, 0.08);
    border-radius: 10px;
    display: flex;
    align-items: center;
    justify-content: center;
    overflow: hidden;
}

.toggle span{
    position: absolute;
    width: 40px;
    height: 4px;
    background: #1863ff;
    border-radius: 4px;
    transition: 0.5s;
}

.toggle span:nth-child(1)
{
    transform: translateY(-15px);
    width: 25px;
    left: 15px;
}

.toggle span:nth-child(2)
{
    transform: translateY(15px);
    width: 15px;
    left: 15px;
}
.toggle.active span:nth-child(1)
{
    transform: translateY(0px) rotate(45deg);
    width: 40px;
}
.toggle.active span:nth-child(2)
{
    transform: translateY(0px) rotate(315deg);
    width: 40px;
    transition-delay: 0.25s;
}
.toggle.active span:nth-child(3)
{
    transform: translateX(60px);
    width: 15px;
    left: 15px;
}
```

