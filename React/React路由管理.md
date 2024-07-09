---
title: React路由管理
date: 2022-01-03 14:09:55
tags: React
categories: React
---

# React Router v6

## 安装 react-router-dom

使用npm安装

```shell
npm install --save react-router-dom
```



## 路由配置（History Route）

需要在`index.js`中引入`BrowserRouter, Routes, Route, createBrowserHistory`

```jsx
import {BrowserRouter, Routes, Route} from 'react-router-dom'
import {createBrowserHistory} from 'history'

render(
    <React.StrictMode>
        <BrowserRouter location={customHistory.location} navigator={customHistory}>
            <Routes>
                <Route exact path="/" element={<App />}></Route>
            </Routes>
        </BrowserRouter>
    </React.StrictMode>,
    document.getElementById('root')
)
```

## Link跳转传参

新版`react-router-dom`不支持类组件在`props`中获取参数了，所以建议使用函数式组件，需要做如下操作。

- 在`<Route>`的`path`中使用参数表示方式`path="/test/:prop"`

- 在`<Link>`的`to`中使用`/path/参数`

- 在函数组件中使用`useParam()`hook来获取传递的参数

示例代码：

```react
//这里代表传递的参数名为theme
<Route path="/food/:theme" element={<MyElement />}></Route>

//这里传递的参数是dark
<Link to={"/food/" + "dark"}>家乡美食</Link>
  
function MyElement(){
    const param = useParams();
    console.log(param);
}


```



# 路由原理

> 参考链接：
>
> https://juejin.cn/post/6844903609411305479
>
> https://www.cnblogs.com/tugenhua0707/p/10859214.html

## hash、history

前端的路由其实是受到后端路由的启发，我们访问一个网站就是通过发送一个请求到服务器，服务器通过解析`url`，返回对应路径下的资源。当我们切换页面，修改了`url`的时候，浏览器会向服务器发送新的请求，访问对应路径下的文件。

但是这样一来，每次切换页面都要发送请求，就显得不够高效。所以我们寻求一种，不需要发送请求的切换页面的方法。因此前端路由应运而生。

要理解React的路由原理，首先得先了解下两种前端路由模式：

**hash模式：**

> hash模式的url是这样的：`http://www.abc.com/#/xx`
>
> url会带一个`#`号，`#`号后面就是`hash值`，改变后面的`hash值`，它不会向服务器发出请求，所以不会刷新页面，并且每次`hash值`发生变化的时候，会触发`hashchange`事件。因此我们可以通过监听该事件，来知道`hash值`发生了哪些变化。

示例代码：

```js
function handleHashUpdate(){
	//...
}

window.addEventListener('hashchange',handleHashUpdate);
```

{% note info %}

为什么改变hash页面不会跳转呢？因为hash链接是当前页面中的某个片段，我们经常使用`hash`来滚动到页面的指定位置，我们称为`锚点`。但是页面中如果不存在`hash`对应的片段，则没有任何效果。

{% endnote %}

那么我们如何去改变hash呢？有两种方式：

- 使用`a`标签，设置`href`属性，比如：`<a href='#/test1'>测试hash1</a>`
- 通过`js`直接赋值给`location.hash`也会改变`url`，触发`hashchange`事件。

下面是一个简单的hash路由实现：

index.html：

 ```html
 <body>
     <ul>
         <li><a href='#/'>主页</a></li>
         <li><a href='#/a'>a页</a></li>
         <li><a href='#/b'>b页</a></li>
     </ul>
 </body>
 ```

hash.js：

```js
/* 路由管理类 */
class HashRouter{
    constructor(){
        this.routes = {};
        this.currentHash = '';
        const hashChangeUrl = this.hashChangeUrl.bind(this);
        window.addEventListener('load',hashChangeUrl,false);
        window.addEventListener('hashchange',hashChangeUrl,false);
    }
    
    route(path, callback){
        /* 将回调函数跟对应path绑定。*/
        this.routes[path] = callback || function(){};
    }
    
    hashChangeUrl(){
        /* location.hash获取到的值的形式为: '#/a' */
        this.currentHash = location.hash.slice(1) || '/';
        /* 执行当前hash对应的回调函数 */
        this.routes[this.currentHash]();
    }
}

const Router = new HashRouter();
/* 注册函数 */
Router.route('/',()=>{
    console.log('我是主页')
})
Router.route('/a',()=>{
    console.log('我是a页')
})
Router.route('/b',()=>{
    console.log('我是b页')
})
```

