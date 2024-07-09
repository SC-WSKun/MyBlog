---
title: Ubuntu18.04配置GLUT、GLFW环境
date: 2021-11-28 11:26:43
tags: Ubuntu
categories: 配环境
---

> 参考链接:https://blog.csdn.net/qq_42608626/article/details/99577448

# 前言

博主用的是Ubuntu18.04，在配置时出现了一些问题，特此记录

# 安装assimp

```
sudo apt-get install cmake
```



# 解决依赖包的问题

# 编译cpp

```
g++ main.cpp glad.c -std=c++17 -lglfw -lvulkan -ldl -lpthread -lX11 -lXxf86vm -lXrandr -lXi -o glfwTest
```



# Windows下的运行方案

下载msys2，看另一篇博客做好glfw，glad，stb_image.h，glm的配置

```
g++ main.cpp glad.c stb_loader.cpp -std=c++17 -lglfw3 -lvulkan -lpthread -o glfwTest
```

