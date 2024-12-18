---
title: CentOS配置Python3
date: 2021-11-16 00:00:36
tags: CentOS
categories: 配环境
---

> 参考链接：https://www.cnblogs.com/simuhunluo/p/7704765.html

# 注意！！

CentOS7 yum安装不了一些库，需要gcc4.9以上

# 配置Python3

首先按照上面的参考链接进行配置

# 补充设置

配置完Python3后，由于我们的yum还是基于python2.7的，所以当我们使用yum install的时候会报错。

这时候我们需要改变下系统的配置：

```
vi /usr/libexec/urlgrabber-ext-down
将 #!/usr/bin/python
改为 #!/usr/bin/python2 即可
```

# 安装dlib

> 参考链接：https://blog.51cto.com/u_10931223/2309722

安装不了大概率是要升级GCC版本

升级GCC版本参考：https://blog.csdn.net/libaineu2004/article/details/49160315

# 报以下错误

```
http://people.centos.org/tru/devtools-2/7/x86_64/RPMS/repodata/repomd.xml: [Errno 14] HTTPS Error 404 - Not Found
Trying other mirror.
To address this issue please refer to the below wiki article

```

解决方法

> 参考链接：https://blog.csdn.net/weixin_33717298/article/details/93669045
