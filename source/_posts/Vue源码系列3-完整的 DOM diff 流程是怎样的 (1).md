---
title: Vue源码系列2-完整的 DOM diff 流程是怎样的

date: 2020-07-22

tag: [vue]
---

组件的渲染过程中创建了一个带副作用的渲染函数，当数据变化的时候就会执行这个渲染函数来触发组件的更新。

#### 副作用渲染函数更新组件的过程

更新组件主要做三件事情：**更新组件 vnode 节点(initialTree)、渲染新的子树 vnode(subTree)、根据新旧子树 vnode 执行 patch 逻辑。**

patch流程：

首先判断新旧节点是否是相同的 vnode 类型，如果不同，比如一个 div 更新成一个 ul，那么最简单的操作就是删除旧的 div 节点，再去挂载新的 ul 节点。

如果是相同的 vnode 类型，就需要走 diff 更新流程了，接着会根据不同的 vnode 类型执行不同的处理逻辑，这里我们仍然只分析普通元素类型和组件类型的处理过程。

加入App组件中：

```html
<template>
	<div>
		<p>111</p>
		<hello :msg="msg"></hello>
	</div>
</template>
```

如果msg发生了变化，那么首先触发app组件的更新，app组件的子树subtree是一个普通div，所以触发processElement逻辑，然后因为深度遍历，遇到hello这个节点的时候，会执行processComponent逻辑：

```js
const processComponent = (n1, n2, container, anchor, parentComponent, parentSuspense, isSVG, optimized) => {
  if (n1 == null) {
    // 挂载组件
  }
  else {
    // 更新子组件
    updateComponent(n1, n2, parentComponent, optimized)
  }
}
const updateComponent = (n1, n2, parentComponent, optimized) => {
  const instance = (n2.component = n1.component)
  // 根据新旧子组件 vnode 判断是否需要更新子组件
  if (shouldUpdateComponent(n1, n2, parentComponent, optimized)) {
    // 新的子组件 vnode 赋值给 instance.next
    instance.next = n2
    // 子组件也可能因为数据变化被添加到更新队列里了，移除它们防止对一个子组件重复更新
    invalidateJob(instance.update)
    // 执行子组件的副作用渲染函数
    instance.update()
  }
  else {
    // 不需要更新，只复制属性
    n2.component = n1.component
    n2.el = n1.el
  }
}
```

processComponent 主要通过执行 updateComponent 函数来更新子组件，updateComponent 函数在更新子组件的时候，会先执行 shouldUpdateComponent 函数，根据新旧子组件 vnode 来判断是否需要更新子组件。这里你只需要知道，**在 shouldUpdateComponent 函数的内部，主要是通过检测和对比组件 vnode 中的 props、chidren、dirs、transiton 等属性，来决定子组件是否需要更新**。

如果shouldUpdateComponent 返回true，那么会先执行invalidateJob避免一些不必要的更新，然后执行instance.update来主动触发子组件的更新。

回顾副作用函数（setupRenderEffect）的一部分代码：

```js
// 更新组件
let { next, vnode } = instance
// next 表示新的组件 vnode
if (next) {
  // 更新组件 vnode 节点信息
  updateComponentPreRender(instance, next, optimized)
}
else {
  next = vnode
}
const updateComponentPreRender = (instance, nextVNode, optimized) => {
  // 新组件 vnode 的 component 属性指向组件实例
  nextVNode.component = instance
  // 旧组件 vnode 的 props 属性
  const prevProps = instance.vnode.props
  // 组件实例的 vnode 属性指向新的组件 vnode
  instance.vnode = nextVNode
  // 清空 next 属性，为了下一次重新渲染准备
  instance.next = null
  // 更新 props
  updateProps(instance, nextVNode.props, prevProps, optimized)
  // 更新 插槽
  updateSlots(instance, nextVNode.children)
}
```

结合上面的代码，**我们在更新组件的 DOM 前，需要先更新组件 vnode 节点信息**，包括更改组件实例的 vnode 指针、更新 props 和更新插槽等一系列操作，因为组件在稍后执行 renderComponentRoot 时会重新渲染新的子树 vnode ，它依赖了更新后的组件 vnode 中的 props 和 slots 等数据。

现在我们知道了一个组件重新渲染可能会有两种场景，**一种是组件本身的数据变化，这种情况下 next 是 null；另一种是父组件在更新的过程中，遇到子组件节点，先判断子组件是否需要更新，如果需要则主动执行子组件的重新渲染方法，这种情况下 next 就是新的子组件 vnode。**

所以processComponent本质上是去判断vnode中子组件是否需要更新，如果不需要，那就仅仅是更新一些vnode的属性，并让子组件保留对先的父组件vnode的引用，方便下次子组件更新的时候可以拿到父组件。

**不过组件是抽象的，所以的组件即使经过深层递归后，最终也都会落到DOM元素的更新**，所以接下来讨论普通元素的处理流程：

先看上文提到的processElement

```js
const processElement = (n1, n2, container, anchor, parentComponent, parentSuspense, isSVG, optimized) => {
  isSVG = isSVG || n2.type === 'svg'
  if (n1 == null) {
    // 挂载元素
  }
  else {
    // 更新元素
    patchElement(n1, n2, parentComponent, parentSuspense, isSVG, optimized)
  }
}
const patchElement = (n1, n2, parentComponent, parentSuspense, isSVG, optimized) => {
  const el = (n2.el = n1.el)
  const oldProps = (n1 && n1.props) || EMPTY_OBJ
  const newProps = n2.props || EMPTY_OBJ
  // 更新 props
  patchProps(el, n2, oldProps, newProps, parentComponent, parentSuspense, isSVG)
  const areChildrenSVG = isSVG && n2.type !== 'foreignObject'
  // 更新子节点
  patchChildren(n1, n2, el, null, parentComponent, parentSuspense, areChildrenSVG)
}
```

更新DOM的流程比较简单，就是更新props和子节点，因为一个DOM元素一般也就是由这两部分构成的。

更新子节点是一个相对复杂的过程，一个子节点一般会有三种情况：纯文本、vnode数组和空。所以新旧节点排列组合就可能有9种情况。

![](https://s0.lgstatic.com/i/image/M00/31/18/Ciqc1F8MBDWAfUAXAADe59XvjHY701.png)

![](https://s0.lgstatic.com/i/image/M00/31/23/CgqCHl8MBEOANnFmAADYr-_R5mM894.png)

![](https://s0.lgstatic.com/i/image/M00/31/23/CgqCHl8MBCuAUZksAADplAU2718113.png)

而最为复杂的就是在都是数组的情况下，需要进行完整的diff算法。