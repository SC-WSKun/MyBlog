---
title: JS的原型和原型链
date: 2021-12-08 19:14:40
tags: JavaScript
categories: JavaScript
---

# prototype

每创建一个函数，解析器都会向函数中添加一个属性prototype，这个属性是一个对象，也就是我们所谓的原型对象。

创建构造函数时，可以将公有的属性和方法，统一添加到构造函数的原型对象中，这样每个对象都有这些属性和方法了。

不同的函数有不同的prototype。

{% note info %}

由构造函数创建出来的对象也有一个隐藏的属性指向prototype，这个隐藏的对象叫做\_\_proto\_\_。即对象的\_proto\_和函数的prototype指向同一个原型对象，这个\_\_proto\_\_其实并不存在于函数原型中，实际上，它来自于Object.prototype，也就是所有对象的原型，

{% endnote %}

## 访问对象属性或方法的机制

当我们访问对象的一个属性或方法时，如果有则直接使用；如果没有，则会去原型对象中寻找。

{% note warning %}

对象实例可以访问原型的值，但是不能通过对象实例重写原型中的值。当对象实例中添加一个与原型中的属性同名的属性时，会在实例中创建该属性并将原型中的属性屏蔽

{% endnote %}

# 从原型获取构造函数

原型中有一个constructor属性，指向该原型的构造函数

假设此时有以下代码：

```
function Person(){

}
let person = new Person();
```

此时我们可以使用下面方式访问构造函数：

person. constructor  =  Person.prototype.constructor  =  person.\_\_proto\_\_.constructor

# 与原型有关的方法

## 判断对象中是否含有某个属性

如果使用 `in` 则，当对象中没有该属性但是原型中有时也会返回true。

所以我们应该使用`hasOwnProperty(name)`

## 获得对象中的可枚举的实例属性

使用`Object.keys( )`，接收一个对象作为参数，返回包含所有可枚举属性的字符串数组

## 判断原型关系

`isPrototypeOf( )`是用来确定对象之间是否存在原型关系

例如：

```js
Person.prototype.isPrototypeOf(person1) //true
```

## 获得实例的原型

`getPrototypeOf( )`是Object用来获取实例的原型对象的函数

```js
Object.getPrototypeOf(person1) == Person.prototype //true
```



# 原型的级次

如果一直没有找到属性，因此一直沿着原型链往上寻找，当找到Object对象的原型时，如果还没有找到，则返回undefined;

