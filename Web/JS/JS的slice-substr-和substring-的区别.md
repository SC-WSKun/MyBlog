---
title: 'JS的slice(),substr()和substring()的区别'
date: 2022-01-24 09:37:45
tags: JavaScript
categories: JavaScript
---

# 共同点

{% note info %}

- 这三个函数都是从字符串中截取子串，都具有两个参数

- 都不会对原来的字符串产生影响，这个跟基本包装类型的运行机制有关

{% endnote %}

# 不同点

- 参数代表意义不同

  {% note info %}

  - slice：第一个参数代表开始截取的位置，第二个参数代表截取的长度
  - substr：第一个参数代表开始截取的位置，第二个参数代表截取的长度
  - substring：第一个参数代表开始截取的位置，第二个参数代表**截取结束的位置**

  {% endnote %}

- 参数为负数时处理方式不同

  {% note info %}

  - slice：将负值与字符串的长度相加
  - substr：对于第一个参数，会与字符串的长度相加；对于第二个参数，会转变成0
  - substring：会把所有负数都转化为0

  {% endnote %}

