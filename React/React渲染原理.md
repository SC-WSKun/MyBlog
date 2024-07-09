---
title: React渲染原理
date: 2021-11-18 19:08:49
tags: React
categories: React
---

> 参考链接：
>
> React官方文档：https://react.docschina.org/docs/rendering-elements.html
>
> 深入理解React Fiber：https://zhuanlan.zhihu.com/p/98295862

# 前置知识



## 根DOM节点

根节点内的所有内容都将由React DOM管理，根节点的代码如下：

~~~
<div id="root"></div>
~~~

仅由React构建的应用一般只有一个根DOM节点。如果将React集成进一个已有应用中，则可以有任意多的独立根节点。



## React元素

先来看看官方的解释：

> 元素是构成React应用的最小砖块，元素描述了你在屏幕上想看到的内容，与浏览器的DOM元素不同，React元素是创建开销极小的普通对象。React DOM会负责更新DOM来与React元素保持一致。

从上面的解释可以看出，元素其实是一种描述了DOM的JS对象。它的优点是创建开销极小。

> React元素分为两类:
>
> 1. DOM元素：当元素的类型是字符串的时候，比如`<button>submit</button>`
> 2. 组件元素：当元素类型是一个类或者函数的时候，比如`<Button>submit</Button>`，特征是首字母大写

在了解React和Vue为什么能高效渲染的时候，我们可能会接触到这么一个概念，叫虚拟DOM。

虚拟DOM顾名思义，就是一个虚拟的DOM节点，在React中对应的就是React元素，描述了实际DOM应该被渲染的状态。

至于为什么要用<a href="#vdom">虚拟DOM</a>，我们下面会提到。



## React.createElement（ ）

React是通过React.createElement来初始化虚拟DOM结点的。

React有两种创建虚拟DOM的方法：

1. 使用React.createElement()方法

   ```react
   React.createElement(标签名，标签属性/props，标签内容)
   ```

