---
title: 微信小程序npm构建
date: 2021-08-18 15:55:36
tags: 微信小程序
categories: Web开发
---
# 微信小程序npm环境配置
1. 首先下载Node.js并配置好环境变量
2. 用cmd打开项目所在目录并执行
```
npm init
npm install
```
3. 在新生成的小程序项目中的project.config.json中找到以下字段并作如下修改
```
    "packNpmManually": true,
    "packNpmRelationList": [{
      "packageJsonPath": "./package.json",
      "miniprogramNpmDistDir": "./miniprogram/"
    }],
```
4. 如果显示缺少package.json所对应的node_modules，只需要在该项目文件夹下新建node_modules文件夹即可
5. 如果显示缺少miniprogram文件夹同上
