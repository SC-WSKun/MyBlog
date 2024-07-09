---
title: 获取摄像头视频流，实现录制与存储功能Demo
date: 2022-09-08 09:45:28
tags:
categories:
---

# 项目介绍

这是一个关于WebRTC的Demo，实现了获取摄像头的视频流，并且能够录制视频并保存下来，实现播放。

最终目的是实现获取监控视频流，并实现录制并上传到服务器。

# 技术栈

- React

# 兼容性

目前测试成功的平台有

- fireFox in PC
- safari in IOS

# 代码仓库

https://github.com/SC-WSKun/RTCDemo

# 代码解读

这个项目的核心在于两个API：`getUserMedia`和`MediaRecorder`

- `navigator.getUserMedia`（旧）或`MediaDevices.getUserMedia`（新）：

  **MediaDevices.getUserMedia()** 会提示用户给予使用媒体输入的许可，媒体输入会产生一个[`MediaStream`](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStream)，里面包含了请求的媒体类型的轨道。此流可以包含一个视频轨道（来自硬件或者虚拟视频源，比如相机、视频采集设备和屏幕共享服务等等）、一个音频轨道（同样来自硬件或虚拟音频源，比如麦克风、A/D 转换器等等），也可能是其它轨道类型。

  它返回一个 [`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise) 对象，成功后会`resolve`回调一个 [`MediaStream`](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStream) 对象。若用户拒绝了使用权限，或者需要的媒体源不可用，`promise`会`reject`回调一个 `PermissionDeniedError` 或者 `NotFoundError` 。

  参考链接：https://developer.mozilla.org/zh-CN/docs/Web/API/MediaDevices/getUserMedia

- `MediaRecorder`

  **MediaRecorder** 是 [MediaStream Recording API](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStream_Recording_API) 提供的用来进行媒体轻松录制的接口，使用`start()`和`stop()`控制录制开始和结束。搭配`start`、`stop`和`ondataavailable`的监听事件可以实现获取录制视频并进行处理的功能。

  参考链接：https://developer.mozilla.org/zh-CN/docs/Web/API/MediaRecorder

然后是一些小坑：

- video标签的`src`属性绑定音视频，`srcObject`属性是绑定音视频流，不能混淆
- safari貌似不支持webm类型的Blob视频，换成mp4即可播放