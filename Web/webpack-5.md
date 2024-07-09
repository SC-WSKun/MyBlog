---
title: webpack 5
date: 2022-02-16 12:03:28
tags: webpack
categories: Web开发
---

> 参考：《webpack官方文档》《webpack指南》

# 什么是webpack

webpack是一个用于web项目的模块打包工具，可以讲前端各种资源统一打包为`.js`文件和资源文件

![image-20220217130131882](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20220217130131882.png)

 # 模块化时代

浏览器对资源加载有同源策略限制，也不支持编程化加载资源。最终大部分的加载器选择通过`<script>`标签加载，然后通过各种hack判断是否加载完成

比如：

- AMD：require.js
- CMD：command.js
- UMD

# 进入webpack

webpack是一个在开发阶段进行打包的模块化工具，即不经过打包无法直接在线上使用。而require.js / sea.js可以直接在线上使用。

webpack同时兼容AMD、Command.js以及非模块化的写法，所以当我们遇到一个经手过许多人开发的老项目时，使用兼容性好的webpack显然更加舒服。

安装webpack前，我们需要先安装Node.js，如何安装可以自行百度一下。

安装后使用以下代码:

```shell
npm install -g webpack webpack-cli
或者选择局部安装
npm install --save-dev webpack webpack-cli
```

可能需要科学上网，如果没有换源的话。

# webpack相关的一些概念

## 入口文件

入口文件就是在HTML中直接引用的，由浏览器触发执行的JS文件。其他的非入口文件则是由入口文件来直接或间接依赖，由JS互相调用执行。也就是入口文件就是我们打包的起点。

示例：

index.html

```html
<html>
    <head>
        <script src="main.js"></script>
    </head>
	<body>
    </body>
</html>
```

main.js

```js
var a = require('./a');
var b = require('./b');
a.doSth();
b.doSth();
```

这里main.js由HTML直接引用，是整个程序逻辑开始的地方，因此是入口文件，而被引用的`a`和`b`则是由`main.js`引入和调用，因此不是入口文件

# 非模块化文件打包

webpack是一个开发时进行打包的工具，因此我们需要准备两份文件，一份用于开发维护的源码，一份则是由webpack打包生成的文件。

示例：

example1.1.html

```html
<html>
    <head>
        <title>webpack.toobug.net</title>
        <script src="./bundle1.1.js"></script>
    </head>
    <body>
    </body>
</html>
```

example1.1.js

```js
alert('hello world');
```

使用命令

```shell
cd {index.html所在目录}
webpack example1.1.js bundle1.1.js
```

可以看到目录下生成了一个`bundle1.1.js`，就是我们html中引用的JS文件

打开这个js文件，我们可以看到

```js
/******/ (function(modules) { // webpackBootstrap
/******/ 	// The module cache
/******/ 	var installedModules = {};

/******/ 	// The require function
/******/ 	function __webpack_require__(moduleId) {

/******/ 		// Check if module is in cache
/******/ 		if(installedModules[moduleId])
/******/ 			return installedModules[moduleId].exports;

/******/ 		// Create a new module (and put it into the cache)
/******/ 		var module = installedModules[moduleId] = {
/******/ 			exports: {},
/******/ 			id: moduleId,
/******/ 			loaded: false
/******/ 		};

/******/ 		// Execute the module function
/******/ 		modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);

/******/ 		// Flag the module as loaded
/******/ 		module.loaded = true;

/******/ 		// Return the exports of the module
/******/ 		return module.exports;
/******/ 	}


/******/ 	// expose the modules object (__webpack_modules__)
/******/ 	__webpack_require__.m = modules;

/******/ 	// expose the module cache
/******/ 	__webpack_require__.c = installedModules;

/******/ 	// __webpack_public_path__
/******/ 	__webpack_require__.p = "";

/******/ 	// Load entry module and return exports
/******/ 	return __webpack_require__(0);
/******/ })
/************************************************************************/
/******/ ([
/* 0 */
/***/ function(module, exports, __webpack_require__) {

        var __WEBPACK_AMD_DEFINE_ARRAY__, __WEBPACK_AMD_DEFINE_RESULT__;!(__WEBPACK_AMD_DEFINE_ARRAY__ = [
        ], __WEBPACK_AMD_DEFINE_RESULT__ = function(){
            alert('hello world!');
        }.apply(exports, __WEBPACK_AMD_DEFINE_ARRAY__), __WEBPACK_AMD_DEFINE_RESULT__ !== undefined && (module.exports = __WEBPACK_AMD_DEFINE_RESULT__));
/***/ }
/******/ ]);
```

