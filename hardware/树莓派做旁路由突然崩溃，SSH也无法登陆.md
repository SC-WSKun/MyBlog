---
title: 树莓派做旁路由突然崩溃，SSH也无法登陆
date: 2021-07-16 16:19:40
tags: [树莓派, 计算机网络]
---

今天突然宿舍的 wifi 断了，看了下 wifi 的指示灯是红色的。

### 首先排除故障原因：

1. ping 一下网关，看看 wifi 有没有问题
2. 把 wifi 网线取下来，让树莓派直连电脑。ping 一下树莓派，看看树莓派有没有问题，笔者到这一步就卡住了，所以是树莓派的问题
3. 由于树莓派 ping 不通，那肯定也不能 SSH 登录。
4. 考虑了下可能是电脑的 ip 配置问题。
5. 打开控制面板，网络连接状态，在以太网里查看详细信息，果然是 ipv4 地址不知道为啥变成了一个 169 开头的 IP。

### 解决方法：

大概率是树莓派的 DHCP 出了问题

1. 重启解决一切问题，重启后看下自己的 ipv4 地址是否变成内网地址，一般这一步就够了
2. 如果一直不能自动配置，则在网络的属性里双击 ipv4 协议，手动设置 ip 为子网中除了网关跟树莓派之外的一个 ip，比如 192.168.1.3，子网掩码为 255.255.255.0，网关设置为树莓派的登录 IP（此时还是直连）
3. 设置完后应该就能用 SSH 登录上树莓派了
4. 进入图形界面的 DHCP/DNS，在高级设置里面找到 DNS 缓存改为 0 再改回来，手动释放缓存
5. 重新配置 iptable 跟运行 xray，可以看到网络又回来了
6. 把电脑的 Ipv4 重新改回 DHCP，再看看是否变成内网地址
