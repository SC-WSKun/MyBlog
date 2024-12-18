---
title: Windows配置apache-maven
date: 2021-10-09 14:21:19
tags: Java
category: 配环境
---
# 问题描述
在编译java程序的时候报错
```
mvn 不是外部指令...
```
# 配置流程
1. 下载apache-maven-3.6.0-bin.zip，解压得到文件夹
    
    > apache-maven下载链接：http://maven.apache.org/download.cgi
2. 配置环境变量
    ```
    增加系统变量MAVEN_HOME为解压后文件夹所在的地址，例如：D:\下载工具   \apache-maven-3.8.3-bin\apache-maven-3.8.3
    编辑Path环境变量，添加%MAVEN_HOME%\bin
    ```
3. 测试配置是否成功
   
   打开cmd，输入代码：
   ```
   mvn -v
   ```
