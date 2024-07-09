---
title: 安装Npm
date: 2022-03-03 17:20:13
tags:
categories: 配环境
---
# 安装NodeJS

下载链接：https://nodejs.org/zh-cn/

一路next安装后进入下一步

# 重新设置`node_global`和`node_cache`位置（如果不想放在C盘的话）

在想要重定向的文件夹里新建两个文件夹：`node_global`和`node_cache`（我放在nodeJs文件夹下），然后运行以下命令：

```shell
npm config set prefix "新的node_global路径"
npm config set cache "新的node_cache路径"
```

# 配置镜像源

```shell
npm config set registry=http://registry.npm.taobao.org
```

