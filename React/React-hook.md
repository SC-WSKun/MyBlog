---
title: React hook
date: 2022-03-31 21:17:15
tags: React
categories: React
---

> 参考链接：https://zhuanlan.zhihu.com/p/429308983 
>
> 其实这篇文章看下来感觉一般般，有一些没有写清楚，》》》是我的疑惑

# 什么是React hook

在看React相关的一些博客的时候，总是会看到`hook`有关的内容。那么这个`hook`究竟是什么呢？

首先我们知道，React有两种组件：`函数组件`和`类组件`。

一个类组件内置了很多现成的东西，比如生命周期，我们可以按照提供的规则去写就能得到一个可以使用的组件。

但是往往我们在一个生命周期函数中需要添加很多个逻辑，比如清空定时器、删除变量、缓存处理。**相互关联且需要对照修改的代码被进行了拆分，而完全不相关的代码却在同一个方法中组合在一起。**如此很容易产生 bug，并且导致逻辑不一致。

函数组件一开始被称为"无状态组件"。因为在`hook`出现之前，函数组件无法定义和维护state，所以当时类组件是要强于函数组件的。

而`react hooks`的出现，可以让我们在不编写 class 的情况下使用 state 以及其他的 React 特性，补齐函数相对于类组件而言缺失的功能。

没有太多书写的限制，不强制按照生命周期划分逻辑，不需要理解 this，将复杂组件中相互关联的部分拆分成更小的函数达到复用的目的。

# hook分类

hook一般分为以下几种：

1. 组件状态处理： useState、useReducer、useContext
2. 处理副作用：useEffect、useLayoutEffect
3. 性能优化相关： useMemo、useCallback
4. DOM相关： useRef
5. redux相关：useSelector、useDispatch、useStore

---

## **useState**

在函数组件保存数据的主要方法，等同于类组件的`this.setState`

示例：

```React
function TestComponent(){
	const [count, setCount] = React.useState(0);
    const change = () => setCount(count + 1);
    return (
        <div>{count}
        <button onClick={change}>click</button>
        </div>
    )
}
```

接受初始值，返回一个state，以及更新`state`的函数。



---

## **useEffect**

类组件中通常在生命周期中执行副作用，`useEffect`的作用是补充函数组件无法正确执行副作用的问题。

{% note info %}

对于在React组件中执行**数据获取、订阅**或者**手动修改DOM**的行为，我们把它们称为“副作用”，或者简称“作用”

{% endnote %}

`useEffect`接受两个参数：

- 被监听的参数发生变化时执行的回调函数
- 被监听参数

示例：

```react
function Test() {
  const [count, setCount] = React.useState(0)
  const [text, setText] = React.useState('')
  const change = () => setCount(count + 1)
  React.useEffect(() => {
    fetch("http://127.0.0.1:5504/react-test/index.html").then(async res => {
      let txt = await res.text()
      setText(txt)
    })
  }, [])
  return <div>
    {count}
    <button onClick={change}>click</button>
    {text}
  </div>
}
```

这里我们让`useEffect`监听`[]`，所以这个回调函数只会在初次渲染时执行，相当于：`componentDidMount`

我们可以用`useEffect`来模拟大部分生命周期：

- `componentDidMount`：如上监听一个空数组

