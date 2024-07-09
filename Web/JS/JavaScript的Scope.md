---
title: JavaScript的作用域（Scope）
date: 2021-12-07 09:41:11
tags: JavaScript
categories: JavaScript
---

> 参考链接：https://www.smashingmagazine.com/2009/08/what-you-need-to-know-about-javascript-scope/
>
> 参考书目：《You Don't Know JS》

# Scope（作用域）是什么

几乎所有语言的最基础模型就是具有在变量中存储值的能力，并且可以在稍后取出或者修改这些值。

那这就引出了两个疑问，这些变量存储在了哪里？程序如何在需要的时候找到它？

而解决这两个问题需要定义一组明确的规则，它定义如何在某些位置存储变量，以及如何在稍后找到这些变量，我们称之为*Scope（作用域）*

我们可以给个比较明确的定义：

> Scope是用来收集并维护一张所有被声明的标识符（变量）的列表，并对当前执行中的代码如何访问这些变量制定了一组严格的规则

# Scope的结构

Scope可以理解为当前执行函数的指向所有变量对象的指针的链表，里面的每一个指针指向了一个变量对象。

# Scope如何创建

后台的每个执行环境都有一个表示变量的对象——变量对象。全局环境的变量对象始终存在，而函数的局部环境的变量对象，只会在函数执行的过程中存在。

在创建函数时，会创建一个预先包含全局变量对象的作用域链，这个作用域链被保存在`Scope`属性中。当调用函数时，会为函数创建一个执行环境，然后通过复制函数的`Scope`属性中的对象构建起执行环境的作用域链。

然后将一个活动对象被创建推入执行环境作用域链的前端，这个活动对象存的就是这个函数里面定义的变量以及传进来的参数

# Scope如何运作

## `var a = 2` 的编译过程

编译器看到的`var a = 2`其实是两条语句：`var a`和`a = 2`，一个是编译器声明一个变量，一个是引擎在`Scope`中查询这个变量并给它赋值

- 处理过程：

  > 遇到`var a`编译器让*Scope*去查看特定的作用域集合，判断变量a是否已经存在：
  >
  > - 如果是，编译器会忽略此声明
  >
  > - 如果否，编译器会让作用域去为这个作用域集合声明一个称为a的新变量
  >
  > 遇到`a = 2`引擎运行的代码首先会让`Scope`去查看在当前的作用域集合中是否有一个`a`可以被访问：
  >
  > - 如果有，引擎就使用这个变量
  > - 如果没有，引擎就会沿着作用域链往上面查找（具体见下文）

## LHS和RHS

编译器在执行`a = 2`这个指令的时候，必须查询变量`a`来看他是否被声明过了，这时候就用到了"LHS"查询和"RHS"查询，这个查询是干什么的呢？首先`L`和`R`代表的是左边和右边。这里的左右是指赋值计算的左右，也就是`=`，举个例子：

> 在`a = 2`中，a在等号左边，我们称指向a的引用为LHS引用

不过严格地讲，右的定义其实是非左，再举个例子：

> 在`console.log(a)`中，不存在等号，所以a不是LHS引用，而是RHS引用

注意，上面的 `2`不是RHS引用，这个引用的针对对象要求是一个变量

那这两个引用有什么意义呢？

我们可以结合`=`的语义来分析，LHS引用可以理解为**赋值的目标（容器）**，RHS引用可以理解为**赋值的源**

我们再举个例子：

```js
function foo(a) {
	console.log( a ); // 2
}
foo( 2 );
```

可以分析下这里哪里产生了RHS引用，哪里有LHS引用：

注：这里有1处LHS引用，2处RHS引用

## Scope的嵌套查询

刚刚我们了解了如何判断LHS和RHS，那么他们是怎么实现这个查询的呢？

就像一个代码块或函数被嵌套在另一个代码块或函数中一样，作用域可以被嵌套在其他的作用域中。所以，如果在直接作用域中找不到一个变量的话，*引擎* 就会咨询下一个外层作用域，如此继续直到找到这个变量或者到达最外层作用域（也就是`global（全局）`作用域）

总而言之，Scope的嵌套遍历的简单规则是：

> 引擎从当前执行的 Scope开始，在里面查找变量，如果没有找到，就向上走一级继续查找，如此类推。如果到了最外层的全局作用域，那么查找就会停止，无论它是否找到了变量。

## Reference Error

> 作用域出现错误

看到这里你可能有点疑惑，刚才讲的LHS和RHS，在查询上好像都是向上查询，感觉没有什么区别？

在查询的流程中，确实没有什么区别，但是当变量还没有被声明（即所有作用域中都没有这个变量）的时候，LHS和RHS的工作方式是不一样的。

举个例子：

```js
function foo(a) {
	console.log( a + b );
	b = a;
}
foo( 2 );
```

当`b`的第一次RHS查询时，它是找不到的，此时引擎会抛出 `ReferenceError`。