可以看到，这个源码由两部分组成，前面部分是RUNTIME，保证模块的顺利加载和运行。其中有一段代码：

```
module[moduleId].call(module.exports,modules,module.exports,__webpack_require__)
```

这段代码就是模块引入的代码，其中参数包含了：模块引入函数、引入模块、依赖引入函数、依赖模块

然后由于使用了`.call`函数，所以this被绑定了，因此，如果我们使用var定义了全局变量时，此时它不会被绑定到`window`对象上，所以如果我们使用webpack打包文件，我们需要把所有的全局变量定义为`window.`

另一个部分就是模块部分，可以看到我们的helloworld代码被一个函数包裹起来，变成了一个模块，作为modules参数传入RUNTIME中

# AMD模块打包

AMD（异步模块加载机制）是以`require.js`为代表的新的JS架构方案和标准，其核心就是`define([module-name?], [array-of-dependencies?], [module-factory-or-object]);`，所以webpack针对AMD模块的打包就围绕着解析这个define展开。

下面是对一个AMD模块打包生成的js文件代码，RUNTIME部分与非模块依赖打包是一样的，因此我们只讨论模块部分：

```js
    function(module, exports, __webpack_require__) {
        var __WEBPACK_AMD_DEFINE_ARRAY__, // AMD依赖列表
            __WEBPACK_AMD_DEFINE_RESULT__; // AMD factory函数的返回值，即模块内容
        __WEBPACK_AMD_DEFINE_ARRAY__ = [];
        // 执行factory函数，获取返回值作为模块内容
        // 函数体使用.apply调用，函数体中this为exports
        // 形参则分别对应依赖列表中的各个依赖模块
        __WEBPACK_AMD_DEFINE_RESULT__ = function(){
            alert('hello world!');
        }.apply(exports, __WEBPACK_AMD_DEFINE_ARRAY__);
        // 如果模块内容不为空，则通过module.exports返回
        // 如果为空，则不处理，在Runtime中声明为{}
        if(__WEBPACK_AMD_DEFINE_RESULT__ !== undefined){
            module.exports = __WEBPACK_AMD_DEFINE_RESULT__;
        }
    }
```

首先是两个变量：

- \_\_WEBPACK_AMD_DEFINE_ARRAY\_\_：AMD里面的依赖列表
- \_\_WEBPACK_AMD_DEFINE_RESULT\_\_：AMD factory返回的结果，即模块的内容

然后下面的apply调用，将this绑定在exports，也就是我们返回的模块上，再将AMD依赖作为参数，这样就实现了对AMD的解析以及重包装；

最后通过`module.exports`使返回的模块能被引用

# CLI的使用

我们之前使用的是：

```shell
webpack example1.1.js bundle1.1.js
```

当我们的项目比较大的时候，参数会非常多，导致命令很长，这时我们可以使用配置文件`webpack.config.js`，在里面写入配置项，执行webpack的时候会从该配置文件中读取参数

```
webpack
或者用参数指定配置文件
webpack -c myconfig.js
```

## 配置文件写法

```
module.exports = {
	//配置项
}
```

# API的使用

webpack支持被作为Node.js模块使用，例如：

```
webpack({
	entry:'main.js',
	...
},callback)
```

# 基本配置项

## entry（入口文件）和output（输出文件）

基本用法：

```js
module.exports = {
	entry:'./example1.1',
	output:{
		filename:'bundle1.1.js'
	}
}
```

- output的filename占位符：

  - name：模块名称

  - hash：模块编译后整体的hash值

  - chunkhash：分片的hash值

{% note info %}

整体和分片的区别：有时候入口文件不止一个，如果没有分别对入口文件命名，则这时候他们会被视为同一个模块，webpack会将他们的代码合并打包，此时生成的这两个文件的hash是一致的，而chunkhash则是不一致的

{% endnote %}

