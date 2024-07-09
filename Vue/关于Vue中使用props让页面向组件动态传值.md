---
title: 关于Vue中使用props让页面向组件动态传值
date: 2021-07-16 01:22:24
tags: Vue
categories: Vue
---
在做项目的时候，由于商品列表页面使用了子组件。由于子组件不能使用onLoad(),所以只能在page里使用云函数返回需要的数值再传到子组件
此时可以使用props进行传参，用v-bind单向传值示例代码为
## 父组件：
```
<com v-bind:prop-data=car></com>
<script>
    export default{
        data{
            car: []
        }
    }
</script>
```
## 子组件：
```
<script>
    export default{
        props:['propData']
    }
</script>
```
使用this.propData可以拿到页面传来的参数

应该也可以用v-model双向绑定