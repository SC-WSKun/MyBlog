---
title: Web性能权威指南笔记
date: 2021-08-22 20:22:41
tags: 计算机网络
category: 计算机网络
---

# 延迟与带宽

## 延迟构成

1. 传播延迟

   解决方案：缩短传送距离，更换传送介质

2. 传输延迟

   解决方案：增大带宽

3. 处理延迟

   解决方案：改善设备

4. 排队延迟

   解决方案：用塞预防

## 小 Tip

1. 本地路由器的缓冲区爆满

   由于路由器配备很大的入站缓冲区，为了降低丢包率。但是这样破坏了 TCP 的拥塞预防机制（窗口变大了），导致网络中产生较长且可变的延迟时间

2. CDN（内容分发网络）

   一种网络加速技术，通过将数据部署在全球各地，用户请求内容时，从最近的服务器加载内容，可以降低传播时间

3. 使用 traceroute 测量延迟

   traceroute 可以用来列出分组经过的路由结点以及每一跳的延迟。原理是发送跳数限制递增的分组，每个分组达到跳数限制时返回一个 ICMP 消息，根据消息可以推算延迟

# TCP

## 概念

TCP（Transmission Control Protocol，传输控制协议）负责在不可靠的传输信道上提供可靠的抽象层

## 经典 TCP 三次握手

1. A->SYN->B
2. B->SYN,ACK->A
3. A->ACK->B

   可以使用捎带确认的方式，在第三次握手的时候携带数据

## TCP 的拥塞预防和控制

1. 流量控制

   通过流量控制防止发送方发送过多的数据，给接收方时间处理。

   原理是通过互相发送 rwnd（received window 接收窗口），rwnd 代表还剩余的缓冲区空间大小

2. 慢启动

   窗口最大值取决于 rwnd 和 cwnd，rwnd 通过缓存确定大小，cwnd 通过慢启动——乘减加增来确定大小。为了减少初始启动时间，RFC9828 规定将初始拥塞窗口大小从 4 段（RFC2581）提升到 10 段

3. 拥塞预防

   AIMD（乘减加增）
   PRR（比例降速，新算法，Linux3.2+默认拥塞预防算法）

## 带宽延迟积（BDP）

带宽延迟积代表在途未确认状态的最大数据量

带宽延迟积会影响网络性能，由于窗口大小是 ACK 报文携带的，在超过未确认的最大数据量当时未收到报文前，发送方都必须等待。所以当延迟比较高的时候，会造成数据缺口。因此要让窗口大小能保证发送方在 ACK 返回前持续发送数据，所以这也是 RFC9828 提升初始窗口大小的原因

## 队首阻塞

因为 TCP 分组必须按顺序传送到接受端，如果有一个分组丢包了，后续分组都会保存在 TCP 缓冲区。只有分组全部到达应用层才能访问数据。而分组到达的时间造成的延迟变化称为抖动

## TCP 优化

1. 增大 TCP 的初始拥塞窗口
2. 禁用慢启动重启
3. 窗口缩放
4. TCP 快速打开（TFO，TCP Fast Open）

   在某些条件下，允许 TCP 在第一个 SYN 分组中发送应用程序数据。

## 小 Tip

1. 窗口缩放

   最初分配给通告窗口的大小是 16 位，限制了最大值，后来可以通过 TCP Window Scaling 选项把窗口字段左移十六位，从而增大到 1G。现在的主要平台都是默认启用该选项的

2. 慢启动重启 SSR（Slow-Start Restart）

   在连接一段时间空闲后重置连接的拥塞窗口为默认值，但是会对有突发空闲的长周期 TCP 连接产生影响，建议在服务器上禁用

   ```
   sysctl net.ipv4.tcp_slow_start_after_idle
   sysctl -w net.ipv4.tcp_slow_start_after_idle=0
   ```

3. 尽管 HTTP 并未规定要使用 TCP，但是现实中所有 HTTP 实现都使用 TCP

# UDP

UDP（User Datagram Protocol，用户数据报协议），这也是为什么 UDP 的分组称为数据报的原因。

有趣的是，因为 UDP 的特点是不可靠传输，所以 UDP 很多场合下被称为（Unreliable Datagram Protocol）。

UDP 的重点不在于引入了什么特性，而是在于忽略了什么特性。

## 数据报

数据报是不可靠的服务传输的分组，对节点间之前的数据交换和传输网络没有任何依赖。

## 小 Tip

DNS 是 UDP 的经典应用。

WebRTC（Web Real-Time Communication，Web 实时通信）是一种基于 UDP 实现的原生的语音和视频实时通信。正是 RTC 的出现，使 UDP 的重要传输机制地位得以突显，而且出现了浏览器 API。

