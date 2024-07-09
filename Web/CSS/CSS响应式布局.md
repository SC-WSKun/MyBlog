---
title: CSS响应式布局
date: 2022-03-22 22:21:23
tags: CSS
categories: CSS
---

> 在看别人的源码的时候，发现了一个神奇的东西：
>
> ```
> @media screen and (max-width:996px){
> 
> }
> ```
>
> 查了下这是`CSS3`的`Media Query`，专门用来写响应式布局的

# Media Query是什么

Media Query作用就是允许添加表达式用以确定媒体的环境情况，以此来应用不同的样式表。

- 换句话说，其允许我们在不改变内容的情况下，改变页面的布局以精确适应不同的设备。
- 其实就是动态样式表的一种实现样式覆盖的方法 

# Media Query怎么使用

有两种使用的方式

- 在`link`标签中直接使用`media`属性直接进行判断

  例如：

  ```html
  <link rel="stylesheet" type="text/css" href="styleA.css" media="screen and (min-width: 400px)">
  ```

  {% note info %}

  - screen是媒体类型的一种，CSS2.1定义了10种媒体类型

    | media_type | describe                   |
    | ---------- | -------------------------- |
    | all        | 所有设备                   |
    | aural      | 听觉设备                   |
    | braille    | 点字触觉设备               |
    | handled    | 移动设备                   |
    | print      | 打印预览图                 |
    | projection | 投影设备                   |
    | screen     | 显示器、笔记本、移动端设备 |
    | tty        | 打字机或终端等设备         |
    | tv         | 电视机等设备类型           |
    | embossed   | 盲文打印机                 |

  - `and`是关键字，关键字还有`not（排除某种设备）`、`only（限定某种设备）`
  - `(min-width: 400px)`是媒体特性，有点像是启动条件

  {% endnote %}

- 直接写在`style`标签里

  ```css
  @media screen and (max-width: 600px){
  	.class{
  		background: #ccc
  	}
  }
  ```

# 一个示例

效果，当宽度小于800px时变为纵向布局

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>响应式布局</title>
  </head>
  <body>
    <div class="largeBox">
      <div class="leftBox"></div>
      <div class="midBox"></div>
      <div class="rightBox"></div>
    </div>
  </body>
  <style>
    body {
      display: flex;
      flex-direction: row;
      justify-content: center;
      min-height: 100vh;
    }
    .largeBox {
      width: 80%;
      background: black;
    }
    .leftBox {
      width: 25%;
      background-color: yellow;
      height: 100%;
      float: left;
    }
    .midBox {
      width: 50%;
      height: 100%;
      background: green;
      float: left;
    }
    .rightBox {
      width: 25%;
      background: pink;
      height: 100%;
      float: left;
    }
    @media screen and (max-width: 800px) {
      .largeBox {
        width: 100%;
      }
      .leftBox {
        width: 100%;
        height: calc(100% / 3);
      }
      .midBox {
        width: 100%;
        height: calc(100% / 3);
      }
      .rightBox {
        width: 100%;
        height: calc(100% / 3);
      }
    }
  </style>
</html>
```

