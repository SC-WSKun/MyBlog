---
title: include的用法
date: 2022-05-03 12:02:12
tags: C++
categories: C++
---

> 参考链接：https://blog.csdn.net/weixin_44705391/article/details/106867765

# include工作原理

假设有以下的工程结构：

![image-20220503120751519](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20220503120751519.png)

可以看到项目结构是这样的

```
- 根目录
	- main
		- main.c
		- func1.h
		- func1.c
	- func2
		- func2.h
		- func2.c
	- func4
		- func4.h
		- func4.c
	- func3.h
	- func3.c

```

根据目标文件位置的不同，我们在下面几种使用情况下需要使用不同的include格式：

- 在`main`中包含头文件`func1.h`（目标文件与源文件处于同一个文件夹下）:

  ```C++
  #include "func1.h"
  ```

- 在`main`中包含头文件`func2.h`（目标文件在源文件的子文件夹）：

  ```C++
  #include "func2\func2.h"
  ```

- 在`main`中包含头文件`func3.h`（目标文件在源文件的父文件夹）：

  ```C++
  #include "..\func3.h"
  ```

- 在`main`中包含头文件`func4.h`（目标文件与源文件在两个不同的文件夹，没有直接的父子关系）：

  ```C++
  #include "..\func4\func4.h"
  ```

  
