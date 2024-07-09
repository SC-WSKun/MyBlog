---
title: JS的事件机制
date: 2022-02-27 09:47:34
tags: JavaScript
categories: JavaScript
---

> 参考链接：https://zhuanlan.zhihu.com/p/73091706

# 什么是事件

事件就是用户交互触发，或者浏览器自身执行的某种动作

# 事件的分类

## DOM 0级事件（现在不建议使用）

- on-event

  ```
  <input onclick="..."/> 		//html属性
  或者
  window.onload = function(){ //非html属性
  	...
  }
  或者
  var btn = document.getElementById('btn'); //实体元素
  btn.onclick = function(){
  	...
  }
  ```

- 同一个元素同一个事件只能绑定一个函数

- 解绑事件时，只需要让它等于`null`

- 不存在兼容性问题

## DOM 2级事件

- 通过`addEventListener`绑定的事件
- 同一个元素的同种事件可以绑定多个函数，按照绑定顺序执行
- 解绑事件时，使用`removeEventListener`，注意删除的函数跟添加的函数必须是同一个实体

## DOM 3级事件

DOM3级事件在DOM2级事件的基础上添加了更多的事件类型：

- UI事件，用户与页面交互时触发：load、scroll
- 焦点事件，当元素获得或失去焦点时触发：blur、focus
- 鼠标事件，当用户通过鼠标在页面执行操作时触发：dblclick、mouseup
- 滚轮事件，当使用鼠标滚轮或类似设备时触发，如：mousewheel
- 文本事件，当在文档中输入文本时触发，如：textinput
- 键盘事件，当用户通过键盘在页面上执行操作时触发，如：keydown、keypress
- 合成事件，当为IME（输入法编辑器）输入字符时触发，如：compositionstart
- 变动事件，当底层DOM结构发生变化时触发，如：DOMsubtreeModified
- 自定义事件

# 事件流

既然有了事件，那就有事件触发的顺序，也就是事件流

**事件流有两种机制：事件捕获、事件冒泡**

当一个事件发生时，会在子元素和父元素之间传播。传播有三个阶段：

- 捕获阶段：事件从window对象自上而下向目标结点传播的阶段
- 目标阶段：真正的目标结点正在处理事件的阶段
- 冒泡阶段：事件从目标结点自下而上向window对象传播的阶段

## 事件捕获

> 从启动事件的元素节点开始，逐层向下传递

![img](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/v2-d7bc856e4fa608b85659e2512d3f25ec_720w.jpg)

## 事件冒泡

> 事件冒泡和事件捕获相反是逐层向上触发的，首先在触发元素寻找是否有相应的注册事件，如果没有再继续向上级父元素寻找是否有相应的注册事件

![img](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/v2-d397d5bffe5e4ab6edb2539bd17af167_720w.jpg)

## 事件运行机制

事件捕获和事件冒泡是同时存在的，触发的顺序是，先从上到下进行事件捕获，再从下到上执行事件冒泡。

![img](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/v2-4de189d2a42b1e8c74b379e067b67578_720w.jpg)

# 事件监听（addEventListener）

`addEventListener()`有三个参数，对应[事件名称]、[触发执行的函数]、[布尔值：捕获(true)还是冒泡(false)]

```js
window.addEventListener('mousedown',function(){
	alert('hi')
},false)//添加鼠标按下时触发的事件
window.removeEventListener('mousedown',function(){
	alert('hi')
},false)
//注意上面这种做法是没办法删除监听的，因为两个function不是指向堆中的同一个实体
var mouseDown = function(){
    alert('hi')
}
window.addEventListener('mousedown',mouseDown,false)
window.removeEventListener('mousedown',mouseDown,false)
//这才是正确写法
```

# 事件代理

上面我们提到了事件的捕获和冒泡机制，这就引出了一个问题。当我们写一个列表，使用for循环添加了100个`li`，此时如果我们对每一个`li`都添加一个事件，那么就会导致for循环做了100次dom操作。

而我们知道，DOM操作是非常耗费性能的，我们应该尽可能减少直接对DOM操作的次数。所以这就需要用到事件代理。

> 事件代理是利用事件冒泡机制实现的，将事件绑在父元素上，这样子元素找不到对应的触发事件就会向上查找父元素的事件，实现相同的效果

---

示例：

index.html

```html
<ul id="ul1">
	<li>1</li>
	<li>2</li>
	<li>3</li>
	<li>4</li>
</ul>
```

index.js

```js
window.onload = function(){
	var Ul = document.getElementById("ul1");
	Ul.onclick = function(){
		alert("123")
	}
}
```

这样我们就只需要进行一次DOM操作，而且当我们点击li的时候，就会向上冒泡，找到`ul`上的`onclick`触发。

甚至当我们没点击`li`，点击`ul`中的其他地方它也会触发，但是这显然不是我们想要的。所以这时候我们需要`target`来限定触发条件来源

```js
Ul.onclick = function(e){
	e = e||window.event;
	var src = e.target;//获取target
	if(src&&src.nodeName.toLowerCase()==='li'){
		alert(src.innerHtml)
	}
}
```

