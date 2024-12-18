---
title: GPIO
date: 2024-12-06 22:37:25
tags: GPIO
categories: Hardware
---

# GPIO 简介

GPIO(General Purpose Input Output)通用输入输出，是一种通用的引脚，可以作为输入和输出，可以设置电平，也可以设置方向。

Linux 提供了 GPIO 子系统驱动框架，使用该驱动框架即可灵活地控制板子上的 GPIO。

# GPIO 命名

1. RockChip（瑞芯微）
   - Rockchip Pin 的 ID 按照 控制器(bank)+端口(port)+索引序号(pin) 组成。
   - 控制器和 GPIO 控制器数量⼀致
   - 端口固定 A、B、C 和 D，每个端口仅有 8 个索引号,(a=0,b=1,c=2,d=3)
   - 索引序号固定 0、1、2、3、4、5、6、7

> GPIO1_A4 表达的意思为第 1 组控制器，端口号为 A，索引号为 4。该引脚号的计算公式为 32 x 1 + 0 x 8 + 4 = 36

# GPIO 库

在 linux 下，常用的库是 libgpiod，可以参考[libgpiod](https://git.kernel.org/pub/scm/libs/libgpiod/libgpiod.git/about/)。

可以使用下面的命令安装 gpiod

```bash
#安装gpiod 命令行工具
sudo apt install gpiod
```

gpiod 配套了几个工具，可以帮助我们更好地操控 gpio

| 命令       | 作用                       | 使用举例           | 说明                                |
| ---------- | -------------------------- | ------------------ | ----------------------------------- |
| gpiodetect | 列出所有的 GPIO 控制器     | gpiodetect(无参数) | 列出所有的 GPIO 控制器              |
| gpioinfo   | 列出 gpio 控制器的引脚情况 | gpioinfo 1         | 列出第一组控制器引脚组情况          |
| gpioset    | 设置 gpio                  | gpioset 1 20=0     | 设置第一组控制器编号 4 引脚为低电平 |
| gpioget    | 获取 gpio 引脚状态         | gpioget 1 20       | 获取第一组控制器编号 4 的引脚状态   |
| gpiomon    | 监控 gpio 的状态           | gpiomon 1 20       | 监控第一组控制器编号 4 的引脚状态   |
