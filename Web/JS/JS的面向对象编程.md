---
title: JS的面向对象编程
date: 2022-01-26 09:21:26
tags: JavaScript
categories: JavaScript
---

# 对象的创建

最简单的创建对象的方式就是`new`一个`Object`

```js
var person = new Object();
person.name = "kunkun";
person.sayName = function(){
	alert(this.name)
}
```

# `Object`的属性

{% note info %}

ECMAScript中有两种属性：数据属性和访问器属性

{% endnote %}

## 数据属性

- `Configurable`：表示能否通过`delete`删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性。默认为`true`。
- `Enumerable`：表示能否通过`for-in`循环返回属性。默认为`true`。
- `Writable`：表示能否修改属性的值。默认为`true`。
- `Value`：包含这个属性的数据值，读取属性值的时候从这个位置读取，写入的时候把新值写入这个位置，默认为`undefined`

{% note warning%}

要修改属性默认的特性，必须使用`Object.defineProperty()`方法。这个方法接收三个参数：属性所在的对象、属性的名字和一个描述符对象。

描述符对象：`configurable、enumerable、writerable和value`，前三个默认为`false`

例如：

```
var person  = {};
Object.defineProperty(person,"name",{
	writable: false,
	value: "kunkun"
})

Object.defineProperties(person,{
	name:{
        writable: false,
        value: "kunkun"
	},
    age:{
        writable: false,
        value: "18"
	},
},)
```

{% endnote %}

## 访问器属性

- `Configurable`：表示能否通过`delete`删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为数据属性。默认为`true`。
- `Enumerable`：表示能否通过`for-in`循环返回属性。默认为`true`。
- `Get`：在读取属性时调用的函数。默认为`undefined`
- `Set`：在写入属性时调用的函数。默认为`undefined`
