---
title: React HOC
date: 2022-04-14 14:57:56
tags: React
categories: React
---

> 参考链接：
>
> https://zh-hans.reactjs.org/docs/higher-order-components.html#use-hocs-for-crossing-cutting-concerns
>
> https://blog.csdn.net/qq_35534823/article/details/80566037

# 为什么需要HOC（高阶组件）

按官方文档中的例子，当我们存在许多相同逻辑的组件的时候（比如：添加侦听-更新页面-删除侦听），我们需要一个抽象，来让这种逻辑在许多组件之间可以共享。

# 高阶组件是什么

高阶组件其实就是对现有的组件进行一次封装生成新的组件。

我们通过HOC这个纯函数，对传入的组件进行修改，有点像是JS的工厂模式。

HOC不会使用继承来复制其行为，而是把组件包装在容器组件中来组成新组件。

我们先来看官方的例子，`withSubscription`就是一个HOC函数：

```react
// 此函数接收一个组件...
function withSubscription(WrappedComponent, selectData) {
  // ...并返回另一个组件...
  return class extends React.Component {
    constructor(props) {
      super(props);
      this.handleChange = this.handleChange.bind(this);
      this.state = {
        data: selectData(DataSource, props)
      };
    }

    componentDidMount() {
      // ...负责订阅相关的操作...
      DataSource.addChangeListener(this.handleChange);
    }

    componentWillUnmount() {
      DataSource.removeChangeListener(this.handleChange);
    }

    handleChange() {
      this.setState({
        data: selectData(DataSource, this.props)
      });
    }

    render() {
      // ... 并使用新数据渲染被包装的组件!
      // 请注意，我们可能还会传递其他属性
      return <WrappedComponent data={this.state.data} {...this.props} />;
    }
  };
}
```

首先可以看到，这个函数总体的结构是这样的：

```react
function withSubscription(WrappedComponent, selectData) {
  return class extends React.Component {
  };
}
```

可以看到，`withSubscription`接收两个参数，第一个参数代表了被包装的组件，第二个参数代表的是对传入组件进行差异处理的函数（下面继续解析），然后`withSubscription`返回了一个新的组件，符合我们所说的对组件包装变成新组件的结构。

我们再接着分析两个参数：

 ```react
 function withSubscription(WrappedComponent, selectData) {
   return class extends React.Component {
     constructor(props) {
       this.state = {
         data: selectData(DataSource, props)
       };
     }
 
     handleChange() {
       this.setState({
         data: selectData(DataSource, this.props)
       });
     }
 
     render() {
       return <WrappedComponent data={this.state.data} {...this.props} />;
     }
   };
 }
 ```

可以看到`render`最后渲染的是第一个参数传入的组件，同时增加了一个属性名为`data`的`prop`，这个`data`是第二个参数传入的函数的运行结果。

而其它省略的`componentDidMount`等生命周期部分，则是通过`withSubscription`生成的新组件所共有的逻辑，比如使用下方的代码，我们就可以生成两个逻辑上类似，实现上有区分的组件：

```react
const CommentListWithSubscription = withSubscription(
  CommentList,
  (DataSource) => DataSource.getComments()
);

const BlogPostWithSubscription = withSubscription(
  BlogPost,
  (DataSource, props) => DataSource.getBlogPost(props.id)
);
```

这个区别就来自第二个参数。

# HOC的原则

- **不要改变原始组件，而应该使用组合。**

  一旦修改了组件，可能导致原始组件无法像增强前一样使用了。而且有可能不同的HOC之间的修改会产生冲突。这也是高耦合的一种缺点。

- 将不相关的props传递给被包裹的组件

  HOC需要保留原始组件的特性，所以返回的组件应该与源组件保持类似的接口。大部分HOC都应该包含一个类似的render：

  ```react
  render() {
    // 过滤掉非此 HOC 额外的 props，且不要进行透传
    const { extraProp, ...passThroughProps } = this.props;
  
    // 将 props 注入到被包装的组件中。
    // 通常为 state 的值或者实例方法。
    const injectedProp = someStateOrInstanceMethod;
  
    // 将 props 传递给被包装组件
    return (
      <WrappedComponent
        injectedProp={injectedProp}
        {...passThroughProps}
      />
    );
  }
  ```

- 最大化可组合性

  有的HOC仅接受一个参数，也就是被包裹的组件，例如：
  
  ```react
  const NavbarWithRouter = withRouter(Navbar);
  ```
  
  但是，一个参数会比较不灵活，HOC通常可以接收多个参数，例如下面的HOC额外接收了一个配置对象用于指定组件的数据依赖：
  
  ```react
  const CommentWithRelay = Relay.createContainer(Comment, config);
  ```
  
  还有一种常见的HOC签名：
  
  ```react
  // React Redux 的 `connect` 函数
  const ConnectedComment = connect(commentSelector, commentActions)(CommentList);
  ```
  
  是不是想起了js的柯里化:)
  
  因为这里的connect是   一个返回高阶组件的高阶函数（也就是HOC），有点像是一个装饰器的角色。
  
- 包装显示名称以便轻松调试

  设置包装名称是为了方便调试的时候可以看出组件是HOC产物。

  ```react
  function withSubscription(WrappedComponent) {
    class WithSubscription extends React.Component {/* ... */}
    WithSubscription.displayName = `WithSubscription(${getDisplayName(WrappedComponent)})`;
    return WithSubscription;
  }
  
  function getDisplayName(WrappedComponent) {
    return WrappedComponent.displayName || WrappedComponent.name || 'Component';
  }
  ```

# 注意事项

- 不要在render方法中使用HOC：

  React是使用diff算法来判断是否应该更新组件的，如果在render中使用HOC会因为每次返回的组件不同而导致每次都会重新挂载，影响性能而且会导致该组件及其所有子组件的状态丢失

- 务必复制静态方法：

  使用HOC包装原始组件时，不会复制原始组件的静态方法。

  我们可以有以下解决方式：

  - 使用HOC函数之后，手动添加函数

  - 在HOC函数的return前，手动添加函数

  - 使用`hoist-non-react-statics`自动拷贝所有非`React`静态方法

    ```react
    import hoistNonReactStatic from 'hoist-non-react-statics';
    function enhance(WrappedComponent) {
      class Enhance extends React.Component {/*...*/}
      hoistNonReactStatic(Enhance, WrappedComponent);
      return Enhance;
    }
    ```

- Refs不会被传递

  因为`refs`并不是一个`prop`，而高阶组件是将所有的`props`传递给被包装组件，所以如果将`ref`添加到HOC的返回组件中，则`ref`引用指向容器组件而不是被包装组件

