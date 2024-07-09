---
title: React的key
date: 2022-03-19 09:45:21
tags: React
categories: React
---

> 参考链接：https://react.docschina.org/docs/lists-and-keys.html

# 为什么要使用Key

在React的diff算法中，`element diff`有一个优化是通过唯一标识来识别新旧集合是否存在相同的`element`，而`key`值就是用来作为唯一标识符的。使用`key`可以优化页面的性能。

# Key的使用

一般会使用到`key`的场景就是列表，当我们使用一个函数组件来渲染列表的时候，我们可以在上面附带`key`，

例如：

```React
function listItem(props){
	return <li>{props.value}</li>
}

function NumberList(props){
    const numbers = props.numbers;
    const listItems = numbers.map((number)=>{
        <ListItem key = {number.toString()} value={number}/>
    })
    return (
    	<ul>
        	{listItems}
        </ul>
    )
}
```

# 注意点

- 添加`key`的时候要在组件`<ListItem>`上添加，而不是在`<li>`上添加，在`<li>`上添加无法起到`key`的效果

- `key`只是在兄弟节点间必须唯一，可以理解为只需要在一个列表同一级别唯一
- `key`没有办法通过`props.key`读取，所以传值时避开`key`的命名
