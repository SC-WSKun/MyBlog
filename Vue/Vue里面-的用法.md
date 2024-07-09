---
title: Vue里面$set()的用法
date: 2021-10-29 09:39:19
tags: Vue  
category: Vue
---

# 问题描述

先来看一个示例

```
<template>
     <div>
    <p @click="add(obj)">{{obj.temp1}}</p>
    <p @click="add(obj)"> {{obj.temp2}}</p>
</div>
</template>

 <script>
  export default {
      data(){
            return {
                obj:{}
            }
      },
      mounted() {
        this.obj = {temp1: 0};
        this.obj.temp2 = 0;
        console.log('after--', this.obj);
      },
     methods: {
        addd(item) {
            item.temp1 = item.temp1 + 1;
            console.log('item--',item);
        },
        adde(item) {
            item.temp2 = item.temp2 + 1;
            console.log('item--',item);
        }
       }
  }
 </scirpt>  
```

运行后可以发现，temp1会增加，而temp2**没变**

原因是：

Vue无法监听对象属性的添加或删除，只会对初始化时拥有的属性进行监听，即只有初始化时就已经存在的属性有getter和setter，而后续新增的属性只有当再次渲染时，属性才会更改。

所以为了让新增的属性的修改也能监听到属性的更改，Vue提供了$set()方法

# $set()的用法

使用set新增的属性也能有getter跟setter：

```
this.$set(this.obj,'temp2',0)
```

# 小扩展

使用Object.assign( )也可以实现新增可监听的属性：

```
Object.assign(obj,{temp2: 0})
```

Object.assign( )是将第二个参数的对象合并到第一个参数的对象中，可以发现新增的temp2也可以被监听到改变。原因应该是对于整个obj对象也有一个监听，我们将第二个参数合并到obj时，相当于对obj进行赋值，vue能监听到obj的变化。

那既然知道原理，我们不妨再试试：

```
obj={...obj,temp2:0}
```

发现temp2也可以被监听，所以如果我们新建一个对象替换掉原来的对象，那新增的属性也可以实现被监听。