2. [使用JSX](http://112.74.85.250:39/2021/11/26/JSX/)

   

# 栈调和



## 什么叫调和？

官方文档中有这么一句话：

> React DOM 会将元素和它的子元素与它们之前的状态进行比较，并只会进行必要的更新来使 DOM 达到预期的状态。

简单来说，进行最小的更新就叫做调和。



## 如何实现栈调和？

首先我们来了解下React是如何解析一个组件的，比如<App/>组件如下：

```html
<Com1>
	<Btn>
		login
	</Btn>
</Com1>
```

首先React会解析Com1基于props，渲染成什么，比如对于Com1：

```react
const Com1 = (props) => {
	return (
		<div className="component1">
            {props.name}
		</div>
	)
}
```

React会调用**render( )**知道它会渲染什么元素，并最终会将其替换成带有子节点的`<div>`，React会重复这个过程直到明确每个潜在的DOM元素。

**然后当组件state或props更改的时候，React会比较新旧元素来决定是否需要更新实际的DOM，不相等的时候，React会找出最小变化集合（这里用到了<a href="#diff">diff算法</a>），然后更新实际DOM**

那为什么我们称之为栈调和呢？因为React这个解析组件的过程是一个递归调用，而递归就是利用栈来递归的。但是这个也给我们带来了新的问题。



## 栈调和存在的问题

由上述对栈调和的原理介绍可知

每次数据更新的时候都要遍历整个DOM树，而且直到DOM树遍历完成之前，都不会进行更新，这对于那些动画效果就非常不友好，会出现明显的卡顿现象。 

所以在Andrew Clack's 的笔记中，我们可以看到他觉得调和算法需要新的特性：

>- 为不同类型的任务赋予优先级
>- 任务的暂停与恢复
>- 如果任务不再需要，可以中止
>- 复用之前已完成的工作

因此React团队开发了新的调和算法：**Fiber**



# Fiber

在Andrew Clack's 的笔记中提到：

> *Fiber 是专门为 React 组件，栈的重新实现。你可以把单个 fiber 看做一个虚拟的栈帧。*
> *重新实现栈的优势是你可以在内存中追踪栈帧，然后以你期望的时间或者方式来执行他们。*
> *对于实现目标调度是重要的。*
> *除了调度以外，手动处理栈帧解锁并发和错误边界（error bundaries）等潜在的特性。在未来我们会涵盖这一部分。”*

所以Fiber就代表一个携带虚拟栈帧的任务单元，简单来说就是一个Fiber代表一个组件。

## Fiber的”双向“链表

既然FIber节点是用来替换原来的React元素的，那么他自然代表了React组件的一个实例，一个fiber节点由下面成员组成：

- Type：返回组件的类型，对于自定义组件，type是函数或者类组件本身；对于标准组件，则返回string

- Key：一个唯一标识用来标记节点

- Child：代表render后面返回的第一个**子元素**，比如在Name组件中：

  ```
  const Name = (props) => {
    return(
      <div className="name">
        {props.name}
      </div>
    )
  }
  ```

  return的div就是child

- Sibling：代表兄弟节点，比如在Name组件中：

  ```
  const Name = (props) => {
    return([<Customdiv1 />, <Customdiv2 />])
  }
  ```

  上面的<Customdiv1> 和 <Customdiv2> 是 <Name> 的孩子，两个孩子形成一个单链表。<Customdiv1>的Sibling指向了<Customdiv2>

- Return：代表父fiber节点，所以Fiber的父节点通过child指向子节点，子节点通过return指向父节点，有点像是个双向链表。为什么说像呢？因为Fiber节点的兄弟节点并不是双向的，兄弟节点的return都是指向同一个父节点，所以他的遍历其实是一个三角形的结构，这个在下面的例子中可以体现。

  > https://www.youtube.com/watch?v=ZCuYPiUIONs中的例子

  ![image-20211217213302703](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211217213302703.png)

  ![image-20211217213242840](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211217213242840.png)

- pendingprops/memorizedProps：

  > props是传入组件的参数，pendingProps是执行开始的时候设置，memoizedProps是结束的时候设置，当两者相同的时候，fiber可以重新使用之前的fiber，减少重复工作

- **pendingWorkPriority**：*待处理的工作优先级*，Fiber实现优先级调度的核心，数字越大表示优先级越低

- Alternate：

  > 任何时候一个组件实例最多两个对应的fiber节点：flushed fiber和work-in-progress fiber

  - flush：flushed fiber是当前输出渲染到屏幕上的fiber
  - work-in-progress：未完成的fiber，还在堆栈中，可以理解为flushed的一个备胎

- Output：返回的是最后提供给渲染器的输出，以便输出到具体渲染环境

  - Host Component

    > 这个是虚拟DOM跟真实DOM转换的关键点
    >
    > 作用：更新Dom结点

    Host Component在第一次渲染时会调用：

    1. createInstance：创建DOM实例
    2. appendAllChildren：插入子节点
    3. 执行`finalizeInitialChildren()`，初始化事件监听，并且判断该节点如果有`autoFocus`属性并为`true`时，执行`markUpdate()`，添加`EffectTag`，方便在`commit`阶段`update`
    4. 最后将创建并初始化好的 DOM 对象绑定到`fiber`对象的`stateNode`属性上

# Fiber的Setstate做了什么

## Render阶段（可被打断，实现了优先级调度）

- componentWillMount
- componentWillReceiveProps
- shouldComponentUpdate
- ComponentWillUpdate

## 深度优先遍历

从HostRoot开始，React通过Child属性快速到达叶子结点，然后检查是否有兄弟节点（Sibling），如果有兄弟节点，则对该兄弟节点开始继续深度优先的渲染，直到回退到根节点结束

## Work Loop（任务循环）

> 每个任务循环处理一个Fiber Node

- shouldYield( )：这个函数会询问主线程是否有优先级更高的任务存在，是则跳出循环，否则继续渲染
- 优先级：
  - Synchronous： same as stack rec.
  - Task：before next tick
  - Animation：before next frame
  - High：pretty soon
  - Low：minor delays ok
  - Offscreen：prep for display/scroll


react实现异步构建树的机制，React会向主线程请求分给构建Fiber树一段时间片（不是固定长度），然后React会在这个时间片内从current根节点开始，逐步clone到work-in-progress，

![image-20211218002255600](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211218002255600.png)

## Commit阶段（不可被打断）

- componentDidMount
- componentDidUpdate
- componentWillUnmount

在Commit阶段，我们要进行交换当前树的根指针和workInProgress树的根指针，从而高效地更新Fiber树

**在Commit阶段有一个帧时间，React为每一个执行的任务单元启动一个内部计时器，当执行任务的时间超过16ms，就会暂停当前执行的工作单元，回到主线程，给浏览器做一些渲染任务，然后在下一帧的时候重回离开的地方继续构件树。这种思想有点类似于操作系统的分片思想。**

## 使用Fiber的好处

在之前的调和算法中，我们创建的是React元素树，注意**React元素是不可变对象**，这意味着我们无法直接改变节点，需要重新创建节点才能进行更新。

在上面的Fiber的属性介绍中，我们可以看到Fiber用Child和Sibling构成了链表组，相比与原来的栈调和，我们可以使用深度优先遍历（，这样效率上无疑从O(n)优化成了O(logn)

<div id="diff"></div>

# diff算法

> diff算法是用来求出新旧DOM树的最小变化集合的，diff算法越高效，渲染效率越好。
>
> diff算法分为三类，分别是：
>
> 1. Tree diff
> 2. Component diff
> 3. Element diff



## Tree diff



<div id="vdom"></div>

# 虚拟DOM

对于传统WEB应用而言，任何数据的变化，都会导致DOM树的重新渲染，即：我在一次函数调用中刷新了多次props，那真实DOM树就会刷新多次。

![image-20211202155332577](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211202155332577.png)

而真实DOM频繁地排版跟重绘效率非常低，想象一下，如果只是修改一小部分的节点，就要导致全部DOM节点的重绘，而且还重复多次，这效率想想就很低。

所以虚拟DOM应运而生，虚拟DOM最大的特点就是**把频繁的更改累加起来，一次性进行重绘**，这就大大提高了执行的效率。相当于我们给程序跟真实DOM中间加了一个中间层，能帮我们缓冲数据的更新，处理后一次性交给真实DOM渲染。

![image-20211202163113434](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211202163113434.png)



### 关于SetState的异步

setstate在React的合成事件（例如：onClick( )）和生命周期（钩子函数）中会进行合并，在原生事件和计时器中不会合并。

即：

**setstate在合成事件与钩子函数中是异步的，在原生事件与定时器中是同步的**

其实从这里可以看出，同异步与setstate本身（代码）是没有关系的。setstate本身执行的过程和代码都是同步的。区别在于合成事件和钩子函数的调用顺序在更新之前，所以合成事件和钩子函数没法马上拿到更新后的值，形成了异步，具体可以看下[React合成事件]().
