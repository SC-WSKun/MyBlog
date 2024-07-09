---
title: JS打乱数组顺序
date: 2022-03-15 22:38:36
tags: JavaScript
categories: JavaScript
---

> 参考链接：https://github.com/mqyqingfeng/Blog/blob/master/articles/%E4%B8%93%E9%A2%98%E7%B3%BB%E5%88%97%E6%96%87%E7%AB%A0/JavaScript%E4%B8%93%E9%A2%98%E4%B9%8B%E4%B9%B1%E5%BA%8F.md

# 乱序

在实际业务中，可能需要打乱数组提高数据的曝光度。或者有其他业务情况，此时可以采用下面的写法

# Math.random()

`Math.random`是随机算法中常见的函数，我们可以用它来随机sort的条件：

```js
let arr = [1,2,3,4,5];
arr.sort(function(){
    return Math.random() - 0.5;
})
```

看似合理，正数和负数的几率是相等的，但是其实效果并不如人意。

## sort函数的原理

以`JS v8`为例，`v8`在处理`sort`方法时，对数组长度小于10的数组采用插入排序，对大于等于10的采用快速排序和插入排序的混合排序。

 插入排序的源码是：

```js
function InsertionSort(a, from, to) {
    for (var i = from + 1; i < to; i++) {
        var element = a[i];
        for (var j = i - 1; j >= from; j--) {
            var tmp = a[j];
            var order = comparefn(tmp, element);
            if (order > 0) {
                a[j + 1] = tmp;
            } else {
                break;//这里break了，没有继续跟前面的元素比较
            }
        }
        a[j + 1] = element;
    }
};
```

可以发现，插入排序算法中，待排序元素跟有序元素进行比较时，一旦确定位置就会`break`而不会跟前面的元素进行比较，所以乱序并不彻底

# Fisher-Yates算法

原理就是从数组中随机抽取一个元素，与最后一个未确定元素进行交换。

```js
function shuffle(a) {
    var j, x, i;
    for (i = a.length; i; i--) {
        j = Math.floor(Math.random() * i);
        //交换
        x = a[i - 1];
        a[i - 1] = a[j];
        a[j] = x;
    }
    return a;
}
```

使用ES6的解构进行简化：

```js
function shuffle(a) {
    for (let i = a.length; i; i--) {
        let j = Math.floor(Math.random() * i);
        [a[i - 1], a[j]] = [a[j], a[i - 1]];
    }
    return a;
}
```