{% note warning %}

注意点：

- `hash`只能修改`url`的片段标识符的部分，并且必须从`#`号开始。
- `hash`必须和原型的值不同，才能新增会话浏览历史的记录

{% endnote %}



---

**history模式：**

> window对象中的`onpopstate`事件用来监听历史栈的改变，只要历史栈有信息发生改变的话，就会触发该事件。

示例代码：

```js
window.addEventListener('popstate',function(e){
	//...
})
```

而`history`提供了两个操作历史栈的API：

- history.pushState( )：在历史记录中追加一条记录
- history.replaceState( )：替换当前页在历史记录中的信息

这两个方法也可以改变`url`，且不会导致页面刷新。

下面是一个简单的`history`路由：

```js
class HistoryRoutes{
	constructor(){
		this.routes = {};
		window.addEventListener('popstate',(e)=>{
            const path = this.getstate();
            this.routes[path] && this.routes[path]()
        })
	}
    
    // 获取路由路径
    getState(){
        const path = window.location.pathname;
        return path ? path : '/';
    }
    
    route(path, callback){
        this.routes[path] = callback || function(){};
    }
    
    init(path){
        history.relaceState(null, null, path);
        this.routes[path] && this.routes[path]();
    }
    
    go(path){
        history.pushState(null, null, path);
        this.routes[path] && this.routes[path]();
    }
}

window.Router = new HistoryRoutes();
Router.init(location.pathname);
/* 注册函数 */
Router.route('/',()=>{
    console.log('我是主页')
})
Router.route('/a',()=>{
    console.log('我是a页')
})
Router.route('/b',()=>{
    console.log('我是b页')
})
```

## react-router

> 源码链接：https://github.com/remix-run/react-router/tree/main/packages/react-router
>
> 在`README`中提到，
>
> *If you're using React Router, you should never `import` anything directly from the `react-router` package, but you should have everything you need in either `react-router-dom` or `react-router-native`. Both of those packages re-export everything from `react-router`.*
>
> 所以要注意不要直接`import`这个库

react-router提供了专门的路由匹配方法matchPath，依赖`path-to-regexp`包进行实现

{% note info %}

path-to-regexp包可以将路径字符串转化为两部分：

- 正则表达式（re）：

- 一个数组（keys）：储存param的key

{% endnote %}

**matchPath核心：**

- 首先`matchPath`通过`path-to regexp`来获取Route上定义的`path`对应的正则，再将生成的正则表达式与url中的pathname做正则匹配判断是否匹配

  ```js
  console.log(re.exec('/foo/randal'));   
  console.log(re.exec('/foos/randal'));
  
  // 输出
  [ '/foo/randal', 'randal', index: 0, input: '/foo/randal' ]
  null
  
  ```

- 由于`path-to-regexp`创建的正则中对`param`部分创建了捕获组，同时把`param`的`key`记录在了单独的数组`keys`中，因此通过遍历正则匹配的结果和`keys`数组即可将`param`的`key`和`value`进行关联

  ```js
  const match = re.exec('/foo/randal');
  const [url, ...values] = match;
  
  const params = keys.reduce((memo, key, index) => {
    memo[key.name] = values[index];
    return memo;
  }, {})
  
  console.log(params) // {"bar": "randal"}
  ```

- 最终matchPath针对未匹配的返回`null`，匹配成功的返回一个`object`

**Route渲染：**

Route组件维护一个`state`(match)，match的值来自于`matchPath`的执行结果

```js
state = {
    match: this.computeMatch(this.props, this.context.router)
  };
computeMatch({ computedMatch, location, path, strict, exact, sensitive }, router) {
  	if (computedMatch) return computedMatch; // computedMatch留给Switch使用
    const { route } = router;
    const pathname = (location || route.location).pathname;

    return matchPath(pathname, { path, strict, exact, sensitive }, route.match);
}

```

只有当`state.match`不为`null`的时候Route才会创建关联的`component`。

Route关联`component`有多种形式（render\component\children）children定义形式与render和component的不同在于，children的执行与match无关，`children`定义形式与`render`和`component`的不同在于，`children`的执行与`match`无关，即使`match`为`null`，`children`函数也是会执行的，这个与`Link`组件有关。

