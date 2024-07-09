---
title: uni-app支付参考
date: 2021-08-18 00:48:00
tags: [uni-app,微信小程序]
category: Web开发
---

# 使用 uni-app 开发微信小程序的微信支付

使用的是 Dcloud 官方给的 uni-pay 插件实现的跨平台统一支付

# 代码示例

## 前端

```
//生成订单，这是我自己定义的云函数，在数据库中生成一条订单记录并获取该记录的_id作为支付请求需要的订单号
uniCloud.callFunction({
	name: "order_discount_center",
	data: {action: "add_order", ... }
}).then(res => {
	console.log("order_id", res)//获取订单号
    //自己定义的云函数，调用了统一订单的接口获取订单信息
	uniCloud.callFunction({
		name: "order_discount_center",
		data: {
            action: "wx_pay",
			totalFee: parseInt(that.money) * 100,   //金额
			outTradeNo: res.result.id,              //订单号
			openid: uni.getStorageSync('wx_openid') //openid
		}
	}).then(res => {
		if (res.result.orderInfo) { //获得订单信息参数
		    return new Promise((resolve, reject) => {
                //使用uni.requestPayment发起支付请求
		        uni.requestPayment({ 
		            ...res.result.orderInfo,
		            complete() {
			            resolve(res.result.outTradeNo)
		            }
		        })
	        })
		} else {
			throw new Error(res.result.msg)
		}
	})
})
```

## 后端云函数(NodeJS)
```
//action:"wx_pay",获取统一订单

//引入uni-pay跟uni-config-center模块
const uniPay = require('uni-pay')					
const createConfig = require('uni-config-center')   //注意uni-config-center里的uni-pay的config.js需要配置

//从uni-config-center的uni-pay文件夹读取config配置
const uniPayConfig = createConfig({ 
	pluginId: 'uni-pay'
})

//读取上一步读取到的config配置中的wxConfigMp的配置
const { wxConfigMp } = uniPayConfig.requireFile('config.js')    

const {
	outTradeNo,
	totalFee,
	openid
} = event //读取参数

let uniPayInstance = uniPay.initWeixin(wxConfigMp) //uni-pay封装的初始化函数
let notifyUrl = ``  //回调函数的url
let tradeType = 'JSAPI'
let orderInfo
try {
	// 获取支付信息
	orderInfo = await uniPayInstance.getOrderInfo({
		openid, //小程序支付必须有openid
		outTradeNo, // 订单号
		totalFee,   //金额
		notifyUrl,  //回调函数url
		body: '',   //商品详情
		tradeType
		// attach: "%7B%22type%22%3A%22goods%22%7D"
	})
} catch (e) {
	console.log(e.message)
	return {
		code: -3,
		msg: '获取支付信息失败，请稍后再试'
	}
}
return {
	outTradeNo,
	orderInfo
}
```

# 问题汇总

1. 无法将输入源“/body/xml/totalFee”映射到目标字段“标价金额”中，此字段需要一个合法的 64 位有符号整数
   ```
   totalFee要求的数据类型是整形，检查是否传入了字符串，使用parseInt进行类型转换
   ```
2. connot find module 'uni-config-center'或'uni-pay'
   ```
   解决方法就是删掉模块重装，下载uni-pay示例项目直接把uni-config-center跟uni-pay模块复制过来也可以
   ```