- `componentDIdMount` + `componentDidUpdate`：

  - 任意`state`发生变化都会触发，包括初始化：

    ```react
    React.useEffect(() => {
    	//...
    })
    ```

  - 当指定的`state`（n）发生变化才会触发，包括初始化：

    ```react
    React.useEffect(() => {
    	//...
    },[n])
    ```

  - 只有数组中的其中一个发生变化才会触发，包括初始化：

    ```react
    React.useEffect(() => {
    	//...
    },[n,x])
    ```

  - 如果回调返回新的函数，则初始化时不执行返回的函数，state变化触发时先执行返回的函数，再从第一个`useEffect`开始执行：

    ```react
    React.useEffect(() => {
    	//后执行
        return () => {
            //先执行
        }
    },[n])
    ```

    举个例子：

    ```react
    const Test = props => {
      const [n, setN] = React.useState(0)
      const [times, setTimes] = React.useState(0)
      const handleChangeN = () => {
        setN(n + 1)
      }
      //任何state改变都会触发
      React.useEffect(() => {
        console.log('aaaa')
      })
      //只有n改变才会触发
      React.useEffect(() => {
        console.log("bbbb")
      }, [n])
      //只有n改变时触发，先执行返回的函数
      React.useEffect(() => {
        console.log(':::n变化，重启定时器:::');
        let timer = setInterval(() => {
          setTimes((_times) => {
            return _times + 1
          })
        }, 2000);
        return () => {
          console.log(':::n变化，清空定时器, 会在 aaaa 前触发:::');
          clearInterval(timer)
          setTimes(0)
        }
      }, [n])
      return <>
        计次器：{times}
        <div>state：{n}   <button onClick={handleChangeN}>改变n</button></div>
      </>
    }
    ```

    打印结果：

    ```
    //初始化
    aaaa
    bbbb
    :::n变化，重启定时器：：：
    
    //计时器启动期间，times变化
    aaaa
    aaaa
    aaaa
    
    //手动触发按钮，改变n，先执行返回的函数，然后再从第一个useEffect顺序执行
    :::n变化，清空定时器，会在 aaaa 前触发:::
    aaaa
    bbbb
    :::n变化，重启定时器:::
    
    //n的变化结束，只剩times变化
    aaaa
    aaaa
    aaaa
    ```

- 代替`componentDidMount + componentWillUnmount`：

  ```react
  React.useEffect(() => {
  	console.log('初始化的时候会执行，componentDidMount')
      const onResize = (e) => {};
      window.addEventListener('resize', onResize)
      return () => {
          window.removeEventListener('resize', onResize);
          console.log('在组件卸载的时候调用,componentWillUnmount')
      }
  },[])
  ```

  》》》这里应该是卸载的时候进行垃圾回收，导致触发回调函数？

  初始渲染添加监听器，卸载时取消，达到将相关的代码放在一起。
  
  

---

## **useMemo 和 useCallback**

解决函数组件的性能问题，比如子组件重复执行问题，每次渲染都进行高开销的计算：

例如：

```react
function Sub(props){
	console.log("Subrender");
	let {number, onClick} = props;
	return (
        <button onClick = {onClick}>{number}</button>
	)
}

function Test(){
    let [value, setValue] = React.useState('');
    let [number, setNumber] = React.useState(0);
    const addClick = () => setNumber(number + 1)
    return <>
    	<input
            type="text"
            value={value}
            onChange={(e) => setValue(e.target.value)}
        />
    	<Sub number={number} onClick={addClick} />
    </>
}
```

子组件依赖的只有number ，理想情况下只希望number变化时触发子组件重新渲染。但实际是在输入框内的值发生变化，子组件也会重新渲染。如果子组件的逻辑较复杂，就是无意义的大量计算，浪费资源。

在类组件中，我们可以使用`shouleComponentUpdate(nextProps, nextState)`声明周期，在组件更新前，判断当前组件是否受某个`state`或者`prop`更改的影响。

而在函数组件中，我们不再区分`mount`和`update`两个状态，所以函数组件每一次调用都会执行内部所有逻辑，就带来了非常大的性能损耗。

所以我们引入了`useMemo`和`useCallback`

useMemo：memory，记住计算后的值，只有当依赖发生变化，才会重新计算

```react
function Test(){
	let [value, setValue] = React.useState('');
    let [number, setNumber] = React.useState(0);
    const addClick = () => setNumber(number + 1);
    const MemoSub = React.useMemo(
    	() => <Sub data={number} onClick={addClick} />,
        [number] //只有number变化才重新计算MenoSub
    )
    return <>
    	<input
            type="text"
            value={value}
            onChange={(e) => setValue(e.target.value)}
        />
    	{MemoSub}
    </>
}
```

useCallback：与useMemo返回一个值不同的是，useCallback返回的是一个函数。`useCallback(fn, deps)` 相当于 `useMemo(() => fn, deps)` 

```react
const set = new Set();
function Child({fn}) {
  console.log('child');
  const [count, setCount] = React.useState(() => fn()); // count 初始值由 传递参数决定
  React.useEffect(() => {
    console.log('child effect');
    setCount(fn());
  }, [fn]); // fn变化需要更新count
  return <div>
    child count: {count}
  </div>
}

function Test() {
  const [count, setCount] = React.useState(1);
  const [val, setVal] = React.useState('');

  const callback = () => count * 2
  set.add(callback);

 
  const changeCount = () => setCount(count + 1)
  const changeValue = event => setVal(event.target.value)
  return <div>
    <h4>count: {count}</h4>
    <h4>set.size: {set.size}</h4>
    <div>
      <button onClick={changeCount}>+</button>
      <input value={val} onChange={changeValue} />
    </div>
    <hr />
    <Child fn={callback} />,
  </div>;
}

```

