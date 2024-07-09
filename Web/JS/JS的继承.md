---
title: JS的继承
date: 2021-12-25 23:00:12
tags: JavaScript
categories: JavaScript
---

# 原型链

> 将父类的实例赋给子类的原型

```js
function SuperType(){
	this.property = true;
}

SuperType.protoType.getSuperValue = function(){
    return this.property;
}

function SubType(){
    this.subproperty = false;
}

//继承
SubType.prototype = new SuperType();
```

缺点：

1. 对于引用类型的属性，子对象共用同一个地址空间
2. 创建子类型的实例时，不能向超类型的构造函数中传递参数（不影响其他对象实例）

# 借用构造函数

> 在子类构造函数中调用父类的构造函数，并且用`apply`或者`call`在新创建的对象上执行构造函数

```js
function SuperType(){
	this.colors = ["red"];
}

function subType(){
    SuperType.call(this);
}

var instance1 = new SubType();
instance1.colors.push("black");
console.log(instance1);

var instance2 = new SubType();
console.log(instance2);
```

缺点：

1. 不能实现函数复用，强制所有类型必须使用借用构造函数的形式继承

# 组合继承

> 将前面两种继承组合起来，属性使用借用构造函数继承，方法使用原型链继承，是JS中最常用的继承模式

```js
function SuperType(name){
	this.name = name;
}

function SubType(){
	SuperType.call(this,name)
}

SubType.prototype = new SuperType();
SubType.prototype.constructor = SubType;//重写prototype会导致constructor丢失
let instance1 = new SubType();

```

缺点：

1. 调用两次父类的构造函数，导致有两组属性，一组位于实例中，一组位于原型上，实例中的属性把原型中的属性屏蔽了

# 原型式继承

> 绕过构造函数，直接使用原型进行继承并创建对象

# 寄生式继承

> 寄生式继承其实是在原型式继承上的改进，在封装的函数中对对象进行增强

```js
function createAnother(original){
	var clone = object(original);//也可以用其他生成新对象的方法
    clone.sayHi = function(){
        alert("hi");
    }
    return clone;
}
```

缺点：

与借用构造函数相同，寄生式继承无法做到函数复用

# 寄生组合式继承

>解决组合式继承调用两次父类构造函数的问题

使用原型式继承绕过`SubType.prototype = new SuperType`( )，改为`subType.prototype = object(superType.prototype)`
