---
title: unicloud增删查改参考
date: 2021-08-18 00:47:27
tags: uni-app
category: Web开发
---

# 增删查改操作指南

## 增

1. 使用云函数 add（）在数据库里增加新的用户，并作校验检查是否原来已经注册，是否提交了必须的数据

```
//为了方便,下面的代码统一用db、dbCmd、testdb、testdata简写
const db = uniCloud.database()
const dbCmd = db.command()
const testdb = db.collection('testdb')
const {testdata} = event;//解析对象写法

if(testdata==""}{
    return "缺少关键数据"
}else{
    result = await testdb.where.({_id:testdata}).get()
    if(result._id==""){
        testdb.add({...})
    }else{
        //这里可以选择直接更新，或者返回一个状态询问是否更新
        testdb.update({...})
    }
}
```

2. 使用模态弹窗防止用户多次触发添加操作（模态，指是否能点击窗口外的地方）

```
//前端代码
uni.showLoading({ //打开模态加载弹窗
    title:"",
    mask:true//是否开启遮罩
)}
uni.hideLoading(); //关闭加载弹窗
```

3. 如果需要存储图片使用 uniCloud.uploadFile（），在客户端上传

```
//前端代码
cosnt that = this;
uni.chooseImage({ //选择图片
	count: 1, //上传数量
	sizeType: ['compressed'],
	sourceType: ['album'],
	success: (res) => {
	    that.idcard_path = res.tempFilePaths[0]
	    uniCloud.uploadFile({//上传图片到云端
			filePath: that.idcard_path,//本地路径
			cloudPath: '', //自定义云端路径
			fileType: "image", //上传类型
            //实时上传进度弹窗显示
			onUploadProgress: function(progressEvent) {
			    var percentCompleted = Math.round((progressEvent.loaded * 100) / progressEvent.total);
				uni.showToast({
					title: percentCompleted + "%",
					icon: "loading"
				})
			},
			success: (res1) => {
				console.log("存储成功", res1)
				uniCloud.getTempFileURL({//获取http下载链接
					fileList: [res1.fileID]
				}).then(res => {
					console.log("http连接",res.fileList[0].download_url)
				})
			},
			fail: (res) => {
				console.log("存储失败", res)
			}
		})
	}
})
```

## 删

1. 使用 remove()函数删除云端数据

```
//云函数代码
testdb.where({_id:testdata}).remove()
```

2. 使用选择弹窗让用户进行二次确认再删除

```
//前端代码
uni.showModal({
    content:"",//弹窗内容
    success:(res)=>{
        console.log(res.confirm); //点击确定会返回true
    }
})
```

3. 如果该用户有云存储数据记得使用 uniCloud.deleteFile（）删除，注意这个删除操作要写在云函数里

## 查

1. 使用 get（）函数查找数据库数据，配合 field（），where（）筛选数据

```
//云函数代码
testdb.where({_id:testdata}).field({data1:true}).get()
```

2.  使用 limit（），skip（）控制返回数据的数量与起点（有换页的时候要用到，比如 Web 换页控件或无限滚动列表，这里以 limit 为 10 举例）

```
//云函数代码
    let p = await testdb.skip(Number(event.skip)*10)//返回起点
                    .limit(10)//返回数量
                    .get()
                    .then(res => {
			            console.log(res)
			            return res
                    })
    return p;

//前端代码
    change_page(e) {//e.current为返回页面组件的页号
	    console.log(e.current)
	    let that = this
	    uniCloud.callFunction({
	    	name: "test_function",
	    		data: {
	    		action: "get_data",
	    		skip: (e.current - 1)
	    	    }
	    }).then(res => {
	    	console.log(res.result.data)
	    })
    }
```

3. 多属性搜索（搜索框可以使用，如果要支持模糊搜索请自己写正则表达式，这里是实现了\_id、username、mobile 三个属性的搜索合并，但可能重复）

```
//云函数代码
const {
    keyword
} = event;
let temp1 = await testdb.where({
	_id:keyword
}).get()
let temp2 = await testdb.where({
	username:keyword
}).get()
let temp3 = await testdb.where({
	mobile:keyword
}).get()
//使用了拓展运算符的写法，将数组合并
let result = [...temp1.data,...temp2.data,...temp3.data]
return result;
```

## 改

1. 使用 update（）函数更新数据

```
//云函数
const {//使用了解析对象的写法，相当于 const _id = event._id ...
    _id,
	mobile,
	username,
    ...
} = event;
testdb.where({
	_id: _id
}).update({
	mobile: dbCmd.set(mobile),
	username: dbCmd.set(username),
	...
})
```

2. 更新前对新数据进行校验
