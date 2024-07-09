---
title: vcpkg管理C/C++库文件
date: 2021-10-18 11:47:16
tags: 偷懒用
---

在配置opengl的glfw库时出现了大量问题，后来发现了一个好用的windows的C/C++包管理工具：vcpkg

> Github链接：https://github.com/microsoft/vcpkg/blob/master/README_zh_CN.md#%E5%85%A5%E9%97%A8

在运行bat脚本，生成exe后，可以使用`./vcpkg install [package name]`来配置VS里面的库文件

# 配置Clion

打开 Toolchains 设置 (File > Settings on Windows and Linux, CLion > Preferences on macOS)， 并打开 CMake 设置 (Build, Execution, Deployment > CMake)。 最后在 `CMake options` 中添加以下行:

```
-DCMAKE_TOOLCHAIN_FILE=[vcpkg root]/scripts/buildsystems/vcpkg.cmake
```

**每个项目都需要手动添加**

