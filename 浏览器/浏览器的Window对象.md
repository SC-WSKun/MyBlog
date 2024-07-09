---
title: 浏览器的BOM对象
date: 2022-02-13 21:03:55
tags: JavaScript
categories: JavaScript
---

# BOM（浏览器对象模型）

想要在Web中使用JS，我们需要BOM来提供用于访问浏览器的功能的对象，其中核心对象是window对象

# window对象

在浏览器中，window对象既是通过JS访问浏览器窗口的一个接口，又是ECMAScript规定的Global对象

## 全局作用域

window对象同时扮演ES中Global对象的角色，因此所有在全局作用域中用（var）声明的变量、函数都会变成window对象的属性和方法。

## 窗口大小

{% note info %}

window对象提供了四个属性，`innerWidth`，`innerHeight`，`outerWidth`，`outerHeight`。两个`outer`代表页面容器的大小（带边框），两个`inner`代表页面视图区的大小（减去边框）

{% endnote %}

- 如何控制窗口的大小呢？

可以通过`window.resizeTo(a,b)`和`window.resizeBy(a,b)`来调整窗口大小

`resizeTo`接收新的窗口宽度和高度，`resizeBy`接收新窗口与原窗口的宽度和高度之差

## 打开新窗口

- 如何打开一个新的窗口

  使用`window.open()`可以导航到一个特定的URL并返回一个指向新窗口的引用，这个方法接收4个参数：

  - 要加载的URL
  - 窗口目标
  - 一个特性字符串
  - 一个表示新页面是否取代浏览器历史记录中当前加载页面的布尔值

  示例：

  ```
  window.open("http://www.baidu.com/","topFrame");
  //等价于点击了 < a href="http://www.baidu.com" target="topFrame" ></a>
  ```

  {% note info %}

  特性字符串表格

  | 设置       | 值     | 说明                                                     |
  | ---------- | ------ | -------------------------------------------------------- |
  | fullscreen | yes/no | 表示浏览器窗口是否最大化（仅限IE）                       |
  | height     | 数值   | 新窗口的高度，不能小于100                                |
  | left       | 数值   | 表示新窗口的左坐标，不能是负值                           |
  | location   | yes/no | 表示是否在浏览器窗口中显示地址栏，默认值依具体浏览器而定 |
  | menubar    | yes/no | 表示是否在浏览器窗口中显示菜单栏，默认为no               |
  | resizable  | yes/no | 表示能否通过拖动浏览器窗口的边框改变其大小               |
  | scrollbars | yes/no | 是否允许滚动                                             |
  | status     | yes/no | 表示是否在浏览器窗口中显示状态栏                         |
  |            | yes/no | 表示是否在浏览器窗口中显示工具栏                         |
  |            | 数值   | 表示新窗口的上坐标，不能是负值                           |
  |            | 数值   | 表示新窗口的宽度，不能小于100                            |

  示例：

  ```js
  window.open("http://www.baidu.com/","baiduWindow","height=400,width=400,top=10,left=10,resizable=yes")
  ```

  {% endnote %}

- 如何检测弹出窗口被屏蔽

  新创建的window对象有一个`opener`属性，其中保存着打开它的原始窗口对象。这个属性只在弹出窗口中的最外层window对象（top）有定义，而且指向调用`window.open()`的窗口或框架

  当浏览器内置的屏蔽程序阻止弹出窗口时，`window.open()`可能会返回`null`，此时我们只需要判断：

  ```js
  var baiduWindow = window.open("http://www.baidu.com/","_blank")
  if(baiduWindow==null){
  	alert("window was blocked");
  }
  ```

  当浏览器拓展或者其他程序阻止的弹出窗口，那么`window.open()`通常会抛出错误，这时候我们需要用`try...catch...`来检测，结合上面的内置屏蔽，我们可以使用下面的代码来检测任意情况下的窗口是否被屏蔽：

  ```js
  var blocked = false;
  try{
  	var baiduWindow = window.open("http://www.baidu.com/","_blank");
  	if(baiduWindow==null){
  		blocked = true;
  	}
  }catch(err){
      blocked = true;
  }
  if(blocked){
      alert("the window was blocked")
  }
  ```

{% note warning %}

移动IE浏览器不支持`window.innerWidth`和`window.innerHeight`这些属性，但是可以通过`document.documentElement.clientWidth`和`document.documentElement.clientHeight`来获得

IE6中，`window.innerWidth`和`window.innerHeight`这些属性只在标准模式下有效，在混杂模式下必须通过`document.body.clientWidth`和`document.body.clientHeight`来获得 

{% endnote %}

## 间歇调用和超时调用（定时器功能）

- setTimeout()
- setInterval()
- clearTimeout()
- clearInterval()

{% note warning %}

- 这两个调用的代码都是在全局作用域执行的，所以函数中this的值在非严格模式下指向window对象，严格模式下是`undefined`

- 开发时建议使用超时调用模拟间歇调用

{% endnote %}

## 系统对话框

- alert()：警告对话框
- conform()：选择对话框
- prompt()：提示对话框

# location对象

{% note info %}

`location`是最有用的BOM对象之一，它提供了与当前窗口中加载的文档有关的信息，还提供了一些导航功能。

神奇的是`location`既是window对象的属性，也是document对象的属性，所以`window.location`与`document.location`其实是等价的。

{% endnote %}

## location对象的所有属性

| 属性名   | 例子                   | 说明                                                |
| -------- | ---------------------- | :-------------------------------------------------- |
| hash     | "#content"             | 返回URL中的hash，如果不存在，则返回空字符串         |
| host     | "www.baidu.com:80"     | 返回服务器名称和端口号（如果有）                    |
| hostname | "www.baidu.com"        | 返回不带端口号的服务器名称                          |
| href     | "http://www.baidu.com" | 返回完整的URL，location的toString()方法也返回这个值 |
| pathname | "/document/"           | 返回URL中的目录和（或）文件名                       |
| port     | "8080"                 | 返回URL中指定的端口号，如果不存在，则返回空字符串   |
| protocal | "http:"                | 返回页面使用的协议，通常是`http:`或`https:`         |
| search   | "?q=javascript"        | 返回URL的查询字符串，以问号开头                     |
