---
title: VScode的React环境配置
date: 2021-07-19 01:14:54
tags: React
category: 配环境
---
# React是什么
React是一个JavaScript框架
使用虚拟DOM来管理页面，比起原生的JS可以更多地进行资源复用，节省空间时间
# 搭建VSCode+React环境
```
参考链接：https://zhengkai.blog.csdn.net/article/details/91970581?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-9.control&depth_1-utm_source=distribute.pc_relevant.
none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-9.control
```
1. 首先以管理员身份打开cmd，用cd指令跳转到想要创建React项目的文件夹，运行下面的指令安装babel
```
npm install --save react react-dom webpack webpack-dev-server html-webpack-plugin
npm install --save --dev babel-loader babel-core babel-preset-react babel-preset-env@next
```
2. 用npm安装create-react-app脚手架
```
npm install -g create-react-app
```
3. 用create-react-app创建项目并进入界面，如果能出现react界面就说明配置成功，如果显示create-react-app not found，可以使用npx命令
```
create-react-app myapp
npx create-react-app myapp //备用
cd myapp
npm start
```
4. 用create-react-app指令的时候可能会报错，如果是command not found，则需要重新进行步骤2。
   
   如果是出现提醒babel版本问题，可以将项目文件夹里的node_modules跟package-lock.json删掉,再运行npm install即可
# React怎么写（简单写法）
```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>示例</title>
  </head>
  <body>
    <!-- 定义容器 -->
    <div id="test"></div>
    <!-- 引入核心包 -->
    <script type="text/javascript" src="../js/react.development.js"></script>
    <!-- 引入核心拓展包 -->
    <script
      type="text/javascript"
      src="../js/react-dom.development.js"
    ></script>
    <!-- 引入babel包 -->
    <script type="text/javascript" src="../js/babel.min.js"></script>
    <script type="text/babel">
    // 定义虚拟DOM
      const VDOM = <h1>标题</h1>
      // 渲染
      ReactDOM.rend(VDOM,document.getElementById('test'))
    </script>
  </body>
</html>
```
