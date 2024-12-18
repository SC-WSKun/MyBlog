---
title: 使用Typora+PigGo+Github图床写博客
date: 2021-10-14 22:23:32
tags: 博客 
category: 配环境
---

# 前情提要

[用Hexo搭了个人博客](http://112.74.85.250:39/2021/11/01/Hexo%E9%83%A8%E7%BD%B2%E5%88%B0%E6%9C%8D%E5%8A%A1%E5%99%A8-Ubuntu/)之后，用VSCode写了一段时间的markdown，后来发现太不方便了，于是找了这么一套个人认为最舒服的写博客的环境

# 下载Typora（注意版本要高于0.9.83）

> 参考链接：https://www.typora.io/#windows

![▲ 更新日志](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/aHR0cDovL2hveGlzLmliZXRhbGlmZS5jb20vcGljZ28vaW1hZ2UtMjAyMDAzMTAxMTQ4MDA0MjUucG5n)

**只有在0.9.83后的版本才支持PicGo**

## 配置Typora

打开设置，进行如图配置

![image-20211015004841829](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211015004841829.png)

# 建立自己的Github仓库

新建一个Github仓库，路径是Username/Cangkuming，例如：SC-WSKun/HexoStaticFile，记下来后面配置PicGo要用

然后点开个人设置(不是仓库的设置)->develop setting->personal access tokens->generate new token

![image-20211015004017049](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211015004017049.png)

![image-20211015004042863](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211015004042863.png)

![image-20211015004142307](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211015004142307.png)

随便填写个Note，勾选repo下的所有选项，然后记录好生成的token

![image-20211015004242357](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211015004242357.png)

# 下载并配置PicGo

> 参考链接https://github.com/Molunerfinn/PicGo/releases

参考下图配置

![image-20211015004701514](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211015004701514.png)

配置完应该就可以在Typora直接上传图片啦

