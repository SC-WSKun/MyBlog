---
title: ES6的Promise
date: 2022-03-28 17:45:16
tags: [ES6, JavaScript]
category: JavaScript
---

# 回调

要了解Promise回调，我们首先要了解，什么是回调？

> 回调是一种处理异步函数的机制，它能保证一个异步函数在另一个异步函数执行后再触发

# 引入：什么是Promise

这里用《You Don‘t Know JS》中的例子抽象一下：

>想象这样的场景：我走到快餐店的柜台前，点了一个起士汉堡。并交了1.47美元的现金。通过点餐和付款，我为得到一个 *值*（起士汉堡）制造了一个请求。我发起了一个事务。
>
>但是通常来说，起士汉堡不会立即到我手中。收银员交给一些东西代替我的起士汉堡：一个带有点餐排队号的收据。这个点餐号是一个“我欠你”的许诺（Promise），它保证我最终会得到我的起士汉堡。
>
>于是我就拿着我的收据和点餐号。我知道它代表我的 *未来的起士汉堡*，所以我无需再担心它——除了挨饿！
>
>在我等待的时候，我可以做其他的事情，比如给我的朋友发微信说，“嘿，一块儿吃午餐吗？我要吃起士汉堡”。
>
>我已经在用我的 *未来的起士汉堡* 进行推理了，即便它还没有到我手中。我的大脑可以这么做是因为它将点餐号作为起士汉堡的占位符号。这个占位符号实质上使这个值 *与时间无关*。它是一个 **未来的值**。
>
>最终，我听到，“113号！”。于是我愉快地拿着收据走回柜台前。我把收据递给收银员，拿回我的起士汉堡。
>
>换句话说，一旦我的 *未来的值* 准备好，我就用我的许诺值换回值本身。
>
>但还有另外一种可能的输出。它们叫我的号，但当我去取起士汉堡时，收银员遗憾地告诉我，“对不起，看起来我们的起士汉堡卖光了。”把这种场景下顾客有多沮丧放在一边，我们可以看到 *未来的值* 的一个重要性质：它们既可以表示成功（resolve）也可以表示失败（reject）。
>
>每次我点起士汉堡时，我都知道我要么最终得到一个起士汉堡，要么得到起士汉堡卖光的坏消息，并且不得不考虑中午吃点儿别的东西。
>
>**注意：** 在代码中，事情没有这么简单，因为还隐含着一种点餐号永远也不会被叫到的情况，这时我们就被搁置在了一种无限等待的未解析状态（pending）。我们待会儿再回头处理这种情况。

在上面的例子中，我们可以看到斜体部分有一个很重要的特点：**与时间无关**。

这个与时间无关怎么理解呢？我们简单讲一下：

Promise你可以理解为一个事件，这个事件有两种结果，成功或失败，而Promise对这个动作进行了包装，使得我们不必去关心等待这个动作，只需要关心事件的结果。所以从外部来看，Promise是与时间无关的。另外Promise还有一个特点，Promise的状态一旦被解析，它就永远保持那个状态。所以我们不用担心在传递过程中会被意外或恶意地更改，这是理解Promise最强大且最重要的概念之一。

总而言之：**Promise是一种用来包装与组合 未来值，并且可以很容易复用的机制。**

# Promise的监听

正如我们刚才所说的，一个独立的Promise作为一个 未来值 事件。但还有另外一种理解Promise的方法：在一个异步任务的两个或以上步骤中，作为一种流程控制机制——俗称“这个然后那个”。

我们假设调用`foo(..)`来执行某个任务。它可能会立即完成任务，也可能会花一段时间完成。

我们仅仅想简单地知道`foo(..)`什么时候完成，以便于我们可以移动到下一个任务。换句话说，我们想要一种方法被告知`foo(..)`的完成，以便于我们可以继续执行代码。

在典型的JavaScript风格中，如果你需要监听一个通知，你很可能会想到事件（event）。那么我们可以将我们的通知需求重新表述为，监听由`foo(..)`发出的“完成”事件。

我们可以写一个伪代码：

```js
foo(){
	//...
}
on(foo"complete"){
	//...
}
on(foo"error"){
	//...
}
```

看上去非常优秀的结构，`foo`甚至不知道调用它的代码监听了这些事件，这构成了一个非常美妙的“关注分离”。

但是事实上在JS中并不可能实现这么美妙的代码（其实也不太实际）。所以我们对他做了一些修改：

```js
foo(){
	//...
	return listener;
}

let evt = foo();

evt.on("completion",function(){
	//...
})

evt.on("error",function(){
	//...
})
```

`foo()`这次创建并返回了一个具有事件监听能力的对象，调用方代码接收并在它上面注册了两个事件监听器。

这样有一个重要的好处是，代码的每个分离部分都可以被赋予事件监听能力，而且它们都可在`foo(..)`完成时被独立地通知，来执行后续的步骤，而这也就是Promise的机制之一。通过返回具有事件监听能力的`promise`对象，解决了`回调地狱`的问题，变成了链式回调。

