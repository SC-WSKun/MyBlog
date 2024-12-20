---
title: 浏览器渲染机制
date: 2021-12-07 11:57:03
tags: 浏览器
categories: 浏览器
---

> 参考链接：https://segmentfault.com/a/1190000002629708

# 思维导图

![image-20211207140212025](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211207140212025.png)

渲染过程围绕三棵树进行：DOM树 、CSSOM树 -> 渲染树

步骤可以归纳如下：

# 加载静态资源

HTML的加载是网页的基础，所以要优先下载

在解析HTML的过程中，如果发现有外部资源链接比如CSS、JS、图片等，会启用另外的线程同时加载。

> 比较特殊的是JS，在没有添加defer关键字的情况下，HTML会停止解析，等JS下载并执行结束后再解析HTML，防止JS修改已经完成的解析结果

# 生成DOM树

为了构建DOM树，我们要先从HTML文档的标签中获取静态的标签，然后从JS中获取动态生成的标签，两者结合生成DOM树

# 生成CSSOM树

CSS下载后会对CSS进行解析，解析成CSS对象，再结合JS动态生成的CSS对象组装成CSSOM树

# 构建渲染树

在DOM树和CSSOM树构建完之后，浏览器会根据这两棵树构建一棵DOM树

>- 将不可见的DOM元素去除
>- 将一些DOM元素进行拆分，比如select元素有三个渲染对象：一个显示区域、一个下拉列表及一个按钮；多行文本的新行会以额外的渲染元素被添加
>- 根据CSS选择器计算节点样式

### 样式计算优化

> 样式表有两种来源：浏览器默认样式表、页面作者提供的样式表、用户的样式表（一些网页支持用户自定义，比如一些CSS在线生成网站）。
>
> 样式有三个来源：外部样式表或者内联样式、行内样式属性、HTML可视化属性（方便机器识别网页内容的属性）
>
> 可以看到样式数据非常庞大，如果不进行优化，找到每个元素匹配的规则会导致性能问题，为每个元素查找匹配的规则都需要遍历整个规则表，这个过程有很大的工作量。

1. 共享样式数据（WebKit节点）

   > 共享样式数据需要节点是兄弟节点或者是表兄弟节点，并且有以下要求：
   >
   > 1. 元素必须有相同的鼠标状态，比如hover
   > 2. 不能有元素具有id
   > 3. 标签名必须匹配
   > 4. class属性必须匹配
   > 5. 对应属性必须相同
   > 6. 链接状态必须匹配
   > 7. 焦点状态必须匹配
   > 8. 不能有元素被属性选择器影响
   > 9. 元素不能有行内样式属性
   > 10. 不能有生效的兄弟选择器

2. 规则树（FireFox）

   

# 计算布局（回流）

此时浏览器主要完成两件事：

>- 计算DOM节点的实际大小
>- 识别DOM节点真实位置，比如浮动元素或者绝对定位元素在文本流之外，构建过程要表示出真实的结构而且要用占位符标识出原来的位置

因此当我们每次对DOM节点的**数量**进行更改，或者对大小进行更改，或者对**位置**进行修改，都会触发回流机制（即重新布局）。

**单纯获取位置信息也会触发回流，因为浏览器要保证数据的准确性。**

**但是对布局没有影响的，比如改变背景颜色，则不会触发重绘**

# 渲染（重绘）

浏览器会遍历渲染树，通过渲染引擎绘制UI

所有对元素的外观，风格有影响的都会触发重绘。

# 回流与重绘的优化

1. 减少对单个DOM的修改，尽量用class
2. 可以先设置DOM离线（`display: none`），然后修改完再显示
3. 不要将DOM节点的属性值作为循环里的变量，不然会大量读写这个节点的属性
4. 尽可能修改层级比较低的DOM节点
5. 为动画的HTML元件使用fixed或者absolute的定位，减小reflow
6. 减少使用table布局