## UDP的特点
不保证消息交付

不保证交付顺序

不跟踪连接状态

不需要拥塞控制

## NAT
为什么需要NAT呢，就是因为Ipv4地址即将耗尽，RFC1631建议启用这种IP重用方案

- NAT保留的三短IP地址
  >10.0.0.0~10.255.255.255
  >172.16.0.0~172.31.255.255
  >192.168.0.0~192.168.255.255
## NAT带来的问题
NAT本身就依赖于连接状态，因此转换器必须保留每个UDP流的状态，但是UDP本身却没有状态，导致了连接状态的不可预测性，最好的办法是引入一个双向keep-alive分组

NAT还有个问题，内部客户端不知道外网的IP地址，只知道内网IP地址，如果进行P2P通信，则必然失败，这就是NAT穿透问题。
## 穿透技术
- STUN
  
  STUN就是客户端向一个STUN服务器发送绑定请求，STUN服务器返回一个响应，告诉客户端你的IP地址和端口号
- TURN
  
  TURN就是双方都把数据传到TURN服务器，由TURN服务器负责处理转发，起到一个中介的作用

- ICE
  
  ICE是上面两者的结合，规定了一套方法：能直连就直连，必要时使用STUN，不行再用TURN

# TLS传输

   

# Wi-Fi

## Wi-Fi 是什么

Wi-Fi 是 Wi-Fi 联盟的注册商标，实践中也用来指称任何基于 IEEE802.11 标准的产品

## 以太网与无线局域网
以太网通常被叫做局域网标准，802.11标准族则被称为无线局域网标准。有趣的是，802.11无线标准是按以太网标准的扩展来设计的，但是以太网协议很大程度借鉴了ALOHAnet协议，而ALOHAnet协议是第一个关于无线网络的协议，所以相当于绕了个圈子。因此ALOHAnet协议影响了两者的调度通信的方式
- 以太网：CSMA、CSMA/CD
- Wi-Fi：CSMA/CA
- 以上都是概率访问模型，在轻负载网络中表现较好，一般要求信道负载必须小于10%。随着负载增长，冲突次数会迅速增加，比如当一群人开会连同一个Wifi摸鱼的时候，Wi-Fi网络就可能不稳定
## 测量和优化Wifi性能
- QoS策略能为一些需要低延迟的应用提供更好的服务质量，但是你的网络会跟附近的Wifi网络会争用共享的无线电资源
- 2.4GHz频段提供3个不重叠的20MHz无线电信道：1、6和11
- 当Wi-Fi网络重叠时，就会出现带宽争抢的现象，因为2.4GHz的信道较少，容易出现网络重叠的现象。所以采用5GHz的频段，可以拓宽频率范围，提高性能
- 使用自适应比特流来主动适应带宽变化，主要适用于视频和音频，客户端会根据带宽的变化，决定下一个视频块的比特率。
## 关于Wi-Fi性能
- Wi-Fi不保证用户的带宽和延迟时间
- Wi-Fi的信噪比不同，带宽也随之不同
- Wi-Fi的发射功率被限制在200mW以内
- Wi-Fi在2.4GHz和较新的5GHz频段中的频谱有限
- Wi-Fi信道分配决定了接入点信号会重叠
- Wi-Fi接入点与客户端争用同一个无线信道
### 小Tip
- 在802.11n前，Wi-Fi协议只支持同一时刻传输一个数据帧，必须得到链路层的确认才能继续发送。802.11n引入了“帧聚合”，从而支持同时发送和确认多个Wi-Fi数据帧
- Wi-Fi一般是有线局域网的扩展，而有线局域网可能又通过DSL、有线电视网或者光线连接到广域网。而广域网是不计流量的，所以对于下载大文件、升级软件、流式播放，Wi-Fi用户不需要去担心这个问题，而3G和4G用户需要。

# 移动网络

每一代无线技术都以其峰值频谱效率为标志，为了让用户更直观理解，这个效率会转换成数据传输效率

| 代   | 峰值数据速率 | 说明                             |
| ---- | ------------ | -------------------------------- |
| 1G   | 无数据       | 模拟系统                         |
| 2G   | Kbit/s       | 第一代数字系统                   |
| 3G   | Mbit/s       | 专用数字网络，与模拟系统并行部署 |
| 4G   | Gbit/s       | 数字及分组网络                   |

## 4G

### IMT-Advanced 的4G主要要求:

- 以IP分组交换网络为基础
- 与之前的无线标准兼容
- 移动客户端的速率达到100Mbit/s，静止时的速率达到Gbit/s以上
- 100ms控制面延迟，10ms用户面延迟
- 资源在用户间动态分配和共享
- 可变带宽分配，5~20Mhz