#  Promise“事件”

为什么标题的事件要打引号呢？

因为严格来讲，Promise并不是一个“事件”，其实`.then()`才是一个事件，或者更准确地讲，是`.then()`注册了`fulfillment（完成）`和`rejection（拒绝）`事件。

我们把前面的`listener`替换成`promise`：

```js
foo(){
	//...
	return new Promise(function(resolve,reject){
        //成功则调用resolve()
        //失败则调用reject()
    });
}

let p = foo();

bar(p);

baz(p);
```

{% note info %}

像`new Promise(function(..){..})`这样的模式我们称为"揭示构造器（revealing constructor）"。被传入的函数会被**立即执行**（不会像`.then()`的回调一样被异步推迟），而且它提供了两个参数，我们叫它们`resolve`和`reject`。这些是`Promise`的解析函数。`resolve(..)`一般表示完成，而`reject(..)`表示拒绝。

{% endnote %}

你可能可以猜到`bar()`和`baz()`函数内部是使用`.then()`进行解析：

```js
function bar(fooPromise){
	fooPromise.then(
    	function(){
            //fulfilled
        },
        function(){
            //rejected
        }
    )
}
```

- Promise解析没有必要一定发送信息，它可以仅仅作为一种流程控制信号。

我们也可以把`.then`提取出来：

```js
function bar(){
	//...
}

function nextbar(){
	//...
}

var p = foo();

p.then(bar1,barFail);

p.then(bar2,nextbarFail);
```

可能之前接触过类似代码的同学会觉得应该使用`p.then(..).then(..)`，而不是`p.then(..);p.then(..);`

这是两种完全不同的行为，

在第一个代码段的方式中，无论`foo(..)`是否成功`bar2(..)`都会被调用，如果被通知`foo(..)`失败了的话它提供自己的后备逻辑。显然，`bar2(..)`也是这样做的。

在第二个代码段中，`bar2(..)`仅在`foo(..)`成功后才被调用，否则`oopsBar(..)`会被调用。`baz(..)`也是。

# 如何判断是否是Promise类型（Duck Typing）

我们可能会认为：判断是不是Promise，那就用`instance of`不就行了吗？

事实上可能只适用于一般情况，因为我们使用的Promise可能是经过加工过的。一个库或框架可能会选择实现自己的`Promise`而不是用ES6原生的`Promise`实现。甚至你可能在一个不支持`Promise`的旧版本的浏览器中使用库来使用`Promise`。

所以最后判断是否是`Promise`的方案就是判断是否是`thenable`类型的值，即任何具有`.then(..)`的函数或者对象，我们假定它都是符合`Promise`的`thenable`

我们将这种类型检查称为“鸭子类型检查（Duck Typing）”——“如果它看起来像一只鸭子，并且叫起来像一只鸭子，那么它一定是一只鸭子”

这种类型检查看起来像是这样的：

```js
if (
    p !== null &&
    (
        typeof p === "object" ||
        typeof p === "function"
    ) &&
	typeof p.then === "function"
) {
	// 认为它是一个thenable!
}
else {
    // 不是一个thenable
}
```

但是这样显然会出现很多问题，任意一个拥有`then`这个方法的对象都会被视为`Promise`对象，而且假设这个对象被作为原型使用，那么它的子类型也全部都被视为`Promise`

而且这也意味着，一些现存的库里面使用了命名为`then`的方法必须要改名，或者指出与`Promise`不兼容，显然为了这个检查屏蔽所有`then`方法不是一个好主意，后续也会对这个检查做出一些修改。

# Promise的信任问题

还记得前面我们在[JavaScript异步编程]()提到了回调存在下面的问题：

- 调用回调过早（在它开始追踪之前）

- 调用回调过晚 (或不调)

- 调用回调太少或太多次

- 没能向你的回调传递必要的环境/参数

- 吞掉了可能发生的错误/异常

那么Promise是如何解决这些问题呢？

## 调用太早

## 调用太晚

## 根本不调用

## 调太晚或太多次

## 没能传参

## 吞掉错误

# Promise对象

# Promise对象

> Promise对象，用来处理回调地狱的问题，把嵌套调用变成链式调用。同时提供了两种状态方便传递不同的返回值。

Promise对象有三种状态：

- pending（进行中）
- fulfilled（已成功）
- rejected（已失败）

Promise对象特点：

1. 对象的状态不受外界影响，并且只有异步操作的结果可以决定当前是哪一种状态。
2. 一旦状态改变，就不会再变，任何时候都可以得到这个结果。定型时我们称为`resolved`，一般`resolved`指`fulfilled`状态，不包含`rejected`状态
3. 无法取消promise，一旦新建就会立即执行，无法中途取消。
4. 如果没有回调函数，Promise内部的错误，不会反映到外部
5. 当处于pending状态时，无法得知目前进展到哪一个阶段

# Promise. resolve( )

> promise. resolve( )可以将现有的对象转为promise对象
>
> resolve的Promise对象是在本轮“事件循环”的结束时执行，而不是下一轮“事件循环”的开始时

