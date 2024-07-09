---
title: NodeJs
date: 2021-09-13 15:34:17
tags: NodeJs
---
# 配置NodeJs
# NodeJS三大模块

## fs模块

fs模块负责文件的读写，可以通过引入fs模块来进行一些文件操作

### fs.readFile()

```js
const fs = require("fs");
fs.read(path[, options],callback);
```

path：文件路径

options：读取参数，使用什么编码

callback：读取后执行的回调函数，一般用`function(err,dataStr)`

### fs.writeFile()

```js
const fs = require("fs");
fs.write(file, data[, options], callback)
```

file：文件路径的字符串，表示文件的存放位置

data：要写入的内容

options：表示要用什么格式写入文件内容

callback：回调函数

## path模块

path模块主要用于处理文件路径拼接，还有获取文件名、扩展名等与文件路径有关的操作。

### path.join()

在正常处理路径的时候可能会使用字符串拼接，但是如果拼接的字符串中存在相对路径，那么拼接后的字符串可能会出现`./`或者`../`，而这个字符串会被电脑视为绝对路径，因此不会对`./`和`../`进行对应目录的替换，从而找不到目标文件。

`path.join()`会自动对参数中的`./`和`../`进行替换，保证最终路径的正确：

```js
const path = require('path')
let filepath = path.join(__dirname, './setting.txt')
//__dirname 是文件所在位置的绝对路径
//这里会对./自动进行处理
```

### path.baseName()

## http模块

通过http模块，可以将电脑变成web服务器，对外提供web资源服务，而且不需要安装IIS、Apache等服务器软件。

### 引入http模块

```js
const http = require("http")
```

### 创建Web服务器

```js
const server = http.createServer()
```

### 绑定request事件

```js
server.on('request',(req,res)=>{
	console.log('someone request server')
})
```

### 启动服务器

```js
server.listen(80,()=>{
	console.log('http server running at port 80')
})
```

### req请求对象的一些常用属性

```js
server.on('request',(req,res)=>{
    //请求的源网址
    const url = req.url
    //请求的方法
    const method = req.method
})
```

### res响应对象

```js
server.on('request',(req,res)=>{
    //res.setHeader()设置返回响应头
    res.setHeader('Content-Type','text/html; charset-utf-8')
    //res.end()发送给客户端指定内容，并结束此次请求的处理过程化
	res.end("<h1>hello</h1>")
})
```

### 动态响应内容

```js
server.on('request',(req,res)=>{
    //请求的源网址
    const url = req.url
    //返回的内容
    let content = '<h1>404 Not Found</h1>';
    if(url === '/' || url = '/index.html'){
        content = ''
    }else if(url === ''){
        
    }
    //res.setHeader()设置返回响应头
    res.setHeader('Content-Type','text/heml; charset-utf-8')
    //res.end()发送给客户端指定内容，并结束此次请求的处理过程化
	res.end(content);
})
```



