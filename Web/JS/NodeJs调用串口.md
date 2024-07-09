---
jstitle: NodeJs调用串口
date: 2023-03-01 20:43:38
tags:
categories:
---

# NodeJS中的串口模块SerialPort

SerialPort库的官网：[https://serialport.io/](https://serialport.io/)

SerialPort库的文档：[https://serialport.io/docs/9.x.x/guide-usage](https://serialport.io/docs/9.x.x/guide-usage)

SerialPort库的git仓库：[https://github.com/serialport/node-serialport](https://github.com/serialport/node-serialport)

## 模块安装

使用`npm install serialport`

## 扫描端口

`SerialPort.list(): Promise<PortInfo[]>`静态方法可以获取设备上的窗口列表

这个方法支持异步操作（async / await）

## 打开端口

一般构建`SerialPort`对象时就会打开端口：

```js
//第一个参数是端口路径，第二个是打开端口回调，用于检查端口是否打开成功
const port = new SerialPort('',(err) =>{})
```

可以通过设置`autoOpen`选项设置创建对象时是否自动打开端口，默认为自动打开：

```js
//第二个参数改为配置选项
const port = new SerialPort('',{autoOpen:false})
//把打开的回调放在外面
port.open(function(err){
    if(err){
        console.log('端口打开失败');
        return;
    }
    console.log('端口打开成功');
})
```

还可以在构造方法中使用`baudRate`选项设置串口通讯波特率：

```js
const port = new SerialPort('',{baudRate:115200});
```

> 波特率指每秒钟传送的码元个数。
>
> 串口的传输中1Byte = 10bit，这是因为网络传输时为了区别一个字节的开始和结束需要再每个字节前加一位0，后面加一位1。也就是中间8bit是数据，剩下的2bit是控制信息。

## 发送数据

使用SerialPort对象的`write`方法发送数据，该方法会将要发送的数据放入发送缓存，然后依次发送。

```js
const port = new SerialPort('');
port.write('Hello world');
```

**注意打开端口是异步的，所以可能会先写入缓存，等端口打开再发送。**

还可以添加回调函数：

```js
port.write('Hello world',(err)=>{
	
})
```

**注意write的回调非异常状态触发的时候不代表数据完全从端口发送完成**，需要用`drain`方法来处理：

```js
port.drain(err=>{

})
```

## 接收数据

使用SerialPort对象的`on`方法接收数据；

```js
//用paused mode监听收到的数据，需要主动读取数据
port.on('readable',()=>{
	console.log(port.read());
})

//用flowing mode监听收到的数据
port.on('data',(data)=>{
    console.log(data);
})
```

可以在回调中使用err参数来获取异常对象，也可以通过error事件来统一处理异常：

```js
port.on('error', err => {
    console.log(err);
});
```

