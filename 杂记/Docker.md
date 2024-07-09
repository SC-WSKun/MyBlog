---
title: Docker
date: 2021-12-22 18:12:03
tags: Docker
categories: Docker
---

# Docker介绍

> Docker是使用Go语言开发实现的，在操作系统层面的虚拟化技术。吧在容器的基础上，进行了进一步的封装，从文件系统、网络互联到进程隔离等等，极大的简化了容器的创建和维护。

## Docker VS 传统虚拟机

- 传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整的操作系统，在该系统上再运行所需应用进程；
- 容器内的应用进程直接运行与宿主的内核，容器内没有自己的内核，而且没有进行硬件虚拟，因此容器比传统虚拟机更加轻便

![image-20211222181630837](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211222181630837.png)

## Docker的优势

作为一种新兴的虚拟化方式，Docker具有以下优势：

- 更高效地利用系统资源
- 更快速的启动时间
- 一致的运行环境
- 持续交付和部署
- 更轻松的迁移
- 更轻松的维护和拓展

# Docker基本概念

## 镜像（Image）

我们都知道，操作系统分为内核和用户控件，对于Linux而言，内核启动后，会挂载root文件系统为其提供用户空间支持。而**Docker镜像就相当于是一个root文件系统**。

Docker镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。**镜像不包含任何动态数据，其内容在构建之后也不会被改变。**

## 容器（Container）

镜像跟容器的关系，就跟面向对象设计中的**类**和**实例**一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。

**容器的实质是进程**，当与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的命名空间。因此容器可以拥有自己的root文件系统、自己的网络配置、进程空间、用户ID空间。

容器内的进程是运行在一个隔离的环境里，使用起来就好像在一个独立于宿主的系统中一样，因为这种隔离的特性，很多人常常会混淆虚拟机跟容器

容器运行的时候，是以镜像作为基础层，在其上创建一个当前容器的存储层，我们可以称这个为容器运行时读写而准备的存储层为容器存储层。

容器存储层的生存周期和容器一样，容器消亡时，容器存储层也随之消亡。因此，任何保存于容器存储层的信息都会随容器删除而丢失。

</br>

按照Docker最佳实践的要求，容器不应该向存储层内写入任何数据，容器存储层要保持无状态化。所有的文件写入操作，都应该使用数据卷、或者绑定宿主目录，在这些位置的读写会跳过容器存储层，直接对宿主发生读写，性能和稳定性会更高。

数据卷的生存周期独立于容器，容器消亡，数据卷不会消亡。因此，使用数据卷后，容器删除或者重新运行之后，数据却不会丢失。

## Docker Registry

镜像构建后，如果我们想要在其他服务器上使用这个镜像，我们就需要一个集中的存储、分发镜像的服务，**Docker Registry**

一个 `Docker Registry`可以包含多个仓库`Repository`，每个仓库可以包含多个标签`tag`，每个标签对应一个镜像

通常一个仓库会包含同一个软件不同版本的镜像，而标签就常用于对应该软件的各个版本，比如：

![image-20211222195142275](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211222195142275.png)

仓库名经常以两段式路径形式出现，比如`jwilder/nginx-proxy`，前者往往意味着Docker Registry多用户环境下的用户名，后者往往是对应的软件名。具体取决于使用的Docker Registry的软件或服务。

# 镜像的使用

## 获取镜像

命令：

```shell
docker pull [options] [Docker Registry address[:port]/]RegistoryName[:tag]
```

- `options`：
  - --disable-content-trust：忽略镜像的校验（默认）
  - -a：拉取所有tag的镜像

- `Docker Registry address`：如果没有指定，则默认镜像地址为`docker.io`；

- `RegistoryName`：这里的格式为`<用户名>/<软件名>`如果没有指定，则默认用户名为`library`，即官方镜像。

例子：

```shell
docker pull ubuntu:18.04
```

这里其实等价于

```shell
docker pull docker.io/library/ubuntu:18.04
```

## 运行镜像

命令：

```shell
docker run -it --rm ubuntu:18.04 bash
```

