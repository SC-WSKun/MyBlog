---
title: OSPF
date: 2021-11-11 14:07:09
tags: 计算机网络
categories: 计算机网络
---

# OSPF

> OSPF是一种链路状态协议，为了解决静态路由无法适应规模较大的网络，无法动态响应网络变化

## LSA

>LSA描述了路由器接口的状态信息，例如接口的开销、连接的对象等

## LSDB

> 路由器将LSA存放在LSDB中，LSDB汇总了网络中路由器对于自己接口的描述
>
> LSDB包含全网拓扑的描述，有点像一份地图

## SPF计算

> 每台路由器都可以根据LSDB计算出一棵以自己为根的、无环的、拥有最短路径的树，接着根据结果将路由加载入路由表

## 区域（Area ID）

> OSPF Area用于表示一个OSPF的区域，每个区域用一个区域号（Area ID）来标识

## 路由ID（Router-ID）

> 用于在一个OSPF域中唯一地标识一台路由器，可以手动配置也可以自动配置

## OSPF应用

> 一般用于在核心交换机与汇聚交换机上运行OSPF，可以实现园区内的路由可达

## COST

> OSPF使用Cost（开销）作为路由的度量值

![image-20211021140718579](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211021140718579.png)

## OSPF协议报文类型

- Hello：周期性发送，用来发现和维护OSPF邻居关系
- Database Description：描述本地LSDB的摘要信息，用于两台设备进行数据库同步
- Link State Request：用于向对方请求所需要的LSA。设备只有在OSPF邻居双方成功交换DD报文后才会向对方发送LSR报文
- Link State Update：用于向对方发送其所需要的LSA
- Link State ACK：用来对收到的LSA进行确认

## OSPF三大表

### 邻居表

> - OSPF在传递链路状态信息前，需先建立OSPF邻居关系
> - OSPF的邻居关系靠Hello报文建立
> - OSPF邻居表显示了OSPF路由器之间的邻居状态

查看邻居表的命令：

```
display ospf peer
```

### LSDB表

>- LSDB会保存自己产生的及从邻居收到的LSA信息
>- Type标识LSA的类型，AdvRouter标识发送LSA的路由器

查看LSDB表的命令：

```
display ospf lsdb
```

### OSPF路由表

> - OSPF路由表与IP路由表是不同的表
> - OSPF路由表包含Destination、Cost和NextHop等指导转发的信息

查看OSPF路由表的命令：

```
display ospf routing
```

## OSPF建立邻接关系

- 建立邻居关系
- 协商主/从
- 交互LSDB信息
- 同步LSDB

![image-20211021122942499](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211021122942499.png)

## OSPF建立路由表

### OSPF网络类型

- Broadcast/BMA（广播式多路访问）：

  ![image-20211021125935522](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211021125935522.png)

- NBMA（非广播式多路访问）：

  ![image-20211021130138051](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211021130138051.png)

- P2MP（点到多点）：

  ![image-20211021131250248](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211021131250248.png)

- P2P（点对点）：

  ![image-20211021125959036](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211021125959036.png)

### 三种路由器身份

> 用于优化MA网络中OSPF邻接关系，解决LSA泛洪时产生的带宽浪费和设备资源损耗问题

- DR：指定路由器

- BDR：备用指定路由器

- DRother路由器

只允许DR、BDR与其他OSPF路由器建立邻接关系。DRother之间不会建立全毗邻的OSPF邻接关系，双方停滞在2-way状态

BDR会监控DR的状态，并在DR故障时接替位置

## OSPF多区域

> 解决单个OSPF区域导致的LSDB过于庞大路由计算困难以及拓扑变更时LSA全域泛洪和全网SPF重计算带来巨大负担的问题

- OSPF引入区域（Area）的概念，可以是OSPF支撑更大规模组网
- OSPF多区域的设计减小了LSA泛洪的范围
- 在区域边界可以做路由汇总，减小路由表规模
- 多区域提高了网络扩展性，有利于组件大规模的网络

## STUB区域

> 当一个OSPF区域处于整个自治系统边界，又不含其他路由协议时，就可以配置STUB区域

目的是减少OSPF的路由表规模。

STUB区域中的路由器会增加一条至ABR的默认路由条目，当ABR配置了完全末梢区域后，其他路由器的路由除了直连条目外只有这条默认路由，不会学习其他区域的路由条目。到其他区域的ABR通过ABR转发。  

##  NSSA区域

> 对STUB区域的扩充



## OSPF路由器类型

- 区域内路由器IR
- 区域边界路由器ABR
- 骨干路由器BR
- 自治系统边界路由器ASBR

![image-20211021140044999](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/image-20211021140044999.png)
