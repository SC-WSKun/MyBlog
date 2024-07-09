---
title: Ubuntu配置PHP环境
date: 2021-09-28 22:23:59
tags: PHP
category: 配环境
---
# 安装Apache
```
sudo apt-get install apache2
```
测试安装是否成功
```
service apache2 status/start/stop/restart
```
# 安装SQL-Server
见另一篇blog
# 安装PHP
```
sudo apt-get install php
```
# 安装其它模块
```
sudo apt-get install libapache2-mod-php
sudo apt-get install php-mysql
```
重启服务
```
service apache2 restart
service mysql restart
```
# 测试Apache能否解析php
```
vim /var/www/html/phpinfo.php
 
按i，进入编辑模式，将文件代码改为：<?php echo phpinfo();?>
按esc，输入':wq'，保存退出
```
如果是图形界面，可以直接点开浏览器输入"http://localhost/phpinfo.php"

如果是命令行界面想测试，可以下载w3m模块
```
sudo apt-get install w3m
w3m http://localhost/phpinfo.php
```
# 修改权限
```
sudo chmod 777 /var/www
```
# 安装phpMyAdmin
```
sudo apt-get install phpmyadmin
```
中间出现界面按空格进入选择apache然后按回车确认

重启、测试
```
service php-fpm restart (如果没有就apt-get install php-fpm吧)
service apache2 restart
```
访问`http://localhost/phpmyadmin`
# 配置apache
```
vim /etc/apache2/apache2.conf
 
末尾添加：
 
include /etc/phpmyadmin/apache.conf
 
AddType application/x-httpd-php .php .htm .html
 
AddDefaultCharset UTF-8
```
重启服务
```
service apache2 restart
```
访问`http://localhost/phpmyadmin/`