---
title: React的diff算法
date: 2022-03-11 00:15:20
tags: React
categories: React
---

> 参考链接：https://zhuanlan.zhihu.com/p/20346379

# 什么是diff算法

React diff 会帮助我们计算出 Virtual DOM 中真正变化的部分，并只针对该部分进行实际 DOM  操作，而非重新渲染整个页面，从而保证了每次操作更新后页面的高效渲染，因此 Virtual DOM 与 diff 是保证 React  性能口碑的幕后推手。

# 传统diff

传统的diff算法的算法复杂度达到 O(n^3)

# React diff策略

- Web UI中DOM节点跨层级的移动操作特别少，因此忽略不计
- 拥有相同类的两个组件会生成相似的树结构，拥有不同类的两个组件将会生成不同的树形结构
- 对于同一层级的一组子节点，它们可以通过唯一id进行区分

# React对diff的优化

## tree diff

基于策略一，DOM节点跨层级的移动操作很少，所以React两棵树只会对同一层次的节点进行比较。通过updateDepth对虚拟DOM进行层级控制，这样只需要对树进行一次遍历就可以完成DOM树的比较。

```js
updateChildren: function(nextNestedChildrenElements, transaction, context) {
  updateDepth++; //层级控制
  var errorThrown = true;
  try {
    this._updateChildren(nextNestedChildrenElements, transaction, context);
    errorThrown = false;
  } finally {
    updateDepth--;
    if (!updateDepth) {
      if (errorThrown) {
        clearQueue();
      } else {
        processQueue();
      }
    }
  }
}
```

那万一出现了跨层级的移动操作，React diff会怎么处理呢？

对于跨层级的位置变换，React diff只会进行创建和删除操作。例如，把A移动到不同层级的B节点下。当根节点发现子节点中A消失了，就会直接销毁A。当B节点发现多了个子节点A，就会创建新的A节点。所以跨层级的移动操作其实是重新创建的过程。因此React官方建议不要进行DOM节点跨层级的操作

{% note info %}

注意：在开发组件时，保持稳定的 DOM 结构会有助于性能的提升。例如，可以通过 CSS 隐藏或显示节点，而不是真的移除或添加 DOM 节点。

{% endnote %}

## component diff

React是基于组件构建应用的，所以对于组件替换React也做了相应的优化

- 如果是同一类型的组件，则会按照`tree diff`继续比较虚拟DOM
- 如果不是，则将该组件判断为`dirty component`，直接替换整个组件下的所有子节点
- 对于同一类型的组件，有可能其`Virtual DOM`没有任何变化，如果能够确切的知道这点那可以节省大量的`diff`运算时间，因此`React`允许用户通过`shouldComponentUpdate()`来判断该组件是否需要进行`diff`

## element diff

当节点处于同一层级时，`React diff`提供了三种节点操作：

- INSERT_MARKUP：新的`component`类型不在老集合里，是全新的节点，则需要对新节点执行插入操作

- MOVE_EXISTING：在老集合有新的`component`类型，且`element`是可更新的类型，`generateComponentChildren`已调用`receiveComponent`，这种情况下`prevChild = nextChild`，就需要做移动操作，可以复用以前的DOM节点。

  为什么要有移动操作呢？因为只有插入删除操作的话，如果我们只是对几个节点进行顺序的变换，则会触发多次插入删除操作，这样的代价我们是不能接受的。

- REMOVE_NODE：老`component`类型， 在新集合里也有，但对应的`element`不同则不能直接复用和更新，需要执行删除操作，或者老`component`不在新集合的，也需要执行删除操作。

前面提到移动操作是用来复用节点的，那React如何判断节点相同呢？React通过`唯一key`对同一层级的同组子节点进行区分，只要比较`key`发现都是相同的节点，则无需进行插入和删除，只需要进行移动即可。

那么基于上面的三种操作，`element diff`的差异对比过程就形成了：

>从新集合中取得新节点，判断旧集合中是否有相同的节点：
>
>- 存在相同节点时：
>
>  通过对比节点的位置判断是否进行移动操作。只有在老集合中的节点位置小于`lastIndex`（遍历老节点的最右位置）才会进行节点移动;
>
>  `lastIndex`的更新规则为`lastIndex = Math.max(lastIndex, prevChild._mountIndex)`，即取访问过的旧集合的最右元素下标。
>
>- 存在不同节点时（新节点需要添加或旧节点需要删除）：
>
>  当旧集合不存在新节点时，则创建新节点，并更新lastIndex
>
>  当完成新集合中所有节点diff时，最后还需要对旧集合进行循环遍历，判断是否存在只有旧集合存在的节点，若存在则删除。

