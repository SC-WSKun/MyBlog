---
title: Cocos无法获取AudioSource播放进度
date: 2022-06-26 12:52:25
tags: Cocos
categories: Cocos
---

今天在写音频控制的时候，需要添加一个音乐播放进度条，但是在使用`playOneShot`函数播放音乐后发现，`console.log`出来的`duration`和`currentTime`都是0，但是如果我在编辑器给`AudioSource`一开始就绑定一个音源时，又能打印出来。

# 原因

`playOneShot`是Cocos用来播放短时间音效的函数，所以当使用这个函数时，不会影响`AudioSource`绑定的音源，因为我默认音源为空，导致打印出来的都是0。

# 解决方案

通过更改`AudioSource`的`clip`属性和使用`play`函数来播放长音频

```typescript
@property(AudioSource)
_audioSource: AudioSource = null;

play(myClip:AudioClip){
    _audioSource.clip = myClip;
	_audioSource.play();
}
```

