---
title: JSX
date: 2021-11-26 22:37:54
tags: React
categories: Web开发
---

> 本篇参考了React的官方文档：https://react.docschina.org/docs/introducing-jsx.html

# JSX是什么

JSX是一种语法糖，用于简化`React.createElement()`这个api的使用的，我们可以通过JSX生成React元素。

这也是React的特色之一，通过JSX，能够将UI很巧妙地结合起来，视觉上起到辅助作用

# JSX语法

JSX跟HTML的语法相似，都是一个个标签的嵌套，这里使用官方文档的例子

```react
const name = 'Josh Perez';
const element = (<h1>Hello, {name}</h1>);   //这里建议用()包裹，避免遇到自动插入分号陷阱

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

可以看到，在JSX中，我们使用`{}`来引入js中的变量，我们也可以在`{}`中放置任何有效的JS表达式

# JSX会被转换成什么

编译之后，JSX会转为**普通JS函数调用**（ React.createElement( ) ），并且对其取值后得到JS对象，所以我们可以把JSX赋给变量，作为函数参数，或者作为函数返回结果

# JSX里面给标签属性赋值

JSX可以使用引号，来将属性值指定为字符串字面量

可以使用大括号，来在属性值中插入一个JavaScript表达式，**注意只有一个括号，跟微信小程序不一样**，**也不能在外面加引号**

```
const element = <div tabIndex= "0"></div>;

const element = <img src={user.avatarUrl}></img>;
```

# JSX可以防止注入攻击（XSS）

React DOM在渲染所有输入内容之前，默认会进行转义。这样可以确保在你的应用中，永远不会注入并非自己明确编写的内容。所有内容在渲染之前都被转换成了字符串。

# JSX与React.createElement( )

JSX转换成React.createElement( )函数调用，React.createElement( )本质上创建了一个JS对象

简化版：

```
( <h1 className="name">Hello</h1> )
等价于
{
	type: 'h1',
	props:{
		className: 'name',
		children: 'Hello'
	}
}
```

我们将这些对象称为**React元素**，React使用它们来构建DOM以及保持随时更新
