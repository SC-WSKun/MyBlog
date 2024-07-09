---
title: JS闭包
date: 2021-12-07 15:50:35
tags: JavaScript
categories: JavaScript
---

# JS变量作用域

JS函数内部可以读取外部变量，外部不能读取函数内的局部变量，这个跟JS的词法作用域有关：

```js
var	n1 = 999;
function func(){
	alert(n1); //999
}


function func(){
	var n2 = 999; //局部变量
}
alert(n2); //error
```

更多作用域有关内容，可以看我的另外一篇博客：[JS的Scope]()

# 闭包是什么？

上面说到正常是无法读取函数内的局部变量的，但是我们可以在func函数内定义一个新的函数，将他作为返回值，就可以在func外部获取内部变量了

示例代码：

```js
function func(){
	var N = 999; //局部变量
    function returnN(){
        return N;
    }
    return returnN;
}

var func2 = func();
console.log(func2()) //999
```

阮一峰老师的说法：

>***闭包就是能够读取其他函数内部变量的函数。***

《You Don't Know JS》中的说法：

> ***闭包就是函数能够记住并访问它的词法作用域，即使当这个函数在它的词法作用域之外执行时。***

那么我们也可以类比下C++的类，是不是闭包有点像下面的代码：

```c++
    
class Test{
    private:
    	int n;
    public:
        Test(int a){
            n = a;	
        }
        int returnN (){
            return n;
        }
}

Test obj(1);
obj.n //error
obj.returnN(); //1

```

对于C++类的私有成员，我们在类外也是无法直接访问的，但是我们可以通过一个公有成员函数去获取他的值。

再通俗一点的理解，闭包生成了一个函数对象，这个函数对象保存了当时函数所处环境的镜像（注意是镜像，而不是引用），我们可以通过调用这个函数对象，得到那个镜像的值。

# 让变量能始终保存在内存中

使用阮一峰老师的例子：

```js


function f1(){
    var n=999;
    nAdd=function(){n+=1}
    function f2(){
        alert(n);
    }
    return f2;
}
var result=f1();

result(); // 999
nAdd();
result(); // 1000

```

这里可以看到n里面的值并没有被释放，说明n一直被保存在内存中，如果我们不是返回函数，而是返回n

```js
function f1() {
    var n = 999;
    nAdd = function () {
        n += 1;
    };
    return n;
}
var result = f1();

console.log(result); // 999
nAdd();
console.log(result); // 999
```

没有使用闭包的话，n是不会被保留的

原理是：

> 正常来说，函数执行结束后，作用域链会被销毁，活动对象也会被释放。
>
> 但是因为闭包返回的函数引用了外部函数的变量对象，所以外部函数的作用域链虽然被销毁了，但是变量对象被保留了下来。
>
> 换一种说法，因为外部函数和返回的函数的作用域链同时指向了外部函数的变量对象，所以当外部函数的作用域链销毁（指针变为`null`）时，外部函数的变量对象仍然被返回的函数的作用域链指向，因此不会被垃圾回收机制回收

# 用闭包创建多个对象

**那当我用闭包创建两个对象呢？**

前面我们讲到，闭包返回的是一个函数并且保存了当时函数所处环境镜像（快照），当我们创建两个时，会出现有两个重名的变量储存在内存中，那是否这个变量会被共用或者会被覆盖？我们来看下面的代码，可以思考下输出是什么，注意这里的add是一个全局变量：

```js
   
    function func() {
        var n = 999; //局部变量
        add = function () {
        	n += 1;
        };
        function returnN() {
        	console.log(n);
        }
        return returnN;
    }


    let result1 = func();
    result1();//999
    add();
    result1();//1000
	//上面的输出应该没有异议，下面的代码就体现了闭包快照的特性

    let result2 = func();
    result2();//999
    add();
    result2();//1000
    add();
    result2();//1001
	//这里可以理解出两个对象是独立的变量空间，应该也很好理解，重点在下面

    add();
    result1();//1000? 1002?
    add();
    result1();//1000? 1001? 1003?
	result2();//1001? 1002? 1003?

```

答案揭晓：

```
1000
1000
1003
```

注意add是一个**全局变量**，在定义`let result2 = func()`的时候，我们已经把add的n绑定为result2中的n了，所以每次调用add修改的时候，修改的都是result2中的n

那怎么让这个add加的是对应的n呢，就得让他成为func的成员

