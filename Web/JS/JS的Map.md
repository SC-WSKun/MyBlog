---
title: JS的Map
date: 2021-12-11 10:37:15
tags: JavaScript
categories: JavaScript
---

# 创建Map对象

```js
var map = new Map();
```

# 将键值对放入对象

```js
map.set("key1",value)
```

# 根据key获取map值

```js
map.get(key)
```

# 删除map指定对象

```js
delete map[key]
或者
map.delete(key)
```

# 循环遍历map

```js
map.forEach(key => {
	console.log("key",key)
})
```

