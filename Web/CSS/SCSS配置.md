---
title: SCSS配置
date: 2022-08-26 10:36:20
tags: CSS
categories: 
---

# 环境

- 系统：Windows
- IDE：Webstorm

# 步骤

1. 去Ruby官网[下载Ruby](https://rubyinstaller.org/downloads/)，下载安装完成后，打开CMD命令行，输入`ruby -v`查看是否安装成功

   ![1661481664164](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/20220826104155.png)

2. 使用Ruby安装sass，在CMD命令行中输入`gem install sass`，该命令一定要在system32文件下cmd.exe执行，可以在路径`C:\Windows\System32\cmd.exe`找到。

   安装完成后用`sass -v`命令检查安装是否成功

   ![sass安装成功截图](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/20220826104512.png)

3. 打开WebStorm，创建空项目，点击`File -> Setting -> Tools -> File Watcher`，按照下面步骤进行配置

   ![添加sass](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/20220826104933.png)

   ![配置scss](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/20220826105224.png)

   保存应用即可

4. 新建一个scss文件，正常的话会自动生成下面的目录结构

   ![scss目录结构](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/20220826105419.png)