---
title: OpenGL如何让用户调整窗口大小
date: 2021-11-28 20:12:20
tags: OpenGL
categories:
---

# 实现功能

使用户可以拖拽窗口，实现动态大小

# 代码

## 原型

```
void framebuffer_size_callback(GLFWwindow* window, int width, int height);
```

## 具体实现

```
void framebuffer_size_callback(GLFWwindow* window, int width, int height)
{
    glViewport(0, 0, width, height);
}
```

## 调用

```
glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);
```