- `-i`：表示交互式操作
- `-t`：表示终端
- `--rm`：表示容器退出后将其删除，默认情况下退出的容器不会立即删除
- `bash`：放在镜像名后面是命令，因为我们向打开交互式shell，所以使用bash

运行后输入：

```shell
cat /etc/os-release
```

可以看到：

```shell
NAME="Ubuntu"
VERSION="18.04.6 LTS (Bionic Beaver)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 18.04.6 LTS"
VERSION_ID="18.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=bionic
UBUNTU_CODENAME=bionic
```

输入exit退出容器：

```shell
exit
```

## 列出镜像

命令：

```shell
docker image ls
```

运行结果：

![image-20211225101621052](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211225101621052.png)

可以看到列表列出了：

- REPOSITORY
- TAG
- IMAGE ID：镜像的唯一标识，一个镜像可以有多个TAG
- CREATED 
- SIZE：镜像下载后的大小，官网上给的大小是压缩后的大小

### 虚悬镜像

有一些镜像可能因为官方镜像的维护，发布了新版本之后重新`docker pull`时，镜像名被转移到另一个`IMAGE ID`的镜像上，旧的镜像名称被取消，变成了`<none>`。这种镜像称为虚悬镜像，可以用下面的命令专门显示这类镜像：

```shell
docker image ls -f dangling=true
```

一般来说，虚悬镜像是可以随意删除的，可以用下面的命令删除：

```shell
docker image prune
```

### 中间层镜像

Docker为了加速镜像构建，重复利用资源，会生成中间层镜像，一般使用`docker image ls`只会看到顶层镜像，如果希望显示中间层镜像需要增加`-a`选项

命令：

```shell
docker image ls -a
```

### 部分镜像

- 根据仓库名列出镜像

  ```shell
  docker image ls ubuntu
  ```

- 列出指定的某个镜像

  ```shell
  docker image ls ubuntu:18.04
  ```

- 过滤器`--filter`或`-f`

  ```shell
  docker image ls --f since=ubuntu:18.04
  
  docker image ls --f before=ubuntu:18.04
  ```

  如果构建镜像的时候定义了`LABEL`，则也可以通过`LABEL`过滤

  ```shell
  docker image ls -f label=com.example.version=0.1
  ```

### 以特定格式显示

只列出`IMAGE ID`属性，可以使用`-q`选项：

```shell
docker image ls -q
```

如果希望自己重组列，可以使用Go的模板语法

```shell
docker image ls --format "{{.ID}}: {{.Repository}}"
```

如果想要显示标题行且等距显示，则：

```shell
docker image ls --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

## 查看镜像、容器、数据卷占用空间

命令：

```shell
docker system df
```

运行结果：

![image-20211225102250080](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211225102250080.png)

## 删除本地镜像

命令：

```shell
docker image rm [选项] <镜像1> [<镜像2>...]
```

也可以用指定ID来删除，ID有两种，

- `长ID`，直接`ls`出来的ID就是长ID，一般只有脚本会使用，人工输入比较麻烦

- `短ID`，人工删除一般使用短ID，取ID的前3个字符以上，足够区分于别的镜像就可以了

  ```shell
  docker image rm 111
  ```

  

也可以使用镜像名：

```shell
docker image rm ubuntu
```

如果想要最精确地删除镜像，可以使用镜像摘要来删除：

```shell
docker image ls --digests
docker image rm node@sha256:....
```

### Untagged和Deleted

`rm`指令对于**被其他标签引用，被容器依赖或者被其他镜像依赖的镜像**，只会删除这个镜像的标签（Untagged），而不会删除镜像（Deleted）。

### 使用`docker image ls`命令配合删除

命令：

```shell
docker image rm $(docker image ls -q -f before=ubuntu:18.04)
```

## 构建镜像

。。。

# 容器的使用

## 新建并启动容器

启动容器主要使用`docker run`命令，Docker在后台运行的标准操作包括：

>- 检查本地是否存在指定的镜像，不存在就从registry下载
>- 利用镜像创建并启动一个容器
>- 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
>- 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
>- 从地址池配置一个ip地址给容器
>- 执行用户指定的应用程序
>- 执行完毕后容器被终止

例子：

- 直接执行命令

  ```shell
  docker run ubuntu:18.04 /bin/echo 'Hello world'
  ```

- 启动终端

  ```shell
  docker run -t -i ubuntu:18.04 /bin/bash
  ```

## 启动已终止的容器

命令：

```
docker container start 容器名
```

## 后台运行容器

使用`-d`可以让容器在后台运行，使用后容器并不会把输出的结果打印到宿主机上：

```shell
docker run -d ubuntu:18.04 /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

