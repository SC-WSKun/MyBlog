---
title: Web性能优化
date: 2022-04-01 16:32:35
tags: Web开发
categories: Web开发 
---

> 参考：《JavaScript DOM 编程艺术》

# 尽量少访问DOM和尽量减少标记

访问DOM的方式对脚本性能会产生较大的影响。比如对于下面的这类代码：

```js
if(document.getElementsByTagName("a").length > 0){
	var links = document.getElementsByTagName("a");
    for(let i = 0; i < links.length; i++){
        //...
    }
}
```

重复做了对DOM节点的遍历，应该先获取所有a节点再进行长度判断。

# 合并和放置脚本

尽量避免下面这种情况：

```html
<script src="script/funcA.js"></script>
<script src="script/funcB.js"></script>
<script src="script/funcC.js"></script>
<script src="script/funcD.js"></script>
```

多个`js`文件会增加请求数量，会有显著的性能影响。

推荐的做法是将四个`js`文件合并起来。

`<script>`标签放在文档的位置也有讲究，放在`<head>`块中的脚本会导致浏览器无法并行加载其他文件。一般来说，根据HTTP规范，浏览器每次从同一个域名中最多同时下载两个文件。而在下载脚本期间，浏览器不会下载其他文件，即使是来自其他域名的文件也不会下载。所以我们**把`<script>`标签放到文档的末尾，`</body>`标记之前**，就可以让页面变得更快。

# 压缩脚本 

所谓压缩脚本，就是把脚本文件中不必要的字节，如空格和注释，统统删除，达到“压缩”文件的目的。在现在的打包文件中很多都已经集成了这个功能，比如`webpack`。

