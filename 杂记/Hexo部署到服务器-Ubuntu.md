---
title: Hexo部署到服务器(Ubuntu)
date: 2021-11-01 15:35:29
tags: [Hexo,博客]
categories: 配环境
---

# 前言

博主与2021年11月1日写下这篇博客，Ubuntu版本为18.02

# 下载Apache2

```
apt-get install apache2
```

# 修改Apache端口号

1. 修改配置文件

```
cd /etc/apache2
vim ports.conf
将Listen 80 改为 82
```

2. 添加服务器82端口

![image-20211101154539318](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211101154539318.png)

3. 重启apache2

```
/etc/init.d/apache2 restart
```

# 创建Git用户

1. 创建一个git用户，用来运行git服务。（root不安全）

```
sudo useradd git -m
```

> 注意：Ubantu下必须加上-m，才会自动新建用户目录/home/git/（后面配置和博客文件都放在这里）
>  在/home/git/非常重要，如果出错，可以删除`sudo userdel -r git`用户，重新之前的步骤。

2. 设置密码

```
sudo passwd git
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200229181359235.png)

3. 增加git用户执行sudo的权限

```
chmod 740 /etc/sudoers
vim /etc/sudoers
```

4. 找到以下内容：`root ALL=(ALL) ALL`在下面添加一行

```
git     ALL=(ALL)       ALL
```

5. 保存退出后改回权限：

```
chmod 400 /etc/sudoers
```

# 开启RSA认证

1. 开启认证配置

```
服务器端：

vim /etc/ssh/sshd_config
```

2. 在sshd_config中开启以下几项：(前两项yes，后一项修改为如下)

```
RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile  .ssh/authorized_keys
```

3. 重启sshd

```
systemctl stop sshd.service
systemctl start sshd.service
```

4. 生成RSA公钥

```
ssh-keygen -t rsa -C "你的邮箱"
```

成功后会在.ssh文件夹下生成pub文件

![image-20211101160734887](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211101160734887.png)

5. 在本地电脑使用Git bash发送公钥到服务器

```
ssh-copy-id -i C:/Users/CUNGU/.ssh/id_rsa.pub git@你的服务器IP //把公钥发送给服务器
ssh git@你的服务器IP // 测试能否登录
```

![image-20211101161057714](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211101161057714.png)

6. 调整文件夹权限

```
su git
chmod 700 ~/.ssh
sudo chmod 600 ~/.ssh/authorized_keys
```

# 下载Git

```
apt-get install git
```

# 创建仓库

1. 切换到git用户

```
su git
```

2. 在git用户权限下操作：

```
cd /home/git/
mkdir -p projects/blog  //创建你的博客目录
cd /home/git/
mkdir repos && cd repos
git init --bare blog.git  //创建一个空仓库
cd blog.git/hooks
vi post-receive  //创建hook钩子函数，输入以下内容

#git --work-tree=/home/git/projects/blog --git-dir=/home/git/repos/blog.git
#!/bin/bash
GIT_REPO=/home/git/repos/blog.git    # git仓库
TMP_GIT_CLONE=/home/git/projects/tmp/blog
PUBLIC_WWW=/home/git/projects/blog  # 网站目录
rm -rf ${TMP_GIT_CLONE}
git clone $GIT_REPO $TMP_GIT_CLONE
rm -rf ${PUBLIC_WWW}/*
cp -rf ${TMP_GIT_CLONE}/* ${PUBLIC_WWW}
chmod +x post-receive          # 赋予脚本的执行权限

checkout -f
chmod +x post-receive  // 修改钩子函数权限
```

3. 改变hexo.git目录的拥有者为git用户

```
exit //切换到root用户
cd /home/git/repos
chown -R git:git blog.git
```

4. 添加备份目录

```
cd /home/git/projects/
mkdir -p tmp/blog
```

5. git用户赋权

```
su git
cd /home/git/repos/blog.git/hooks/
chmod +x post-receive
exit
chown -R git:git /home/git/repos/blog.git // 添加权限
chown git:git -R /home/git/projects/
```

6. 本地电脑测试仓库是否可用

```
git clone git@你的公网IP:/home/git/repos/blog.git
```

# hexo配置

在_config.yml中的deploy中增加repo：

```
deploy:
  type: 'git'
  message: update
  github: https://github.com/用户名/仓库名.github.io
  repo: git@公网IP:/home/git/repos/blog.git
  branch: master
```

# openssl配置

```
wget  http://www.openssl.org/source/openssl-1.1.0e.tar.gz
tar -zxvf   openssl-1.1.0e.tar.gz
cd openssl-1.1.0e/ &&./config shared zlib  --prefix=/usr/local/openssl && make && make install  #进入目录把openssl编译安装到 /usr/local/openssl 下
```

此时报错的话，可以参考下面的修改：

```
This issue is due to the Perl package File::Glob, 一些简单的修改就能修复这个. 在 Configure文件和 test/build.info这个文件,
把
use if $^O ne "VMS", 'File::Glob' => qw/glob/;
改成
use if $^O ne "VMS", 'File::Glob' => qw/:glob/;
就解决了
```



# nginx配置

```
apt-get install nginx//安装nginx

//因为懒得配置conf文件所以干脆下载一个
cd /usr/local/
wget http://nginx.org/download/nginx-1.8.1.tar.gz
tar -zxvf nginx-1.8.1.tar.gz
mv nginx-1.8.1 nginx 
cd nginx
./configure --prefix=/usr/local/nginx --with-http_ssl_module

vim /usr/local/nginx/conf/nginx.conf //修改conf文件
```

![image-20211103155341586](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211103155341586.png)

listen表示nginx访问的端口，可以在端口后面加default，这样访问ip时默认会进入这个端口

server_name表示域名（如果有域名就改成你自己的域名）

下面的root index要改成我们刚刚配置的

然后要去阿里云控制台打开39端口（这里端口号可以自己随便选，只要没有被占用就行）

启动nginx服务

```
/usr/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
```

此时使用你的公网IP或者你配置的域名就可以看到nginx页面了