点击按钮和输入框修改内容都会导致`set.size`的修改，说明每次`callback`是不同的。

》》》应该是由于val改变了，所以函数组件更新时，重新定义的`callback`与原来不是同一个函数。而我们要做的就是让`callback`保持不变。

》》》这里有个疑问，为什么每次的`set.size`是增加2？

使用`useCallback`进行修改，将`count`作为依赖：

```react
const callback = React.useCallback(() => count,[count]);
```

此时只有点击按钮修改了`count`才会导致`set.size`的修改：

```react
function Child({ callback }) {
  console.log('child');
  const [count, setCount] = React.useState(() => callback());
  React.useEffect(() => {
    console.log('child effect');
    setCount(callback());
  }, [callback]);
  return <div>
    child count: {count}
  </div>
}

function Test() {
  const [count, setCount] = React.useState(1);
  const [val, setVal] = React.useState('');

  const callback = React.useCallback(() => count * 2, [count]); // 使用了 callback 不会重新声明函数

  set.add(callback);
  const MemoChild = React.useMemo(
    () => <Child callback={callback} />,
    []
  )
 
  const changeCount = () => setCount(count + 1)
  const changeValue = event => setVal(event.target.value)
  return <div>
    <h4>count: {count}</h4>
    <h4>set.size: {set.size}</h4>
    <div>
      <button onClick={changeCount}>+</button>
      <input value={val} onChange={changeValue} />
    </div>
    <hr />
    {MemoChild}
  </div>
}
```



---

## **useRef**

类组件中使用`createRef`生成DOM节点的引用，在函数组件中也可以使用，而`useRef`和`createRef`类似，也可以用来保存DOM节点引用：

```react
function Test(){
	/* 保存DOM */
    const inputEl = React.createRef();
    //或
    const inputEl = React.useRef();
    
    const onClick = () => {
        console.log(inputEl);
        inputEl.current.value = 'hahahahahaha'
    }
    
    return <div>
    	<input ref={inputEl} />
        <button onClick={onClick}>click me !!!</button>
    </div>
}
```

效果是一样的。

但是`useRef`有一点不同的是，它可以保存任何值，比如：

```react
function Test() {
  /* 保存变量 */
  const textRef = React.useRef()
  const [text, setText] = React.useState()
  React.useEffect(() => {
    textRef.current = text
    console.log(textRef);
  }, [text])
  
  return <div>
       <input value={text} onChange={ (e) => setText(e.target.value)}/>
  </div>
}
```

上面这段代码，每次修改输入框都会更新`textRef`

`useRef` 和 自建的`{current: xx}` 的区别是，`useRef`每次重新渲染时返回的是同一个对象，而 `{current: xx}` 的对象每次都会伴随组件的更新而变化

---

## **useContext**

优化了函数组件使用`context`的能力，并进行了写法上的统一。

`context`类似`vue provide`的概念，无需将组件传递到每一个组件，跨组件级别、传递变量，实现共享。

类组件：

```react
const ThemeContext = React.createContext('light')
class App extends React.Component {
    render() {
        return (
        	<ThemeContext.Provider value="dark">
            	<Toolbar theme="dark"/>
            </ThemeContext.Provider>
        )
    }
}

function Toolbar(props) {
    return (
    	// 中间的组件再也不必指名往下传递theme了
        //本来应该从props中提取theme，继续向下传递的
        <div>
        	<Button />
        </div>
    )
}

class Button extends React.Component {
    // 指定 contextType 读取当前的 themeContext
    // React 会往上找到最近的 theme Provider，然后使用它的值
    // 在这个例子中，当前的 theme 值为“dark”
    static contextType = ThemeContext;
    render(){
        return <button>{this.context}</button>
    }
}
```

函数组件：

```react
const ThemeContext = React.createContext('light')
function Test() {
  return (
    <ThemeContext.Provider value="dark">
      <Toolbar theme="dark" />
    </ThemeContext.Provider>
  )
}

function Toolbar(props) {
  return (
    // 中间的组件再也不必指明往下传递 theme 了。
    <div>
      <Button />
    </div>
  )
}

function Button() {
  // 使用 useContext 读取当前的 themecontext。
  // React 会往上找到最近的 ThemeContext.Provider，然后使用它的值。
  // 在这个例子中，当前的 theme 值为 “dark”。
  const theme = React.useContext(ThemeContext)
  return <button>{ theme }</button>
}
```

