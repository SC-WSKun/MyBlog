---
title: 高阶JS函数
date: 2023-03-15 14:12:51
tags:
categories:
---

# once()

只执行一次

```js
function once(fn){
	return function(...args){
        if(fn){
            const ret = fn.apply(this, args);
            fn = null;
            return ret;
        }
    }
}
```

# throttle()

节流

```js
function throttle(fn, time = 500){
    var timer;
	return function(...args){
        if(timer == null){
            fn.apply(this, args);
            setTimeout(()=>{
                timer = null;
            },time)
        }
    }
}
```

# debounced()

防抖

```js
function debounced(fn, dur = 100){
    var timer;
	return function(...args){
        clearTimeout(timer);
		timer = setTimeout(()=>{
            fn.apply(this, args);
        },dur)      
    }
}
```

# consumer()

任务队列储存事件，固定时间间隔触发

```js
function consumer(fn, time = 100){
    var tasks = [],
        timer;
    return function(...args){
        tasks.push(fn.bind(this, ...args));
        if(timer == null){
            timer = setInterval(()=>{
                tasks.shift().call(this);
                if(tasks.length <= 0){
                    clearInterval(timer);
                    timer = null;
                }
            },time)
        }
    }
}
```

# iterative()

迭代运行

```js
function iterative(fn){
	return function(...args){
        return args.reduce(fn.bind(this));
    }
}
```

# toggle()

循环执行actions算子

```js
function toggle(...actions){
    return function(...args){
        let action = actions.shift();
        actions.push(action);
        return action.apply(this, args);
    }
}
```

