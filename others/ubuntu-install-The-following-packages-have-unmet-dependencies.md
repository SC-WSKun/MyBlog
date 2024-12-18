---
title: 'ubuntu install :The following packages have unmet dependencies'
date: 2021-11-28 11:18:33
tags: Ubuntu
categories: 配环境
---

# 问题描述

```
ubuntu install :The following packages have unmet dependencies
```

需要旧版本依赖，但是当前系统的版本较新

# 解决方案

- 安装aptitude

```
sudo apt-get install aptitude
```

- 用aptitude安装包

```
sudo aptitude install 包名
```

- aptitude会自动帮你找到降级方案
  - 一般显示的第一个选项是问你接不接受不安装方案，选择n
  - 第二个选项是降级方案，选择y即可
