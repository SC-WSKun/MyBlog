---
title: AJAX
date: 2021-08-02 17:25:22
tags: AJAX
category: Web开发
---

# AJAX

## AJAX 简介

AJAX(Asynchronous JavaScript and XML)，是一种用于创建快速动态网页的技术。AJAX 是在不重新加载页面的情况下更新部分网页的技术。

## XHR

### XHR 对象

XHR(XMLHttpRequest)用于在后台与服务器交换数据

### 创建 XHR 对象

- 目前版本：

```
variable = new XMLHttpRequest();
```

- 老版本(IE5 和 IE6)：

```
variable = new AvtiveXObject("Microsoft.XMLHTTP");
```

- 兼容写法：

```
var xmlhttp;
if(window.XMLHttpRequest){
    xmlhttp = new XMLHttpRequest();
}
else{
    xmlhttp = new ActiveXObject("Microsoft.XMLHTTP");
}
```

### XHR 请求

- 向服务器发送请求，使用 XHR 的 open()和 send()方法：

```
open(method,url,async)
method:请求的类型，GET or POST
url:文件在服务器上的位置
async:是否异步，true(异步),false(同步)，如果想让XHR用于AJAX，则必须为true。
```

```
send(string)
string:仅用于POST请求
```

```
xmlhttp.open("GET","test1.txt",true);

xmlhttp.send();
```

### GET 请求

- 简单的 GET 请求：

```
xmlhttp.open("GET","demo_get.asp",true);
xmlhttp.send();
```

- 带参数的 GET 请求：

```
xmlhttp.open("GET","demo_get.asp?param1=test1&param2=test2",true);
xmlhttp.send();
```

- 上面的写法可能得到缓存的结果，可以通过给请求增加一个随机参数让浏览器重新请求：

```
xmlhttp.open("GET","demo_get.asp?t="+Math.random(),true);
xmlhttp.send();
```

### POST 请求

```
给请求添加HTTP头的方法
setRequestHeader(header,value)
header:规定头的名称
value:规定头的值
```

- 简单的 POST 请求：

```
xmlhttp.open("POST","demo_post.asp",true);
xmlhttp.send();
```

- 带参数的 POST 请求

```
xmlhttp.open("POST","demo.txt",true);
xmlhttp.setRequestHeader("HeaderName","Header/HeaderValue");
xmlhttp.send("param1=test1&param2=test2");
```

### GET 和 POST 的区别

正常情况下，使用 GET 请求会更快，而且更简单

但是在以下情况只能使用 POST：

1. 无法使用缓存文件
2. 向服务器发送大量数据(POST 没有数据量限制)
3. 发送包含未知字符的用户输入时，POST 比 GET 更稳定也更可靠

### 关于 open( ) 请求的 async 参数

当 async = true 时，执行异步请求，这时浏览器可以去做其他事情而不必等待服务器响应。

当服务器响应时，调用 XHR 的 onreadystatechange( ) 函数，因此 async = true 时必须定义该函数。

当 async = false 时，执行同步请求，这是浏览器会等待服务器响应再继续执行代码。如果服务器繁忙，应用程序会挂起或停止。因此 false 是不被提倡使用的。

async = false 时，不用定义 onreadystatechange( ) 函数，直接再 send( ) 函数后面写代码即可

### XHR 响应

使用 XHR 的 responseText 属性或者 responseXML 属性来接收

```
responseText:
获得字符串形式的响应数据，可以直接使用innerHTML对结点赋值
responseXML:
获得XML形式的响应数据，需要对XML对象进行解析
xmlDoc = xmlhttp.responseXML;
txt = "";
x = xmlDoc.getElemetnsByTagName("ARTIST");
for(i = 0; i < x.length; i++)
{
    txt = txt + x[i].childNodes[0].nodeValue + "<br />"
}
document.getElementByID("myDiv").innerHTML =  txt;
```

