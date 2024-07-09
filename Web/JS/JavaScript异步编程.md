---
title: JavaScript异步编程
date: 2022-03-20 12:02:28
tags: [ES6, JavaScript]
Category: JavaScript
---

# 引入：什么叫同步、异步？

同步、异步其实是串行会出现的一种概念。

同步就是连续地做任务，比如我做蛋炒饭，淘完米放进电饭煲里面坐在一旁等它煮熟，然后再炒饭，这个就是同步的。

异步就是将任务分为几部分，中间可以夹杂着别的事情，比如还是做蛋炒饭，淘完米放进电饭煲里面，等它煮熟的时候顺便准备其他饭菜，煮熟后接着再炒饭

而并行就是我左手炒饭右手炒菜，同时进行两个任务，这意味着你要同时做两件或者多件事情，这时候对你的要求会比较高。

# JS的确定性

因为JS是为了Web交互而开发的，为了适配浏览器， JS引擎在设计的时候就是一个单线程结构，也就是说JS的运行是原子性的。所以JS无法实现并行，只可以通过调度实现并发。因此JS具有一定的确定性，不需要考虑并行情况（注意不是完全确定性）。

# 竞合状态

为什么说JS是一定的确定性呢？我们看下下面的代码：

```js
var a = 1;
var b = 2;
function foo() {
    a++;
    b = b * a;
    a = b + 3;
}
function bar() {
    b--;
    a = 8 + b;
    b = a * 2;
}
// ajax(..) 是某个包中任意的Ajax函数
ajax( "http://some.url.1", foo );
ajax( "http://some.url.2", bar );
```

因为JS虽然是原子性的，但是JS的回调机制会导致事件（这里的`foo`和`bar`）的触发顺序是不确定的。比如第一个事件处理事件比较长，第二个事件先触发回调了。

所以JS在事件顺序的水平上，JS是不确定的。

而对于这种函数顺序的不确定性我们称之为”竞合状态“。

# 并发的几种处理方式

- race
- all
- 协作并发，对长时间的进程进行切割，使用异步进行调用。

# 事件机制

对于事件的调度，确定事件执行顺序的机制有以下三个共同确定：

- 事件轮询（Event Loop）
- 任务队列（Task Queue）
- 工作队列（Job Queue）

感兴趣可以看另一篇博客[JS的事件循环](http://112.74.85.250:39/2022/01/17/JS%E7%9A%84%E4%BA%8B%E4%BB%B6%E5%BE%AA%E7%8E%AF/)，下面介绍一些异步处理方法：

---

# callback

- **回调地狱，代码耦合性比较高**

  ```js
  doA( function(){
      doB();
      doC( function(){
          doD();
      } )
      doE();
  } );
  doF();
  ```

  如果括号内的函数是异步函数，这种代码会有反直觉的执行顺序。

  - `doA()`
  - `doF()`
  - `doB()`
  - `doC()`
  - `doE()`
  - `doD()`

  我们要想确定执行顺序，需要不断在函数间反复跳跃，给调试带来了麻烦

- **信任问题**

  当我们使用第三方的函数时，我们不知道对方的返回结果，返回时机，执行逻辑。可能会存在很多情况：

  - 调用回调过早（在它开始追踪之前）

  - 调用回调过晚 (或不调)

  - 调用回调太少或太多次

  - 没能向你的回调传递必要的环境/参数

  - 吞掉了可能发生的错误/异常

  这些都是我们要考虑的。这就会导致你的代码非常冗长。
  
- **一些改善的努力**

  - 在一些API中加入了`failure()`的回调，可以避免错误被吞掉。

    或者使用`错误优先风格`的回调方式，也称为`Node风格`。它的特点是回调的第一个对象作为错误对象保留。例如：

    ```js
    function response(err,data) {
        // 有错？
        if (err) {
            console.error( err );
        }
        // 否则，认为成功
        else {
            console.log( data );
        }
    }
    ajax( "http://some.url.1", response );
    ```

    但是其实还是没有解决信任问题，因为失败回调也是取决于第三方，你甚至可能得到两个信号都没有收到的情况。

  - 添加定时器，设置超时取消事件

  - 总是异步地调用回调

    当我们不知道当前API是否是异步执行时，可以使用`asyncify()`这样的工具：

    ```js
    function asyncify(fn) {
        var orig_fn = fn,
        intv = setTimeout( function(){
            intv = null;
            if (fn) fn();
        }, 0 );
        fn = null;
        return function() {
            // 在`intv`计时器触发前触发，代表是同步函数
            if (intv) {
                fn = orig_fn.bind.apply(
                orig_fn,
                // 将包装函数的`this`加入`bind(..)`调用的
                // 参数，同时currying其他所有的传入参数
                [this].concat( [].slice.call( arguments ) )
                );
            }
            // 异步
            else {
                // 调用原版的函数
                orig_fn.apply( this, arguments );
            }
        };
    }
    ```

    

# Promise

终于来到ES6中非常重要的一个部分，Promise给异步编程提供了一种新的理论。能不能熟悉应用Promise代表着这个开发者是否真的理解了异步的过程，而不是简单地学习API。

因为Promise内容有点多，我放在另一篇博客：[ES6的Promise]()

# async和await

{% note info %}

首先指出，`async`和`await`本质上其实是`Generator`函数的语法糖，`Generator`函数可以用来提供异步编程的解决方案，所以我们先了解下`Generator`函数

{% endnote%}

## Generator函数

> Generator函数其实是迭代器生成函数，它使用`*`进行标记，在里面使用yield进行“阻塞”
>
> 形如：
>
> ```js
> function* func(){
> 	yield "hello";
> 	yield "world";
> 	return "end";
> }
> ```
>
> Generator函数可以理解为一种状态机，使用yield将状态隔开，当next( )触发的时候进入下一个状态
>
> 我们也可以将Generator函数赋给`Symbol.iterator`属性，这样就可以

### yield

yield会返回一个对象作为`next`的返回值，形如

```json
{
	value: "",
	done: ""
}
```

这个其实跟迭代器返回的对象相同，value代表yield后面紧跟的值，done代表迭代是否结束

{% note warning%}

`yield`后面的表达式，只有当调用`next`方法、内部指针指向该语句时才会执行

{% endnote %}

### next

next方法可以携带一个参数，作为`yield`的返回值使用

比如：

```js
function* foo(x) {
    var y = 2 * (yield (x + 1));
    var z = yield (y / 3);
    return (x + y + z);
}
var a = foo(5);
a.next() // Object{value:6, done:false}
a.next() // Object{value:NaN, done:false}
a.next() // Object{value:NaN, done:true}
var b = foo(5);
b.next() // { value:6, done:false }
b.next(12) // { value:8, done:false }
b.next(13) // { value:42, done:true }
```

## for...of

> for...of函数会自动遍历Generator函数生成的迭代器对象，并且不需要next方法

示例：

```js
function* foo() {
    yield 1;
    yield 2;
    yield 3;
    yield 4;
    yield 5;
    return 6;
}
for (let v of foo()) {
	console.log(v);
}
// 1 2 3 4 5
```

# ...

