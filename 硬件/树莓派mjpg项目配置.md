---
title: 树莓派mjpg项目配置
date: 2023-03-29 19:08:03
tags: [树莓派, 嵌入式]
categories: 嵌入式开发
---

# 环境

设备：

- 树莓派3B+
- CSI摄像头

操作系统：

- Raspberry Pi OS

# 配置流程

1. 安装库

   ```shell
   sudo apt-get update
   sudo apt-get install subversion
   sudo apt-get install libjpeg8-dev
   sudo apt-get isntall imagemagick
   sudo apt-get isntall libv4l-dev
   sudo apt-get install cmake
   sudo apt-get install git
   ```

2. 开启摄像头

   ```shell
   sudo raspi-config
   ```

   找到`interface options`选择`camera(Debian10)`或者`legacy camera(Debain 11)`，选择`yes`开启即可

3. 克隆项目

   ```shell
   git clone https://github.com/jacksonliam/mjpg-streamer.git
   ```

4. 配置项目

   ```shell
   cd mjpg-streamer/mjpg-streamer-experimental
   make
   sudo make install
   ```

5. 启动项目

   ```shell
   ./mjpg_streamer -i "./input_uvc.so -f <帧数> -r <分辨率>" -o "./output_http.so -w ./www"
   ```

6. 在自己的浏览器上查看效果

   输入网址`http://<自己的树莓派ip>:8080/stream.html`

# 可能出现的问题

如果遇到项目启动不成功，要先查看摄像头排线是否插好（博主本人搞了一晚上就是因为排线问题）

检查摄像头能否使用可以用以下命令：

```shell
raspistill -o new.jpg
```

然后查看生成的`new.jpg`是否正常，如果出现绿色线条或者绿色色调，基本上是排线问题，重新检查一次（记得两头都要）

