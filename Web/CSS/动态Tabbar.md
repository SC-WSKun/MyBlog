---
title: 动态Tabbar
date: 2021-11-30 23:52:37
tags: 偷懒用
categories: CSS
---

> 参考链接：https://www.bilibili.com/video/BV1kP4y1Y7Jb?from=search&seid=4325917562290644165&spm_id_from=333.337.0.0

这个代码亮点在于巧妙使用了radio的checked来实现样式之间的切换，对于选择器的运用也是炉火纯青。而且还运用了var()函数

# 效果图

![动态Tabbar](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/%E5%8A%A8%E6%80%81Tabbar.gif)

# HTML

```html
<!DOCTYPE html>
<html>
    <head>
        <title>动态导航栏</title>
        <link rel="stylesheet" type="text/css" href="tabbar.css">
    </head>
    <body>
        <div class="nav">
            <input type="radio" name="tabbar-item-opt" id="nav-item-opt-1" checked>
            <label class="nav-item" for="nav-item-opt-1">国药准字</label>

            <input type="radio" name="tabbar-item-opt" id="nav-item-opt-2">
            <label class="nav-item" for="nav-item-opt-2">生产日期</label>

            <div class="tracker"></div>
        </div>
    </body>
</html>
```

# CSS

```css
:root{
    --bg-color: #eaecf0;
    --main-color: #5080ef;
    --nav-item-width: 100px;
    --nav-item-height: 32px;
    --nav-item-border: 4px;  
}

*{
    padding: 0;
    margin: 0;
}

html,body{
    width: 100vw;
    height: 100vh;
}

body{
    display: flex;
    justify-content: center;
    align-items: center;
    background: var(--bg-color);
}

.nav{
    position: relative;
    background: white;
    border-radius: 24px;
    font-size: 0; /*这里将radio缩小了*/
    padding: var(--nav-item-border);
    z-index: 1;
}

.nav-item{
    width: var(--nav-item-width);
    height: var(--nav-item-height);
    font-size: 14px;
    color: var(--main-color);
    display: inline-flex;
    justify-content: center;
    align-items: center;
    cursor: pointer;
    transition: color .3s ease-in-out;
}

.nav>input[type="radio"]{
    width: 0;
    height: 0;
    opacity: 0;
}

.nav>input[type="radio"]:checked+label{ /*用了兄弟选择器，使文字颜色同步*/
    color: white;
}

.tracker{
    width: var(--nav-item-width);
    height: var(--nav-item-height);
    background: var(--main-color);
    z-index: -1;
    position: absolute;
    left: var(--nav-item-border);
    top: var(--nav-item-border);
    border-radius: 24px;
    transition: transform .3s ease-in-out;
}


#nav-item-opt-2:checked~.tracker{
    transform: translateX(calc(var(--nav-item-width) + var(--nav-item-border)*2)); /*这个加号左右必须有空格，否则calc函数将失效*/
}

```