容器运行多久，与运行的命令有关，与`-d`无关。

使用`-d`参数启动后会返回一个容器id，使用`docker container ls`可以查看容器信息，要获取容器的输出信息，可以通过`docker container logs`命令。

```shell
docker container logs [container ID or Names]
```

## 终止容器

命令：

```shell
docker container stop 容器名
```

对于Docker容器中指定的应用终结时，容器也会自动终止。

比如使用Docker打开了bash，用户通过`exit`指令或者`ctrl+D`退出终端时，容器也会立即终止

## 查看所有容器

命令：

```shell
docker container ls -a
```

- `-a`：包括终止的容器

## 重启容器

命令：

```shell
docker container restart 容器名
```

这个命令会将一个运行态的容器终止，然后再重新启动它

## 进入容器

当我们使用`-d`使容器进入后台后，又想要进入容器操作时，我们应该使用下面两种命令：

- docker attach

  ![image-20211225124842719](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211225124842719.png)

  **在这里输入exit会导致容器停止**

- docker exec（推荐）

  `exec`命令后面可以跟多个参数，比如：

  - `-i`：如果没有`-t`的情况下，界面不会出现Linux命令提示符，但是命令执行结果仍然可以返回
  - `-t`：配合`-i`使用可以看到Linux命令提示符

  **输入exit退出不会导致容器停止**

## 导出容器

命令：

```
docker export IMAGE_ID > packageName.tar
```

## 导入容器快照

命令：

```shell
car ubuntu.tar | docker import - test/ubuntu:v1.0
```

也可以使用URL或者某个目录来导入

```shell
docker import http://example.com/exampleimage.tgz example/imagerepo
```

## 删除容器

命令：

```shell
docker container rm 容器名
```

清除所有处于终止状态的容器：

```
docker container prune
```

# 访问仓库

## Docker Hub

Docker官方维护的公共仓库，大部分需求都可以通过在Docker Hub中下载镜像来实现

### 注册

在`https://hub.docker.com`注册账号

### 登录

使用`docker login`命令输入用户名和密码来登录Docker Hub

使用`docker logout`命令退出登录

### 查找镜像

命令：

```shell
docker search 关键字
```

搜索到镜像后可以使用`docker pull`来下载到本地

我们还可以使用`--filter=stars=N`参数来指定搜索仅显示收藏数量为N以上的镜像

### 镜像的分类

- 官方镜像：`OFFICIAL`的镜像，一般称为基础镜像或根镜像，往往使用单个单词作为名字
- 用户创建镜像：一般有用户名称前缀，可以通过`username/`来指定使用某个用户提供的镜像

### 推送镜像

登录后可以使用`docker push`命令将自己的镜像推送到Docker Hub

```
docker tag ubuntu:18.04 username/ubuntu:18.04
docker push username/ubuntu:18.04
```

`push`后使用`docker search username`就能看到自己的镜像啦

### 自动构建

自动构建允许用户通过Docker Hub指定跟踪一个目标网站上的项目，一旦项目发生新的提交或者创建了新的标签，Docker Hub会自动构建镜像并推送到Docker Hub中

配置步骤：

1. 登录Docker Hub
2. 点击右上角头像，在账号设置中关联目标网站
3. 在Docker Hub中新建或选择已有的仓库，在Builds选项卡中选择`configure Automated Builds`
4. 选取一个目标网站中的项目（需要含`Dockerfile`）和分支
5. 指定`Dockerfile`的位置，并保存

# Docker的数据管理

