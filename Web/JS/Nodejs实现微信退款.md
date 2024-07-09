---
title: Nodejs实现微信退款
date: 2021-09-02 11:23:29
tags: Nodejs
---

```
参考链接：https://blog.csdn.net/weixin_39907636/article/details/83655295
```
# 背景
用uni-app开发一个软件需要用到微信退款功能，但是DCloud官方给的API：uni.request只能在客户端调用，为保障安全性，应该把证书还有一些其他配置保存在服务端，所以只能放弃使用官方API的方案

# Nodejs 代码

引入模块：

```
const fs = require('fs') //用于读取证书
const request = require('request') //用于发送POST请求
const crypto = require('crypto'); //用于生成签名
```

随机字符串生成函数：

```
let randomString = function() { //自己随便写的，只有数字，伪随机字符串可以参考另外一篇博客
	let temp = ''
	for (let i = 0; i < 22; i++) {
		temp += parseInt(Math.random() * 9)
	}
	console.log(temp)
	return temp;
}
```

sha256 加密函数：

```
function sha256(message, secret = '', encoding) {
	const hmac = crypto.createHmac('sha256', secret)
	return hmac.update(message).digest(encoding)
}
```

签名算法部分：

```
const appid = 'appid'
const mch_id = 'mch_id'
const nonce_str = randomString()
const sign_type = 'HMAC-SHA256'
const out_refund_no = result.id
const refund_fee = ""
const total_fee = ""
const stringA = "appid=" + appid +
                "&mch_id=" + mch_id +
                "&nonce_str=" + nonce_str +
                "&out_refund_no=" + out_refund_no +
                "&out_trade_no=" + out_trade_no +
                "&refund_fee=" +refund_fee +
                "&sign_type=" + sign_type +
                "&total_fee=" + total_fee
const key = 'mykey'
const stringSignTemp = stringA + "&key=" + key
const sign = sha256(stringSignTemp, key, 'hex').toUpperCase()
console.log("sign", sign)
```

构造 XML 数据：

```
let data = '<xml>' + '<appid>' + appid + '</appid>' +
	'<mch_id>' + mch_id + '</mch_id>' +
	'<nonce_str>' + nonce_str + '</nonce_str>' +
	'<out_refund_no>' + out_refund_no + '</out_refund_no>' +
	'<out_trade_no>' + out_trade_no + '</out_trade_no>' +
	'<refund_fee>' + refund_fee + '</refund_fee>' +
	'<sign_type>'+sign_type+'</sign_type>'+
	'<total_fee>' + total_fee + '</total_fee>' +
	'<sign>' + sign + '</sign>' +
	'</xml>'
```

发送 POST 请求：

```
request({
	url: 'https://api.mch.weixin.qq.com/secapi/pay/refund',
	agentOptions: {
		pfx: fs.readFileSync(__dirname + '/apiclient_cert.p12'),//这里把p12证书放置在index.js同级目录下，请自行对应路径
		passphrase: "mch_id"
	},
	method: 'POST',
	body: data
}, function(err,response,body) {
	console.log("err", err)
	console.log("response", response)
	console.log("body", body)//微信退款API返回的XML数据
});
```

# 常见报错

1. ![CDATA[0 参数格式错误]]
   ```
   退款金额不能为零，请检查退款金额
   ```
2. 订单金额要与付款时一致
