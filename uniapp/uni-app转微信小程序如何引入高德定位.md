---
title: uni-app转微信小程序如何引入高德定位
date: 2021-09-08 09:31:01
tags: ["高德定位", "微信小程序", "uni-app"]
category: Web开发
---

# 为什么要用高德定位

uni-app 官方给的 API：uni.getlocation() 只能用于安卓 APP 平台，因此在微信小程序端无法使用。

因此有两种解决方案，一种是用微信的 sdk 引入微信官方给的微信定位服务，写一个条件编译；另一种是引入高德定位这种第三方定位服务。我选择后者。

# 使用方法

下载高德的 sdk，在需要使用高德定位的页面import
```
import amap from '../../common/amap-wx.130.js';
```
在高德应用申请一个微信小程序端使用的key，在初始化时填写进去，就可以使用高德位置服务了
```
this.amapPlugin = new amap.AMapWX({
	key: this.key
});
uni.showLoading({
	title: '获取信息中'
});
this.amapPlugin.getRegeo({
	success: (data) => {
		console.log(data)
		if (this.zhusanjiao.indexOf(data[0].regeocodeData.addressComponent.city)) {
			uni.showToast({
				title: "暂不提供除珠三角以外地区的服务"
			})
		}
		uni.hideLoading();
	},
	fail:(err)=>{
		uni.showToast({
			title:err
		})
		uni.hideLoading()
	}
});
```
需要注意的是，uni-app转微信小程序时要在manifest的微信小程序设置中勾选使用位置服务并填写使用原因，否则可能定位失败

在发布小程序体验版或正式上线时，需要对微信小程序进行跨域配置，否则无法使用高德服务。如何配置跨域请看我的另一篇博客，需要配置的request网址如下。
```
https://restapi.amap.com
```