函数组件也可以用Consumer

Provider和Consumer对应

》》》这里是用了一个箭头函数，猜测是theme作为参数从context传入。前面类组件例子中的`this.context`只能在类组件使用？因为函数组件好像没有this这个说法。

```react
function Button() {
	return <ThemeContext.Consumer>
    	{
            theme => <button>{theme}</button>
        }
    </ThemeContext.Consumer>
}
```

# Hook不能使用的情况

- 不能在循环、条件或者嵌套函数中使用`hook`
- 不能在类组件使用`hook`

# Hook原理

》》》这里用的是知乎这篇文章的简化版，但是其实文章里的有一些bug，注释也怪怪的，感觉还是得自己找源码看看

## 模拟useState

```react
const state = []
let index = 0
// 按index顺序存储
function useState(init) {
  let curIndex = index
  state[curIndex] = state[curIndex] || init //默认参数的写法
  function setState(newDate) {
    state[curIndex] = newDate
    update()
  }
  index++
  return [state[curIndex], setState]
}


function update() {
  //众所周知setState会调用render
  //但是多个setState会合并，猜测这里应该有所简化
  render()
}
```

》》》文章说这种实现会导致每次获取只能拿到 index 为0的值，修改的话也是修改这个值，暂时还看不太懂。例如下面的代码：

```react
function Test() {
  if (Math.random() > 0.5) {
    let [number, setNumber] = useState(0)
    const onClickNumber = () => setNumber(number + 1)
    return <div>
      {number} <button onClick={onClickNumber}>number</button>
    </div>
  }
  
  let [name, setName] = useState('q')
  const onClickName = () => setName(name + 'w')
  return (
    <div>
      {name} <button onClick={onClickName}>name</button>
    </div>
  )
}
```



真正的源码（暂且搁置下，好好学学TS。。）：

```js
export function useState<S>(
  initialState: (() => S) | S,
): [S, Dispatch<BasicStateAction<S>>] {
  // debugger
  const dispatcher = resolveDispatcher(); // 
  return dispatcher.useState(initialState);
}

function resolveDispatcher() {
  const dispatcher = ReactCurrentDispatcher.current; // 
  if (__DEV__) {
    if (dispatcher === null) {
      console.error(
        'Invalid hook call. Hooks can only be called inside of the body of a function component. This could happen for' +
          ' one of the following reasons:\n' +
          '1. You might have mismatching versions of React and the renderer (such as React DOM)\n' +
          '2. You might be breaking the Rules of Hooks\n' +
          '3. You might have more than one copy of React in the same app\n' +
          'See https://reactjs.org/link/invalid-hook-call for tips about how to debug and fix this problem.',
      );
    }
  }
  // Will result in a null access error if accessed outside render phase. We
  // intentionally don't throw our own error because this is in a hot path.
  // Also helps ensure this is inlined.
  return ((dispatcher: any): Dispatcher);
}

// 当前使用的 dispatcher
const ReactCurrentDispatcher = {
  /**
   * @internal
   * @type {ReactComponent}
   */
  current: (null: null | Dispatcher), // 
};

```

## 模拟useEffect

```react
let alldeps = []
let unMountCbs = []
function useEffect(cb, arr) {
  if (!alldeps[index]) { // 1、空，说明是初始化，一定会执行
    let unMountCb = cb() // 保存组件销毁回调
    unMountCb && !unMountCbs.includes(unMountCb) && unMountCbs.push(unMountCb)
    alldeps[index] = arr
    index++
    return
  }
  //非空，说明之前已经初始化过了
  let originArr = alldeps[index]
  let hasChange =  arr.some((item, i) => originArr[i] !== item) // some函数判断是否有元素符合条件，看是否有变化
  if (hasChange) {
    cb()
    alldeps[index] = arr
  }
  index++
}
```

》》》这段代码有个不理解的地方在于`unMountCbs`有什么用处，除了初始化的时候调用过，后面似乎没有用到？

---

从上面两个原理中可以看出源码的依赖是顺序存储的，所以不能在条件中使用hook。