使用方法：

```js
Promise.resolve('foo')
或
new Promise(resolve => resolve('foo'))
```

返回结果：

1. 如果resolve( )的参数是promise实例，则原封不动地返回，并且返回的状态由作为参数的promise对象决定，如果作为参数的`promise对象`状态为pending，则不会触发回调函数，而是等待参数的`promise对象`的状态改变才会触发，简单来说就是一个继承的关系 。
2. 如果参数是一个thenable对象（具有then方法的对象），resolve方法会将这个对象转为Promise对象，然后就立即执行`thenable`对象的then方法
2. 参数如果不是具有`then`方法的对象，或根本不是对象，则Promise.resolve()方法会返回一个新的Promise对象，状态为`resolved`
2. 不带有任何参数时，会返回一个resolved状态的Promise对象

# Promise.reject( )

> `Promise.reject( )`方法也会返回一个新的Promise实例，该实例的状态为`rejected`。

`reject( )`方法的参数，会**原封不动**地作为`reject`的理由，变成后续方法的参数。这一点与`Promise.resolve`不同

比如reject的是一个`thenable`对象，他会返回一个`thenable`对象，then里面的返回值。

```js
let thenable = {
	then(resolve,reject){
		reject('出错了');
	}
}

Promise.reject(thenable).catch(e=>{
	console.log(e == thenable);
})
//true
```

# Promise封装同步函数的问题

当我们用Promise处理同步函数时，会把它变成异步函数，并且会在本轮事件循环的末尾执行，会导致函数执行顺序与我们想要的不同，比如

```js
const f = function () {
	console.log("now");
};
const func = {
	then(resolve, reject) {
		resolve("pre");
	},
};
let p = Promise.resolve(func).then((res) => {
	console.log(res);
	f();
});
console.log("next");

//next
//pre
//now
```

当我们想要保持同步函数的同步状态时，可以使用`async`，或者使用下面的`Promise.try()`

# Promise.try( )

> try提供了一种统一的捕获同步异步错误的方法

```js
Promise.try(()=>func)
    .then(...)
    .catch(...)
```

# Promise.prototype.then( )

> then方法是Promise的核心，它是一个定义在原型对象`Promise.prototype`上的方法。
>
> then方法的作用是为Promise实例添加**状态改变**时的回调函数。

## 参数

- 第一个是`resolved`状态的回调函数
- 第二个是`rejected`状态的回调函数（一般不提倡，建议用catch）

## 返回结果

- 返回结果是一个Promise实例
- 所以可以在`then`方法后面调用另一个`then`方法实现链式调用

# Promise.prototype.catch( )

> 捕获`rejected`状态的Promise

跟传统的`try...catch`相比，Promise对象抛出的错误不会传递到外层代码，即不会有任何反应（比如阻塞程序的运行）

# Promise.prototype.finally( )

> 用于指定不管Promise对象最后状态如何，都会执行的操作
>
> `finally`方法不接受任何参数，所以`finally`无法知道`promise`的状态

代码：

```
promise
.then(res=>{...})
.catch(error=>{...})
.finally(()=>{...})
```

# Promise.all( )

> `Promise.all( )`方法用于将多个Promise实例包装成一个新的Promise实例

```js
const p = Promise.all([p1,p2,p3]);
```



状态判断：

fulfilled：当`p1,p2,p3`都为`fulfilled`时，`p`为`fulfilled`

rejected: 只要`p1,p2,p3`中有一个被`reject`，`p`的状态就变成`rejected`，此时第一个被`reject`的实例的返回值，会传递给`p`的回调函数

# Promise. race( )

> `Promise.race( )`方法也是将多个Promise实例包装成一个新的Promise实例

只要`p1,p2,p3`中有一个实例率先改变状态，`p`的状态就会跟着改变。并且先改变的Promise实例的返回值会传给回调函数。

使用示例：请求超时报错

```js
const p = Promise.race([
    fetch('/resource-that-may-take-a-while'),
    new Promise(function(resolve,reject){
        setTimeout(() => reject(new Error('request timeout')),5000)
    })
])
```

# Promise.allSettled( )

> `Promise.allSettleed( )`方法接受一组Promise实例作为参数，包装成一个新的Promise实例。只有等到所有这些参数实例都返回结果，不管是`fulfilled`还是`rejected`，包装实例才会结束。
>
> 当我们不关心异步操作的结果，只关心这些操作有没有结束。这时`allSettled`就很有用

allSettled返回的实例，状态总是`fulfilled`，并且监听函数接收到的参数是一个数组，对应每一个传入`Promise.allSettled( )`的Promise实例

使用示例：过滤出成功与失败的请求

```js
const promises = [ fetch('index.html'), fetch('http://does-not-exist/') ];
const results = await Promise.allSettled(promises);

const successfulPromises = results.filter( p => p.status === 'fulfilled' );

const errors = results.filter(p => p.status === 'rejected').map(p => p.reason)
```

# Promise. any( )

