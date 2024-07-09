---
title: 使用Fontmin进行字体压缩
date: 2022-01-24 20:03:19
tags:
categories: Web开发
---

> Fontmin官网链接：ecomfe.github.io/fontmin/#source

# 引入

在写web的时候，我们为了网页的美观，经常引入花里胡哨的字体。而一些中文字体的ttf文件非常大，像是我用的一个阿里普惠体的ttf就达到8MB。

![image-20220124200524837](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20220124200524837.png)

如果我们使用url引入字体，当用户打开我们的web应用的时候，就需要去加载这个字体，所以ttf文件太大的话会显著影响加载速度，这时候我们就要对ttf文件进行压缩

# 使用Fontmin

压缩ttf文件，其实就是把我们不需要的文字，从ttf中删去，Fontmin就是这么一个工具，能够帮你把想要的文字保留下来。能把一个MB级别的字体压缩成KB级别，属实nb。

他有两种使用方式：命令行和exe

## 命令行

## exe

exe的操作就比较简单了，将ttf文件拖入下方，在左边的文本框中输入想要保留的文字，最后点击生成，就能得到压缩后的ttf：

![image-20220124201038128](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20220124201038128.png)
