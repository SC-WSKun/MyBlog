---
title: webRTC
date: 2022-03-22 21:00:10
tags:
categories:
---

参考链接：https://webrtc.org.cn/20210208-rtp-tcp-udp/

# 实时传输协议（RTP）

在计算机网络这门课程中，我们学过两个网络传输协议：TCP和UDP。

TCP是可靠的网络传输协议，它的重传机制可以让它保证永远不会丢失任意数据包，但是在网络链接质量不好的情况下可能会带来高延迟。

UDP是不可靠的网络传输协议，如果在传输过程中一个视频帧丢失了，由于没有检查是否发生了传送的机制，所以唯一知道数据包没有到达的方法就是超时，这又导致延迟。

因此，实时媒体面临着：TCP太慢且阻塞，UDP丢帧且有延迟

实时传输协议（RTP）就解决了这个问题，RTP是“部分可靠”协议，他会比较数据包重发时间和距离该数据包使用的时间。如果数据包重发的时间（RTT）比距离该数据包使用的时间短，则重发，否则忽略这个帧。

> 需要注意的是，RTP并不能称为网络传输协议，因为网络传输协议不应该知道传输内容，但是前面已经假定了传输内容是媒体帧，所以RTP属于媒体传输协议。



# WebRTC

WebRTC是一个标准，它包含了一套标准的`WebRTC API`和一系列协议，共同构成了一个不需要中间服务器的通信系统。

媒体平面信令是在对等点之间的频带上进行的；安全实时传输协议 *Secure Real-time Transport Protocol* (SRTP)用于将媒体数据与用于监视与数据流相关的传输统计信息的 RTP 控制协议 *RTP Control Protocol* (RTCP)信息一起传输。DTLS 用于 SRTP 密钥和关联管理。



![img](https://static.sitestack.cn/projects/webrtc-book-cn/images/rcwr_0104.png)

## 信令

WebRTC有梯型结构和三角结构，其中一个非常重要的组成部分是**信令**

信令是用来获取另一个通信主机的位置和相关信息的协议，常见的有WebSocket、XHR等等。

WebRTC并没有指定必须使用某种信令协议来进行通信，你可以选择你自己想要的协议比如WebSocket或者XHR等等。

对于信令服务器的职能MDN文档中是这么描述的：`A signaling server's job is to serve as an intermediary to let two peers
find and establish a connection while minimizing exposure of potentially private information as much as possible. `

信令服务器不需要关注信令传输的内容（SDP），SDP只需要由对话双方的应用程序来传递给ICE的子系统处理。





## SDP

信令的用处是用来传输会话的各种参数，通常我们使用SDP来描述多媒体通信会话，SDP 本身不提供任何媒体流，只用于在不同端之间协商网络指标、媒体类型和其他相关属性。这组属性和参数称为会话配置文件。

## WebRTC API

- `MediaStream`

  `MediaStream` 是音频和/或视频的实际数据流的抽象表示。 它用作管理媒体流操作的句柄，例如显示媒体流的内容，对其进行记录或将其发送到远程对等方。 `MediaStream` 可以扩展为表示来自（远程流）或发送到（本地流）远程节点的流。

  `LocalMediaStream` 表示来自本地媒体捕获设备(例如，网络摄像头、麦克风等)的媒体流。要创建和使用本地流，web 应用程序必须通过 `getUserMedia()` 函数请求用户访问。应用程序指定它需要访问的媒体音频或视频的类型。浏览器接口中的设备选择器用作授予或拒绝访问的机制。一旦应用程序完成，它可以通过调用 `LocalMediaStream` 上的 `stop()` 函数来撤销自己的访问权限。

- `PeerConnection`

  `PeerConnection` 允许两个用户在浏览器之间直接通信。然后，它表示与远程对等点的关联，远程对等点通常是在远程端运行的同一JavaScript应用程序的另一个实例。一旦建立了对等连接，就可以将媒体流(与临时定义的 `MediaStream` 对象在本地关联)直接发送到远程浏览器。

  `PeerConnection` 机制将ICE协议（请参阅[ICE Candidate Exchanging](https://www.bookstack.cn/read/webrtc-book-cn/spilt.5.01_introduction.md)）与STUN 和 TURN 服务器一起使用，以**使基于UDP的媒体流穿越NAT盒和防火墙**。 **ICE允许浏览器发现有关部署它们的网络拓扑的足够信息，以找到最佳的可利用通信路径。** 使用ICE还提供了一种安全措施，因为它可以**防止不受信任的网页和应用程序将数据发送到不希望接收它们的主机**。

  - `PeerConnection(configuration)`
    - `configuration` 
      - `balance`
      - 

-  `DataChannel`

  `DataChannel` API旨在提供通用传输服务，允许Web浏览器以双向对等方式交换通用数据。

  通过 UDP 的 DTLS 和 ICE 的 SCTP 封装提供了NAT遍历解决方案以及机密性，源身份验证和完整性受保护的传输。此外，该解决方案允许数据传输与并行媒体传输平滑地互通，并且两者都可能共享一个传输层端口号。SCTP本地支持具有可靠或部分可靠传送模式的多个流，每个流实际上代表一个单向逻辑通道，提供顺序传送的概念。因为`DataChannel`是双向的，所以每个 `DataChannel` 都是由传入和传出SCTP流的捆绑组成的。

# WebRTC使用流程

1. 从本地设备（如麦克风、网络摄像头）创建一个 `MediaStream` 对象。
2. 从本地 `MediaStream` 获取 *URL Blob*
3. 使用获取的 *URL Blob* 进行本地预览
4. 创建一个 `RTCPeerConnection` 对象
5. 将本地流添加到新创建的连接
6. 将你自己的会话描述发送到远程对等点  Send your own session description to the remote peer.
7. 从您的对等方接收远程会话描述  Receive the remote session description from your peer.
8. 处理收到的会话描述，并将远程流添加到您的 `RTCPeerConnection`
9. 从远程流获取 *URL Blob*
10. 使用获取的 *URL Blob* 播放远程对等方的音频和/或视频

# WebRTC的传输优势

对比mjpg-streamer：

- WebRTC支持的视频编解码器：

  ![1680010972904](C:\Users\flyingfeather\AppData\Roaming\Typora\typora-user-images\1680010972904.png)

  对比mjpg等老式解码器，H264、VP8等需要的带宽更少，传输更稳定。因为mjpg本质上是一个个割裂的jpg完整图像，只做到帧内压缩没考虑帧间压缩，没有考虑时空上的相关性，所以每次都要传输一个完整的图像，而H264除了帧内压缩还有帧间压缩，提高了传输的性能。

- 在mjpg-streamer的官网有安全提示：

  >mjpg-streamer should not be used on untrusted networks!
  >By default, anyone with access to the network that mjpg-streamer is running on will be able to access it.

  所以mjpg-streamer更多用于局域网内视频传输





# WebRTC的劣势

WebRTC把很多事情交由应用程序处理，对应用程序提出了较高的挑战。

比如视频会议：

>视频会议系统通常依赖于星形拓扑，其中每个对等方都连接到专用服务器（多点控制单元，MCU），该服务器同时负责：
>
>- 与网络中的所有其他对等方协商参数
>- 控制会议资源
>- 汇总（或混合）各个流
>- 向参加会议的每个对等方分配适当的混合流
>
>WebRTC API 没有提供任何特定的机制来辅助会议方案。 识别 MCU 的标准和过程委托给应用程序。

- 