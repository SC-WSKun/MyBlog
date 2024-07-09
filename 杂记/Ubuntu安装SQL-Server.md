---
title: Ubuntu安装SQL Server
date: 2021-10-03 10:47:10
tags: Ubuntu
category: 配环境
---
```
参考链接：https://blog.csdn.net/qq_30653631/article/details/90409044
```
# 安装curl
```
apt install curl
```
# 导入GPG密钥
```
curl https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
```
# 注册SQL库
```
curl https://packages.microsoft.com/config/ubuntu/16.04/mssql-server.list > /etc/apt/sources.list.d/mssql-server.list
```
# 安装SQL Server
```
sudo apt-get update  

sudo apt-get install -y mssql-server
```
这里报错：
```
E: Malformed line 1 in source list /etc/apt/sources.list.d/mssql-server.list (type)
E: The list of sources could not be read.
```
解决方法是把这个出错的文件删除
```
sudo rm /etc/apt/sources.list.d/mssql-server.list
```
# 初始化SQL Server
```
sudo mysql_secure_installation
```
# 检查服务状态
```
systemctl status mysql.service
```
