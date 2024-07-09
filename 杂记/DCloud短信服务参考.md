---
title: DCloud短信服务参考
date: 2021-08-18 00:47:44
tags: uni-app
category: Web开发
---
# 短信服务解决方案
开发APP时经常需要使用到发送短信来实现验证码登录，DCloud官方提供了一套短信服务的API，以下是参考代码
```
//前端代码
send_message(index) {//数组的索引
	const that = this;
	uniCloud.callFunction({
		name: "owner-center",
		data: {
			action: "send_owner_message",
			mobile: [that.issue[index].mobile],//云函数兼容批量发短信                                                                            所以用了数组
			templateId: 'uni_sms_test'//测试用模板ID
		}
	}).then(res => {
		console.log("success send message")
	})
}

//云函数代码
const {
	mobile,
	templateId
} = event;
try {
	let success = true;
	for (let i = 0; i < mobile.length; i++) {  //批量发送，所以用了for循环 
        let code =  JSON.stringify(Math.random() % 1000000)//随机生成数字
		let res = await uniCloud.sendSms({
			appid: '...',
			smsKey: '...',
			smsSecret: '...',
			phone: mobile[i],
			templateId: templateId,
			data: {
				code: code
			}
		})
		console.log(res)
		// 调用成功，请注意这时不代表发送成功
		if (res.code != 0) success = false;
	}
	if (success) return "success";
	else {
		return "false"
	}
} catch (err) {
	// 调用失败
	console.log(err.errCode)
	console.log(err.errMsg)
	return {
		code: err.errCode,
		msg: err.errMsg
	}
}

```