- output.path

  当我们希望指定输出文件路径的时候，可以使用`output.path`

  ```js
  entry:{
      'example4.1':'src/example4.1'
  },
  output:{
      filename:'[name].js',
      path:'./dist'
  }
  ```

  如果我们想要让文件存在子目录下，则应该在entry的模块名上修改，而不是`output.path`

  ```js
  entry:{
  	'hello/example4.2':'./src/helloexample4.2'
  }
  output:{
  	filename:'[name].js',
  	path:'./dist'
  }
  ```

  {% note info %}

  对资源使用 CDN 和 hash 的复杂示例：

  **config.js**

  ```javascript
  module.exports = {
    //...
    output: {
      path: '/home/proj/cdn/assets/[fullhash]',
      publicPath: 'https://cdn.example.com/assets/[fullhash]/',
    },
  };
  ```

  如果在编译时，不知道最终输出文件的 `publicPath` 是什么地址，则可以将其留空，并且在运行时通过入口起点文件中的 `__webpack_public_path__` 动态设置。

  ```javascript
  __webpack_public_path__ = myRuntimePublicPath;
  
  // 应用程序入口的其余部分
  ```

  {% endnote %}

# package.json配置

## script

可以在script中配置来生成一个快捷方式

```json
"scripts":{
    "test":"echo \"Error: no test specified\" && exit 1",
    "build":"webpack"
}, 
```

现在就可以直接使用`npm run build`来替代之前打包的指令。

{% note info %}

添加自定义参数的方法：

```
npm run build -- --[param]
```

{% endnote %}

# 分片

> 随着项目进展，我们代码的体积越来越大，这时候如果将所有脚本打包到同一个JS文件会造成性能方面的问题（首次加载时间长，无法并发），因此webpack提供了代码分片机制，也是方便代码拆分后进行异步加载
>
> {% note warning %}
>
> webpack的代码分片不能提供公共代码提取和复用的功能，这部分功能由CommonChunk插件来完成
>
> {% endnote %}

## 分割点

> 代码在此处被分割成两个独立的文件

- 使用`require.ensure`:

  ```js
  require.ensure(["module-a","module-b"],function(require){
  	var a = require("module-a");
  	//...
  })
  ```

- 使用AMD的动态`require`：

  ```js
  require(["module-a","module-b"],function(a,b){
  	//...
  })
  ```

通过上面两种方法，`module-a`和`module-b`就会被分割到独立的文件中去，而不会和入口文件打包在同一个文件中

举个例子：

```js
var a=require('./a');
a.sayHello();

require.ensure(['./b'], function(require){
    var b = require('./b');
    b.sayHello(); 
});

require(['./c'], function(c){
    c.sayHello();
});
```

打包后的代码为：

- bundle.js -> main.js + a.js
- 1.bundle.js -> b.js
- 2.bundle.js -> c.js

多入口情况下：

- 入口1 bundle.main1.js -> main.js + a.js
- 入口2 bundle.main2.js -> main2.js + a.js
- 1.bundle.1.js -> b.js
- 2.bundle.2.js -> c.js

所以a.js没有被提取出来进行复用

# loader

> webpack只能理解`JS`和`JSON`文件，而loader就是一个编译前的预处理器，能让webpack处理其他类型的文件，这也是webpack相对于其他打包工具的优势，可以直接通过`import`导入任何类型的模块

## loader的属性

我们可以在配置文件中的`module.rules`中配置loader

- test：识别出哪些文件会被转换
- use：定义出在转换时，应该使用哪个loader

例如：下面的代码告诉webpack编译器，当解析到`.txt`结尾的文件时使用`raw-loader`进行转换

```js
const path = require('path');

module.exports = {
  output: {
    filename: 'my-first-webpack.bundle.js',
  },
  module: {
    rules: [
        { test: /\.txt$/, use: 'raw-loader' }
    ],
  },
};
```

{% note warning %}

在test中使用正则表达式匹配文件时，不要为它添加引号。

/\.txt$/表示任何以.txt结尾的文件

“/.txt$/"表示webpack匹配具有绝对路径`.txt`的单个文件

{% endnote %}

# plugin

> webpack插件是一个具有`apply`方法的JavaScript对象。apply方法会被`webpack compiler`调用，并且在整个编译声明周期都可以访问`compile`对象

由于插件可以携带参数/选项，所以必须在webpack配置中向plugins属性传入一个new实例
