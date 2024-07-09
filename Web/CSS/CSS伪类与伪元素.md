---
title: CSS伪类与伪元素
date: 2022-02-26 14:48:37
tags: CSS
categories: CSS
---

# 为什么要用伪类和伪元素

css引入伪类和伪元素是为了格式化文档树以外的信息，即用来修饰不在文档树中的部分。

# 伪类和伪元素的区别

> **伪类是用于当已有元素处于某个状态时，为其添加对应的样式**。

这个状态是根据用户行为而动态变化的。比如，当用户悬停在指定的元素时，我们可以通过`:hover`来描述这个元素的状态。虽然它和普通的`css`类相似，可以为已有的元素添加样式，但是它只有处于dom树无法描述的状态下才能为元素添加样式，所以称为伪类。

> **伪元素是用于创建一些不在文档树中的元素，比如经典的利用`:before`和`:after`画一些组合图形。**

虽然用户可以看到这些图形，但是这些图形实际上不在文档树中。



**总的来说，区别伪类和伪元素就是看有没有创建一个文档树以外的元素**

# 常用的伪类

> 伪类使用单冒号来表示

---

状态相关：

- `:link`：连接

- `:visited`：被访问过
- `:hover`：鼠标放置在上面
- `:active`：被点击的时候
- `:focus`：焦点在上面的时候

---

结构化相关：

- `:not`：

- `:first-child`：只会匹配父元素中的第一个子元素，前面不可以有其他元素

  html：

  ```html
  <div>
  	<p></p>
  	<h1></h1>
  </div>
  ```

  css：

  ```css
  p:first-child{	//可以匹配
  	color:red
  }
  h1:first-child{ //前面有p元素，匹配不到
  	color:red
  }
  ```

- `:last-child`：父元素中的最后一个子元素才会匹配

- `:first-of-type`：父元素中的第一个该类型的元素，前面可以有其他类型的元素

  html：

  ```html
  <div>
  	<p></p>
  	<h1></h1>
  </div>
  ```

  css：

  ```css
  p:first-of-type{	//可以匹配
  	color:red
  }
  h1:first-of-type{ 	//可以匹配
  	color:red
  }
  ```

- `:last-of-type`：

- `:nth-child`：

- `:nth-of-type`：

- `:nth-last-of-type`：

- `:only-child`：

- `:only-of-type`：

- `:target`：

---

表单相关：

- `:check`
- `:default`
- `:disabled`
- `:empty`
- `:enabled`
- `:in-range`
- `:out-of-range`
- `:indeterminate`
- `:valid`
- `:invalid`
- `:optional`
- `:required`
- `:read-only`
- `:read-write`
- `:scope`

## 常用的伪元素

在CSS3标准中，伪元素使用双冒号来表示，但大部分伪元素也支持单冒号

{% note info %}

如果是为了向后兼容，可以选择单冒号。

如果是为了保证不出错或者做点区分，可以全都使用双冒号。

{% endnote %}

单双冒号：

- `::before`
- `::after`
- `::first-letter`
- `::first-line`

仅双冒号：

- `::selection`
- `::placeholder`
- `::backdrop`
