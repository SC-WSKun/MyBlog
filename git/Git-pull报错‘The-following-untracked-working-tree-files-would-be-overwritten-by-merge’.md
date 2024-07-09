---
title: >-
  Git pull报错‘The following untracked working tree files would be overwritten by
  merge’
date: 2021-08-23 11:14:11
tags: Git
---
# 问题描述
 Git pull时报错“The following untracked working tree files would be overwritten by
  merge”
# 解决方案
用git bash打开项目路径
```
git fetch --all
//输入用户名跟密码
git reset --hard
git pull
//然后就可以拉取分支了
```