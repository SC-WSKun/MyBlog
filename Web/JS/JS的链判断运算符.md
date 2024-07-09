---
title: JS的链判断运算符
date: 2022-03-13 11:50:31
tags: JavaScript
categories: JavaScript
---

# 使用环境

当我们从后端接收数据，或者读取用户填写的表单的时候，经常需要判断读取的变量是否存在，比如一个经典的报错：

```js
const obj = {pro:1}
console.log(obj.pro) //1

const obj = undefined
console.log(obj.pro) //error,cannot read "1" from undefined
```

这时候我们往往会使用条件表达式，或者逻辑表达式来规避这种错误：

```js
console.log(obj?obj.pro:undefined)
//或
if(obj){
	console.log(obj.pro)
}
```

而一旦嵌套得多了会导致判断条件非常冗长：

```js
if(obj && obj.pro1 && obj.pro1.pro2){
	...
}
```

## 链判断运算符`?.`

使用链判断运算符来代替上面的代码的话，就是：

```js
if(obj?.pro1?.pro2){
	...
}
```

上面代码使用了`?.`运算符，直接在链式调用的时候判断，左侧的对象是否为`null`或`undefined`。如果是的，就不再往下运算，而是返回`undefined`。

链判断运算符有三种用法。

- `obj?.prop` // 对象属性
- `obj?.[expr]` // 同上
- `func?.(...args)` // 函数或对象方法的调用，判断函数是否存在，存在就进行调用

示例：

```js
a?.b
// 等同于
a == null ? undefined : a.b
a?.[x]
// 等同于
a == null ? undefined : a[x]
a?.b()
// 等同于
a == null ? undefined : a.b()
a?.()
// 等同于
a == null ? undefined : a()
```

# 注意点

- 短路机制

  ```js
  a?.[++x]
  //等同于
  a == null ? undefined : a[++x]
  ```

  如果`a`是`undefined`或`null`，则x不会进行递增运算

- delete运算符

  ```js
  delete a?.b
  //等同于
  a == null ? undefined : delete a.b
  ```

  如果`a`是`undefined`或`null`，会直接返回undefined，而不会执行delete运算

- 括号的影响

  ```js
  (a?.b).c
  // 等价于
  (a == null ? undefined : a.b).c
  ```

  `?.`对圆括号外部没有影响，所以`.c`总是会执行。一般来说，使用`?.`运算符的场合，不应该使用圆括号。

- 报错场合

  - 构造函数

    ```js
    new a?.()
    new a?.b()
    ```

  - 链判断运算符的右侧有模板字符串

    ```js
    a?.`{b}`
    a?.b`{c}`
    ```

  - 链判断左侧是super

    ```js
    super?.()
    super?.foo
    ```

  - 链运算符用于赋值运算符左侧

    ```js
    a?.b = c
    ```

- 右侧不得为十进制数值

  对于`foo?.3:0`会被解析成`foo ? .3 : 0 `因此如果后面跟着十进制数值，`?.`会被看成三元运算符处理。

  

