---
title: React组件
date: 2021-08-03 14:03:01
tags: React
category: React
---

# React 组件

React的一大特色就是制作组件组成的单页应用，所以组件是React的一个非常重要的概念。

# 类组件

使用 class 定义，必须继承 React. Component

```react
    class MyComponent extern React.Component{
      // 必须要有render函数,必须要有返回值，可以不写构造器
        render(){
            return <h1>我是类组件</h1>
        }
    }
```

## 类组件三大属性

### State

state 有点像 C++里面类的成员变量的集合，可以存储这个组件的所有变量，而且有一些特殊的调用方法的封装，比如 setstate( )

使用示例：

```react
constructor(props){
  super(props);
  this.state = {
    ishot: false,
    iswind: false
  }
}
```

### Props

Props 用于组件之间的传参

基本使用：

在标签中传入参数
```react
<Mycomponent name="kunkun"><Mycomponent/>
//此时会在这个dom元素的props中加上 name:"kunkun"，这个键值对
//在Mycomponent组件中可以用this.props.name来获取"kunkun"
```

批量传入：

使用扩展运算符批量传入参数
```react
const p = { name: "kunkun", sex: "man" };
<Mycomponent {...p}><Mycomponent/>
```

### Ref
用于绑定结点，对结点进行操作

ref有四种方式定义：

- 字符串形式的ref
- 内联函数形式的ref
- 外联函数形式的ref
- 使用CreateRef函数定义容器

# 函数式组件

使用 function 定义，组件名首字母必须大写，小写开头会被浏览器解析为 html 自带的标签而不会去查找自定义标签

```react
function MyCompontent(){
	return <h1>我是函数式组件</h1>
}
```

函数式组件一开始又被称为无状态组件，因为函数组件一开始是没有像类的state这样的机制来进行维护的，也没有类组件的生命周期对渲染过程进行控制的方法，因此每次改动都会重新渲染。所以一开始函数式组件只是用于渲染一些简单的小组件，主流还是使用类组件。

但是Hook的出现改变了这个情况，它让函数式组件也拥有了类组件的流程控制，而且现在更推荐使用函数式组件。Hook成功地解决了类组件中生命周期函数导致的高耦合现象。

## 函数式组件如何复现类组件

**State：**

函数式组件使用`useState()`实现类组件的维护`state`，`useState()`返回一个长度为2的数组，第一个元素相当于`state`里面的变量，第二个相当于专属于这个变量的`setState`；`useState()`可以传入一个参数，这个参数表示`state`变量的默认值。

使用示例：

```REACT
function TestModule(){
    const [state, setState] = React.useState(0);
}
```

---

**Props:**

直接使用函数传参即可

```react
function TestModule(props){
	console.log(props)
}
```

---

**Ref：**

函数式组件使用`useRef()`或`forwardRef()`实现类组件中的`ref`

`useRef()`返回一个可变的`ref`对象，其`current`属性被初始化为传入的参数（也就是我们可以用`current`访问`ReactDOM`）。返回的`ref`对象在整个生命周期内保持不变。

使用示例：

```react
function TestModule(){
	const ref1 = React.useRef(null);
    return (
    	<input ref={ref1} type="text" />
    )
}
```

---

**各种生命周期：**

