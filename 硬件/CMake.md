---
title: CMake
date: 2022-04-24 22:37:25
tags: CMake
categories: C++
---

> 参考链接：https://blog.csdn.net/Yong_Qi2015/article/details/115191703

# C/C++的编译过程

C/C++程序编译的主要工作流程为：

> 源代码 → 预处理器 → 编译器 → 汇编器 → 链接器 → 可执行程序

- 预处理：

  我们在写代码时会经常出现`#define`或者`#include`，这些以`#`开头的命令都是预编译处理指令和预处理操作符，这些实际上并不属于C/C++中的语句不能被编译程序所识别，所以我们需要在编译之前做一个预处理，输出一个`.i`文件

- 编译：

  编译就是把C/C++代码转换成汇编代码。编译程序需要通过词法分析和语法分析，在确认所有的指令都符合语法规则没有语法错误之后，把代码转换成汇编语言，生成汇编代码。

- 汇编：

  汇编就是将上一步输出的汇编代码翻译转换成目标机器指令的过程，生成的目标文件是与源程序等效的机器语言。

- 链接：

  链接就是将汇编生成的目标文件、系统库的目标文件、库文件链接起来，生成可以在某一特定平台运行的可执行程序。

![image-20220424225842926](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20220424225842926.png)

# Make和Makefile是什么关系

在做操作系统课设的时候，我们可能用过`gcc`来生成执行文件，但是众所周知`gcc`指令通常是比较长的，所以我们需要一个自动化的编译工具。

Make是一个自动化软件，用于将源代码文件编译为可执行的二进制文件从而完成自动化编译。Make工具编译的时候需要`Makefile`文件提供编译规则，`Makefile`定义了一系列的编译规则，包括编译的先后顺序，哪些文件需要重新编译等操作。

如果我们只修改了某几个源文件，Make工具则只重新编译这几个源文件。如果某个头文件被修改了，则重新编译所有包含该头文件的源文件。这种自动编译极大地提高了开发效率。

# 什么是CMake

CMake是用来输出各种`Makefile`文件或者工程文件的跨平台项目管理工具。它会根据平台为各个编译器生成工程文件。

CMake命令的执行按照的规则是由`CmakeLists.txt`文件编写的。

CMake的流程如下：

1. 编写源文件
2. 编写`CMakeLists.txt`
3. 由CMake根据`CMakeLists.txt`生成`Makefile`
4. 由Make根据`Makefile`，调用`gcc`生成可执行文件

# 编写CMakeLists

CMakeLists.txt的编写主要包含以下步骤：

1. cmake_minimum_required(VERSION 2.8.0)：用于指定cmake所需最低版本；
2. project(Project) ：用于指定项目名称；
3. include_directories() ：用于包含头文件目录；
4. aux_source_directory(src dir_srcs)：用于包含源文件目录；
5. set(TEST_MATH) ：用于设置环境变量，编译用到的源文件全部都要放到这里；
6. add_executable(${PROJECT_NAME} ${TEST_MATH})：用于添加要编译的可执行文件；
7. target_link_libraries(${PROJECT_NAME} m)：用于添加可执行文件所需要的库；

这里给出一些环境变量的定义：

- CMAKE_MAJOR_VERSION：cmake 主版本号；
- CMAKE_MINOR_VERSION：cmake 次版本号；
- CMAKE_C_FLAGS：设置 C 编译选项；
- CMAKE_CXX_FLAGS：设置 C++ 编译选项；
- PROJECT_SOURCE_DIR：工程的根目录；
- PROJECT_BINARY_DIR：运行 cmake 命令的目录；
- CMAKE_CURRENT_SOURCE_DIR：当前CMakeLists.txt 所在路径；
- CMAKE_CURRENT_BINARY_DIR：目标文件编译目录；
- EXECUTABLE_OUTPUT_PATH：重新定义目标二进制可执行文件的存放位置
- LIBRARY_OUTPUT_PATH：重新定义目标链接库文件的存放位置

在编译前，我们将自己的文件一个个链接到`CmakeList`中就太慢了，我们可以使用下面的指令批量进行打包：

```cmake
file(GLOB_RECURSE SRC_LIST        ${CMAKE_CURRENT_SOURCE_DIR}/modules/src/*.cpp)
aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/modules/common SRC_COMMON_LIST)
add_library(perception STATIC            ${SRC_LIST}    ${SRC_COMMON_LIST})
```

`file()`通过自定义搜索规则将文件中的内容存储到变量中，其中参数`GLOB`会产生一个所有匹配`globbing`表达式的文件组成的列表将其保存到变量`SRC_LIST`，或者使用参数`GLOB_RECURSE`遍历匹配目录的所有文件以及子目录下面的文件将其保存到变量`SRC_LIST`

`aux_source_directory()`的作用与`file()`类似，查找指定目录下的所有源文件，然后将结果存进指定变量名。如上述代码将`common`路径下的文件保存到变量`SRC_COMMON_LIST`中。

`add_library`用于将变量中保存的源文件打成库的形式，可以通过参数选择静态库或动态库。如：

`add_library(perception STATIC ${SRC_LIST})`

`add_library(perception SHARED ${SRC_LIST})`

```cmake
link_directories(    ${CMAKE_CURRENT_SOURCE_DIR}/modules/lib/)
add_executable(main ${SRC_FILES})
target_link_libraries(main     libdetect.a    libsegment.a    libtracker.a)
```

最后就是生成可执行文件并链接库文件的环节，add_executable使用指定的源文件来生成目标可执行文件。

目标可执行文件分为三类：

- 普通可执行目标文件
- 导入可执行目标文件
- 别名可执行目标文件

其中`link_libraries`和`target_link_libraries`这两个命令长得挺相似，功能却不同：

`link_libraries`是指定要链接的库文件路径。自己生成的库文件可以用该指令指定目录的路径以便工程能够找到。

`target_link_libraries`是将目标文件与库文件进行链接，可以指定动态库/静态库，如果只提供库名称，系统会根据链接库目录搜索`xxx.so` 或者 `xxx.a` 文件；或者指定给出全路径。
