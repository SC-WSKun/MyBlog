---
title: ES6箭头函数
date: 2021-08-02 15:58:24
tags: [JavaScript, ES6]
category: JavaScript
---

# 箭头函数（=>）

箭头函数是 ES6 引入的一种新的函数写法，与原来的 function 声明主要在 this 上有所区别

## 箭头函数特点

1. this 是静态的，this 始终指向函数声明时所在作用域下的 this 值

   ```
   function getName(){
       console.log(this.name);
   }

   let getName2 = ()=>{
       console.log(this.name);
   }
   window.name = "kunkun"
   const school {
       name: "cxk"
   }

   getName(); //kunkun
   getName2(); //kunkun

   getName.call(school); //cxk
   getName2.call(school); //kunkun
   ```

2. 不能作为构造函数使用
   ```
   let Person = (name,age){
       this.name = name;
       this.age = age;
   }
   let me = new Person("kunkun",21);
   console.log(me); //false
   ```
3. 不能使用arguments变量
   ```
   let fn=()=>{
       console.log(arguments);
   }
   fn(1,2,3) //arguments is not defined
   ```
## 箭头函数简写