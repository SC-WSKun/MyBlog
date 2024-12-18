---
title: 树莓派+openwrt制作路由
date: 2021-09-25 10:41:30
tags: 树莓派
---
# 前期准备
- 烧录好openwrt的树莓派（没烧录好可以看我另一篇博客）
- 一个usb的网卡拓展坞，因为树莓派只有一个网口，一个网口无法拿来做路由
# 流程1 安装usb网卡驱动
1. 首先用网线连接树莓派跟电脑
```
因为如果没有usb的网卡驱动树莓派无法识别出网口，所以我们要下载usb网卡驱动，这时需要树莓派连上网络，这里我选择连接无线网络
```
2. 在浏览器输入192.168.1.1，进入openwrt的GUI界面，默认没有密码，可以直接登录
3. 选择Network->Wireless
4. 点击上面radio的scan，可以找到附近的无线AP，记下你要连接的网络的BSSID，然后点击Join Network
5. 在WPA passphrase输入你的wifi密码，然后其他可以保持默认，点submit
6. 这里进入配置页面，将之前赋值的BSSID输进去，其他保持默认不要修改，然后save
7. 再点击save&apply,这时应该连接上无线AP了
8. 用putty或者ssh登录树莓派，默认IP为192.168.1.1，默认输入root，没有密码
9. 首先输入`opkg update`，能正常download且最后出现`check passed`说明你的无线网络连接成功
10. 查看usb网卡驱动，如果能找到买的扩展坞的网卡型号可以跳过这一步
    ```
    下载usbuitls，使用lsusb来管理usb：
        opkg install usbutils
        lsusb
    这时应该可以看到usb后面的ID，比如博主的ID是0bda:8152，百度搜索就能找到型号是RTL8152
    ```
11. 在浏览器的GUI上点击System->software，在搜索框里输入你的芯片型号，找到对应驱动install，等待下载完毕，点击dismiss退出
12. 点击network->interface，点击Add new interface，这时device里应该能识别出新的网口，eth0是原生网口，eth1应该是你的usb网口，然后绑定eth1，给你的这个wan起个名字
13. 然后配置根据自己的需求来定，像我们学校的ipv6可以用DHCP，ipv4只能用校园网分配的静态IP，设置完后sava&apply

---
# 流程2 配置网络
1. putty连接树莓派，开启网络转发，执行后出现 `net.ipv4.ip_forward = 1` 即成功
   ```
   echo net.ipv4.ip_forward = 1 >> /etc/sysctl.conf && sysctl -p
   ```
2. 安装v2ray
3. 编写客户端config.json，放在xray的同目录下
4. 下载iptables的tproxy模块跟extra模块
   ```
   opkg install sudo iptables-mod-tproxy iptables-mod-extra
   ```
5. 配置iptable
   ```
    ip rule add fwmark 1 table 100
    ip route add local 0.0.0.0/0 dev lo table 100
    iptables -t mangle -N XRAY
    iptables -t mangle -A XRAY -d 127.0.0.1/8 -j RETURN
    iptables -t mangle -A XRAY -d 192.168.1.2/24 -j RETURN
    iptables -t mangle -A XRAY -d 224.0.0.0/3 -j RETURN
    iptables -t mangle -A XRAY -p tcp -j TPROXY --on-port 4434 --tproxy-mark 1
    iptables -t mangle -A XRAY -p udp -j TPROXY --on-port 4434 --tproxy-mark 1
    iptables -t mangle -A PREROUTING -j XRAY
   ```
6. cd到xray目录下执行
   ```
   ./xray -config=config.json &
   ```