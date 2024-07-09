---
title: Msys2配置OpenGL环境
date: 2021-12-09 17:05:34
tags:
categories: 配环境
---

# GLFW

```
    pacman -Sl | grep mingw64
    pacman -S mingw-w64-x86_64-SDL2
    pacman -S mingw-w64-x86_64-SDL2_image
    pacman -S mingw-w64-x86_64-SDL2_mixer
    pacman -S mingw-w64-x86_64-devil
    pacman -S mingw64/mingw-w64-x86_64-glfw
```

环境变量

算是比较重要的配置,笔者之前就是因为环境变量没配好导致无法识别到SDL库 打开windows属性->高级->环境变量  我们直接设置系统变量 找到Path,打开新建吧../msys64/mingw64/bin复制进去,根据你的具体路径填写,这里只做参考  然后回到系统变量,新建一个`C_INCLUDEDE_PATH`

输入值为`D:\msys64\mingw64\include\`

再新建一个`CPLUS_INCLUDE_PATH`

输入`D:\msys64\mingw64\include\; D:\msys64\mingw64\include\c++\7.2.0` 再新建`LIBRARY_PATH`

输入`D:\msys64\mingw64\lib`
