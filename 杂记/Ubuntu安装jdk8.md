---
title: Ubuntu安装jdk8
date: 2021-10-08 22:10:32
tags: [Ubuntu,Java]
category: 配环境
---
# 使用ppa/源安装
```
sudo add-apt-repository ppa:ts.sch.gr/ppa
sudo apt-get update
```
# 安装oracle-java8-installer
```
sudo apt-get install oracle-java8-installer
```
# 设置系统默认jdk
```
sudo update-java-alternatives -s java-8-oracle
或者用
sudo update-alternatives --config java 然后选择想要的版本
```