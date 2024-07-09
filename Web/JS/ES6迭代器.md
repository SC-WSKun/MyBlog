---
title: ES6迭代器
date: 2021-08-04 09:48:35
tags: [ES6, JavaScript]
category: JavaScript
---

# ES6 迭代器

迭代器使 JS 中的不同数据结构都能使用遍历算法 for...of 进行遍历。在 JavaScript 中的迭代器是对象中的一个属性，可以通过自定义 iterator 这个属性来自定义 for...of 的具体实现

# 迭代器原理

1. 创建一个指针对象，指向当前数据结构的起始位置
2. 第一次调用对象的 next 方法，指针自动指向数据结构的第一个成员
3. 接下来不断调用 next 方法，指针一直向后移动，并每次调用都返回一个对象{value: "", done: true | false}
4. 对象中的 value 属性是调用该次 next 方法得到的值，done 表示此次遍历是否完成

```
//声明一个数组
const vtuber = ['nano','mana','milei','serena'];
//打印iterator
const iterator = vtuber[symbol.iterator]();
console.log(iterator);
console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());
```

# 自定义迭代器

通过自定义对象中的 iterator 属性实现 for...of 的重写

```
const DD = {
    name: "kunkun",
    dantui: [
        'nano',
        'mana',
        'milei',
        'serena'
    ],
    //自定义迭代器属性
    [symbol.iterator](){
        //索引变量
        let index = 0;
        return {
            //next方法
            next: () => {
                if(index < this.dantui.length){
                    index++;
                    return {
                        value: this.dantui[i],
                        done: false
                    };
                }
                else{
                    return {
                        value: undefined,
                        done: true
                    };
                }
            }
        };
    }
}
for(let v of DD){
    console.log(v);
}
```