而当 `b`进行第一次LHS查询，到达顶层作用域都没找到它，而且程序不是“严格模式”的情况下，将会在 **全局作用域**中创建一个同名的新变量，比如`b = a`就会隐式地在全局创建一个 `b`的变量

显然这是很危险的，可能不知不觉就在全局作用域中定义了一个自己不知道的变量，所以 ES5 引入了“严格模式”，其中有一条规则就是不允许自动/隐含的全局变量被创建。这时，LHS查询就不会隐式创建去阿奴变量了，也会抛出 `ReferenceError`

## Type Error

如果一个 RHS 查询的变量被找到了，但是你试着去做一些这个值不可能做到的事，比如将一个非函数的值作为函数运行，或者引用 `null` 或者 `undefined` 值的属性，那么 引擎就会抛出一个不同种类的错误，称为 `TypeError`

`ReferenceError` 是关于 Scope 解析失败的，而 `TypeError` 暗示着 Scope 解析成功了，但是试图对这个结果进行了一个非法/不可能的动作。

# Scope与闭包

这是一个非常神奇的部分，可以看看我的另外一篇博客：[JS闭包](http://112.74.85.250:39/2021/12/07/JS%E9%97%AD%E5%8C%85/)

# 动态作用域？

我们先来看下面一段代码：

```js
function foo() {
	console.log( a ); //2 还是 3
}
function bar() {
	var a = 3;
	foo();
}
var a = 2;
bar();
```

你觉得这段代码会输出多少？假设JS具有动态作用域，那么当我们对`a`进行RHS查询的时候，是不是会向上查询到 `bar`的作用域，得到 `a`的值为3呢？可惜答案是错的。

![image-20211221134702066](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211221134702066.png)

所以**JS并没有动态作用域，他只拥有词法作用域**，当这个代码在编译的时候，就已经把作用域确定下来了，`a`从 `foo`向上查询到外层 `global`，所以打印出的值为2.

那有的人可能会说，不对呀，之前我写过的代码里有类似动态作用域的机制呀？没错，你说的那个是 `this`（具体可以看下文），你确实可以把它当做JS的动态作用域的实现，但是JS实际上没有动态作用域。

# this

JavaScript使用`this`来获得当前作用域，也就是拥有当前执行代码的对象，有一个比方我非常喜欢

> *At any point in your JavaScript code, you can ask “Help! Where am I?” and get back an object reference.*

注意`this`指向的是拥有当前执行代码的对象，比如下面的代码：

```js
function func(){
	console.log(this) //当前执行的代码是func();，所以this指向的是func();所在的global作用域
}
func();
```

`this` 关心的是 **函数是如何被调用的**，这揭示了 `this` 机制与动态作用域的想法有非常紧密的关联

那这个`this`有什么用呢？对于JS的对象，`this`会很有用。看下面的例子：

```js
var myObj = {
	func: function(){
		console.log(this)
	}
}
myObj.func(); //可以发现console出来的是myObj
```

所以我们可以用this来区分当前执行的对象（注意不是类定义）：

```js
function MyClass() {
  this.func = function() {
    console.log(this);
  }
}

var obj1 = new MyClass();
var obj2 = new MyClass();

instance1.func(); // obj1
instance2.func(); // obj2
```

# 如何解决this指向混乱的问题

JS有一些库可以帮助我们处理this的指向

## PROTOTYPE

Prototype提供了`bind`方法来固定this的指向

```js
var products = ['Shoes', 'Sweater', 'Jeans', 'Wig'];

function showCount() {
  for(var i = 0; i < number; i++) {
    document.body.innerHTML += this[i] + '. ';
  }
}

var fn = showCount.bind(products);
fn(2); // outputs Shoes. Sweater. to the documentxxxxxxxxxx
```

可以看到this指向的不是window对象，而是products



bind还可以记住你想要传递的参数，比如：

```js
var showOne = showCount.bind(products, 1);
var showFour = showCount.bind(products, 4);
showOne(); // outputs Shoes.
showFour(); // output Shoes. Sweater. Jeans. Wig.
```



Prototype还提供了`showCountHandler`的函数，他会多传一个事件对象参数，可以用来屏蔽默认事件

```js
var products = ["Shoes", "Sweater", "Jeans", "Wig"];
Event.observe($('clickbtn'),'click',showCountHandler.bindAsEventListener(products,2));
function showCountHandler(e, number) {
  console.log(e);
  for (var i = 0; i < number; i++) {
    document.body.innerHTML += this[i] + ". ";
  }
  Event.stop(e);
}
```

## 原生

### CALL

```js
showCount.call(products,4)
```



### APPLY

apply与call在于apply的第二个参数是一个数组，对于不知道要传多少个参数的时候比较友好

在ES6引入`...`运算符之后，我们可以看到

```
let argument = [1,2];

showCount.apply(products, argument);
等价于
showCount.bind(products, ...argument);
```

### 箭头函数（并不是好的解决方案）

在ES6中引入了一种新的语法，叫做箭头函数。详情可以看我另一篇博客。



