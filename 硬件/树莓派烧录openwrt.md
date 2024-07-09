---
title: 树莓派烧录openwrt
date: 2021-09-25 10:20:44
tags: 树莓派
---
# 前期准备
树莓派，一张SD卡，SD卡读卡器
# 使用工具
Win32DiskImager

# 流程
1. 首先插入SD卡，电脑上能读取到硬盘插入
2. `win+R`调出运行，输入`diskpart`
3. 输入`list disk`查看硬盘，一般容量比较小的那个是你的SD卡，记住它前面的编号
4. 选择硬盘，输入`select disk 硬盘编号（比如：2）`
5. 使用`clean`清空SD卡
6. 使用`create partition primary`创建分区
7. 在我的电脑里右键这个盘，点击格式化
8. 打开Win32DiskImager，将之前下载好的openwrt的img文件烧录进SD卡
9. 将SD卡插入到树莓派，启动成功