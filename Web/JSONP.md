---
title: JSONP
date: 2022-03-11 08:46:32
tags:
categories:
---

> 参考链接：https://segmentfault.com/a/1190000009773724

# JSONP是什么

JSONP（JSON with Padding）是JSON的一种“使用模式”，可用于解决主流浏览器的跨域数据访问的问题。

# 为什么可以用JSONP解决跨域

`html`的`<script>`、`img`、`iframe`可以请求第三方的资源，不受同源策略的影响。因为web页面可以加载放在任意站点的`js`、`css`、图片等资源，不会收到跨域的影响。

所以我们可以将数据放到第三方站点的js中，从而使前端能访问到数据，而JSON就是一个JS支持的可以表示复杂的数据结构的一种语言规范。

# 简单实现

index.html:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <div>jsonp</div>
</body>
<script>
    function getRemoteData(data) {
        console.log(data);
    }
    var div = document.getElementsByTagName('div');

    div[0].onclick = function(){
        var url = "./getData.js";
        var script = document.createElement('script');
        script.setAttribute('src', url);
        document.getElementsByTagName('head')[0].appendChild(script);
    };
</script>
</html>
```

getData.js：

```js
getRemoteData({
    code:0,
    result:'success'
});
```

![2413953772-59409fa12c4dc](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/2413953772-59409fa12c4dc.webp)

**当我们正常地请求一个JSON数据的时候，服务端返回的是一串JSON类型的数据。**
**而我们使用JSONP模式来请求数据的时候，服务端返回的是一段可执行的JavaScript代码。**

# JSONP的优缺点

优点：

- 它不像XHR对象实现的Ajax请求那样收到同源策略的限制，JSONP可以跨越同源策略
- 它的兼容性更好，不需要XHR或者ActiveX的支持
- 在请求完毕后可以通过调用callback的方式回传结果，将回调权限给了调用方。

缺点：

- 只支持GET请求而不支持POST等其它类型的HTTP请求
- 只支持跨域HTTP请求，不能解决不同域的两个页面之间如何进行JS调用的问题
- 调用失败的时候不会返回HTTP状态码
- 安全性较低，如果JSONP的服务存在页面注入漏洞，则所有调用这个JSONP的网站都会存在漏洞
