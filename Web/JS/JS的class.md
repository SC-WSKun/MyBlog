---
title: JS的class
date: 2022-03-13 23:19:49
tags: JavaScript
categories: JavaScript
---

>参考链接：阮一峰ES6标准入门教程

# class简介

从C++转JS的同学在写面向对象编程的时候一定会觉得，JS的面向对象很别扭。因此ES6引入了Class来贴近传统语言的面向对象写法。

我们先复习下JS的面向对象一般写法：

```js
function fatherType(x){
	this.x = x
}

let obj1 = new fatherType(1)
```

新引入的class写法：

```js
class fatherType{
	constructor(x){
		this.x = x;
	}
    toString(){
        return `${this.x}`;
    }
    [toNumber](){
        return Number(this.x)
    }
}

let obj1 = new fatherType(1)
```

- 可以发现，跟传统面向对象语言一样，在class里有一个构造函数定义，而在js中统一命名为`constructor`。
- 其中`this`指向实例对象。
- 在类里面定义新的函数不需要加上function，直接把函数定义放进去即可。
- 属性名可以使用属性表达式
- 方法之间不需要逗号分割。

- js里面还有个重要的概念是原型，在类里面定义的方法其实都定义在原型上 

  ```js
  class fatherType{
  	constructor(){
  		...
  	}
      
      func1(){
          ...
      }
          
      func2(){
          ...
      }
  }
  //等价于
  fatherType.prototype = {
      constructor(){},
      toString(){},
      toValue(){},
  }  
  ```

- 所以给类添加新方法可以使用`Object.assign`方法

  ```js
  Object.assign(fatherType.prototype,{
  	func3(){},
  	func4(){}
  })
  ```

- 类的内部定义的所有方法都是不可枚举的

# constructor方法

`constructor`方法是类的默认方法，通过`new`命令生成对象实例时，自动调用该方法。一个类必须有`constructor`方法，如果没有显式定义，一个空的`constructor`方法会被默认添加。

```js
class Point {}
// 等同于
class Point {  
    constructor() {}
}
```

# getter和setter

与 ES5 一样，在“类”的内部可以使用`get`和`set`关键字，对某个属性设置存值函数和取值函数，拦截该属性的存取行为。

```js

class MyClass {  
	constructor() {    
		// ...  
	}  
	get prop() {    
		return 'getter';  
	}  
	set prop(value) {
    	console.log('setter: ' + value);  
    }
}
let inst = new MyClass();
inst.prop = 123;
// setter: 123
inst.prop
// 'getter'
```

存值函数和取值函数是设置在属性的 Descriptor 对象上的。

```js
class CustomHTMLElement {
    constructor(element) {
        this.element = element;
    }
    get html() {
        return this.element.innerHTML;
    }
    set html(value) {
        this.element.innerHTML = value;
    }
}
var descriptor = Object.getOwnPropertyDescriptor(
    CustomHTMLElement.prototype, "html"
);
"get" in descriptor  // true
"set" in descriptor  // true
```

# class表达式

与函数一样，类也可以使用表达式的形式定义

```js
const MyClass = class Me{
	...
}
```

在初始化实例的时候，我们只能使用MyClass而不能使用Me

我们也可以省略class的名字，类似匿名函数：

```js
const MyClass = class{
	...
}
```

还可以使用类似立即表达式的立即执行Class：

```js
let person = new class{
	constructor(name){
		...
	}
}(“kunkun”)
//上面初始化了一个name为kunkun的实例
```

# 注意

- 严格模式

  类和模块的内部默认为严格模式

- 不存在变量提升

- `name`属性

  本质上，`class`只是构造函数的一个语法糖，所以`class`也有函数的`name`属性

- Generator方法

  
