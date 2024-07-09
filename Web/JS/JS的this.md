---
title: JS的this
date: 2022-01-04 23:47:26
tags: JavaScript
categories: JavaScript
---

> 参考资料《You Don't Know JS》

# this的作用

> this使函数能对多个环境对象进行复用，而不是针对每个对象定义函数的分离版本

例子：

```js
function identify() {
    return this.name.toUpperCase();
}
function speak() {
    var greeting = "Hello, I'm " + identify.call( this );
    console.log( greeting );
}
var me = {
    name: "Kyle"
};
var you = {
    name: "Reader"
};
identify.call( me ); // KYLE
identify.call( you ); // READER
speak.call( me ); // Hello, I'm KYLE
speak.call( you ); // Hello, I'm READER
```

当然我们也可以使用函数参数的方式传递环境对象，但是使用参数的方式在使用模式越复杂时往往会导致执行环境更乱，所以this相当于提供了一种自动引用恰当的执行环境对象的功能。

# this的误区

> this不是指向当前函数，也不会以任何方式指向函数的词法作用域

错误示范：

```js
function foo() {
    var a = 2;
    this.bar(); //这里想要使用this去访问全局上下文，是错误的
}
function bar() {
    console.log( this.a );
}
foo(); //undefined
```

# this工作原理

> this有依赖于函数调用的上下文条件的特点，所以**this不是在编译时绑定的，而是在执行的时候绑定的**。

当一个函数被调用时，会创建一个执行上下文，记录了函数是从何处被调用，如何被调用，传递了什么参数。其中有一个属性就是函数执行时江北使用的this引用

## Call-site（调用点）

> 既然this是执行的时候绑定的，那么我们就得知道函数是在哪个地方被调用的？

有一个结构存储了**使我们到达当前执行位置而被调用的所有方法**，叫做调用栈，所以我们的调用点就位于当前执行中的函数**之前**的调用

例子：

```js
function baz() {
    // 调用栈是: `baz`
    // 我们的调用点是 global scope（全局作用域）
    console.log( "baz" );
    bar(); // <-- `bar` 的调用点
}
function bar() {
    // 调用栈是: `baz` -> `bar`
    // 我们的调用点位于 `baz`
    console.log( "bar" );
    foo(); // <-- `foo` 的 call-site
}
function foo() {
    // 调用栈是: `baz` -> `bar` -> `foo`
    // 我们的调用点位于 `bar`
    console.log( "foo" );
}
baz(); // <-- `baz` 的调用点
```

## this 绑定的四种规则

### 默认绑定

示例代码：

```js
function foo() {
    console.log( this.a );
}
var a = 2;
foo(); // 2
```

这里是直接调用了`foo`，对`this`使用了默认绑定，所以`this`指向全局对象

这里的`this.a`与全局中的`a`等价，因为此时的`this`绑定了window全局对象，而`var`定义的全局变量会挂载在window对象上。如果用`let `定义就会是`undefined`了

### 隐含绑定

示例代码：

```js
function foo() {
console.log( this.a );
}
var obj = {
a: 2,
foo: foo
};
obj.foo(); // 2
```

首先，注意 `foo()` 被声明然后作为引用属性添加到 `obj` 上的方式。无论 `foo()` 是否一开始就在 `obj` 上被声明，还是后来作为引用添加（如上面代码所示），这个 **函数** 都不被 `obj` 所真正“拥有”或“包含”。简单来说就是，这样添加只是让`obj`可以调用`foo`，但是`foo`其实并没有附着在`obj`上

这里的`foo`被`obj`所引用。当一个方法引用存在一个环境对象（比如：obj）时，隐含绑定规则规定：这个对象应该被这个函数调用的`this`所绑定

顺带一提，只有引用链的最后一层是影响调用点的。比如：

```js
obj1.obj2.foo()
```

最后指向`obj2`



### 明确绑定

> **隐含丢失**
>
> 示例代码：
>
> ```js
> function foo() {
> console.log( this.a );
> }
> var obj = {
> a: 2,
> foo: foo
> };
> var bar = obj.foo; // 函数引用！
> var a = "oops, global"; // `a` 也是一个全局对象的属性
> bar(); // "oops, global"
> ```
>
> 这里看似绑定了`obj`，其实只是传递了一个`foo()`的引用给`bar`而已
>
> 明确绑定就是为了解决这一情况，使用`call`和`apply`等函数，强制一个函数调用某个特定对象作为它的`this`

示例代码：

```js
function foo() {
console.log( this.a );
}
var obj = {
a: 2
};
foo.call( obj ); // 2
```

`call`函数的第一个参数指的是`this`要绑定的对象。如果第一个参数是一个基本类型，则这个值会被包装在它的对象类型中，比如`new Boolean(..)\String(..)\Number(..)`，这个操作被称为封装（Boxing）

#### 明确绑定：硬绑定(bind)

> 在函数中手动调用call方法，这种方法可以保证函数不会丢失自己原本的`this`

```js
function foo() {
console.log( this.a );
}
var obj = {
a: 2
};
var bar = function() {
foo.call( obj );
};
bar(); // 2
setTimeout( bar, 100 ); // 2
// `bar` 将 `foo` 的 `this` 硬绑定到 `obj`
// 所以它不可以被覆盖
bar.call( window ); // 2
```

`bar`函数中手动调用`foo`并绑定了`obj`，无论`bar`以后如何调用，`foo`都是绑定``obj`的

ES5中的`bind`函数就是一种硬绑定

### new绑定

> 任何函数，包括像 `Number(..)`这样的内建对象函数都可以在前面加上 `new` 来被调用，这使函数调用成为一个 *构造器调用（constructor call）*。这是一个重要而微妙的区别：实际上不存在“构造器函数”这样的东西，而只有函数的构造器调用。

new 做了什么：

- 创建了一个全新的对象
- 把新构建的对象接入原型链
- 这个新构建的对象被设置为函数调用的`this`绑定
- 除非函数返回一个它自己的其他对象，否则这个被`new`调用的函数将自动返回这个新构建的对象

## 绑定规则的优先顺序

1. new绑定 
2. 明确绑定 
3. 隐含绑定 
4. 默认绑定

## 绑定的特例

- 函数的间接引用时是默认绑定
- 软化绑定

# 词法this

## 箭头函数

ES6引入了一种新的函数：箭头函数，箭头函数从封闭它的作用域采用`this`绑定



