---
title: Vue源码系列1-Vnode到真实node的渲染

date: 2020-07-22

tag: [vue]
---

## Vnode到真实node的渲染

重写app.mount的原因是vue需要支持多平台渲染，每个平台的mount方法都是对应平台的组件渲染流程。

```js
mount(rootContainer) {
  // 创建根组件的 vnode
  const vnode = createVNode(rootComponent, rootProps)
  // 利用渲染器渲染 vnode
  render(vnode, rootContainer)
  app._container = rootContainer
  return vnode.component.proxy
}
```

标准的流程都是先生成vnode，在渲染vnode。参数rootContainer可以是不同类型的对象，比如对应到web就是dom对象。

上面就是标准的流程，而重写这个方法，就是为了适应不同平台的渲染流程。

#### 1、创建Vode

vnode本质上是用来描述DOM的js对象，在vue中它可以描述不同类型的节点，比如普通元素、组件节点等。

普通：

```html
<button class="btn" style="width:100px;height:50px">click me</button>
```

vnode：

```js
const vnode = {
  type: 'button',
  props: { 
    'class': 'btn',
    style: {
      width: '100px',
      height: '50px'
    }
  },
  children: 'click me'
}
```

组件节点：

```html
<custom-component msg="test"></custom-component>
```

vnode：

```js
const CustomComponent = {
  // 在这里定义组件对象
}
const vnode = {
  type: CustomComponent,
  props: { 
    msg: 'test'
  }
}
```

vnode的优势在于**抽象化**和**跨平台**。

**vnode的性能其实不一定比手动操作dom好。**

因为，首先这种基于 vnode 实现的 MVVM 框架，在每次 render to vnode 的过程中，渲染组件会有一定的 JavaScript 耗时，特别是大组件，比如一个 1000 * 10 的 Table 组件，render to vnode 的过程会遍历 1000 * 10 次去创建内部 cell vnode，整个耗时就会变得比较长，加上 patch vnode 的过程也会有一定的耗时，当我们去更新组件的时候，用户会感觉到明显的卡顿。虽然 diff 算法在减少 DOM 操作方面足够优秀，但最终还是免不了操作 DOM，所以说性能并不是 vnode 的优势。

创建Vnode的大致流程：

```js
function createVNode(type, props = null
,children = null) {
  if (props) {
    // 处理 props 相关逻辑，标准化 class 和 style
  }
  // 对 vnode 类型信息编码
  const shapeFlag = isString(type)
    ? 1 /* ELEMENT */
    : isSuspense(type)
      ? 128 /* SUSPENSE */
      : isTeleport(type)
        ? 64 /* TELEPORT */
        : isObject(type)
          ? 4 /* STATEFUL_COMPONENT */
          : isFunction(type)
            ? 2 /* FUNCTIONAL_COMPONENT */
            : 0
  const vnode = {
    type,
    props,
    shapeFlag,
    // 一些其他属性
  }
  // 标准化子节点，把不同数据类型的 children 转成数组或者文本类型
  normalizeChildren(vnode, children)
  return vnode
}
```

- 对props做标准化处理
- 对Vnode的类型信息编码
- 创建Vnode对象
- 把不同类型的Children转换成数组或者文本：标准化子节点

#### 2、渲染Vnode

app.mount中调用的render：

```js
render(vnode, rootContainer)
const render = (vnode, container) => {
  if (vnode == null) {
    // 销毁组件
    if (container._vnode) {
      unmount(container._vnode, null, null, true)
    }
  } else {
    // 创建或者更新组件
    patch(container._vnode || null, vnode, container)
  }
  // 缓存 vnode 节点，表示已经渲染
  container._vnode = vnode
}
```

render的逻辑比较简单，在传入vnode的时候去更新组件，没有传入则代表销毁组件。

patch函数是核心，接受多个参数，目前暂时用到前3个：

1. 第一个参数表示旧vnode，当为null时，表示是一个挂载过程
2. 第二个参数是新节点，后续会根据节点的不同类型执行不同逻辑
3. 第三个参数则表示更新后的vnode应该挂载到的容器

对于渲染节点，重点关注组件节点和普通DOM元素

组件：

- mountComponent：创建组件实例、设置组件实例、设置并运行带副作用的渲染函数
- 首先是创建组件实例，Vue.js 3.0 虽然不像 Vue.js 2.x 那样通过类的方式去实例化组件，但内部也通过对象的方式去创建了当前渲染的组件实例。
- 其次设置组件实例，instance 保留了很多组件相关的数据，维护了组件的上下文，包括对 props、插槽，以及其他实例的属性的初始化处理。

###### 组件vnode

patch中主要做两件事情:渲染组件生成的subtree，首先渲染组件成subvnode，它是一个js对象。

_initialVnode(组件Vnode)和subtree的区别是前者代表的是这个组件Vnode， 后者表达组件内根据template渲染出的vnode。

######  普通DOM

创建dom元素、处理props、处理处理Children、挂载到DOM元素到container上（web端底层还是通过document.createElement，其他端有各自的方法）。

###### 子节点

vnode和dom都是树型结构，且是一一映射的，如果子节点是文本，那么直接设置DOM元素的文本属性。

如果子节点是数组，则调用mountChildren：

```js
const mountChildren = (children, container, anchor, parentComponent, parentSuspense, isSVG, optimized, start = 0) => {
  for (let i = start; i < children.length; i++) {
    // 预处理 child
    const child = (children[i] = optimized
      ? cloneIfMounted(children[i])
      : normalizeVNode(children[i]))
    // 递归 patch 挂载 child
    patch(null, child, container, anchor, parentComponent, parentSuspense, isSVG, optimized)
  }
}
```

子节点的挂载逻辑就是遍历children，然后递归执行patch，这里两个注意点是第一个遍历时对子节点做了预处理。第二patch中传入的container就是在mountElement中生成的dom节点，也就是children的父元素，这样就很好的建立了父子关系。本质上是一种深度优先遍历。

处理完子节点后，通过hostInsert方法把创建的DOM元素节点挂载到container上，它在web环境下：

```js
function insert(child, parent, anchor) {
  if (anchor) {
    parent.insertBefore(child, anchor)
  }
  else {
    parent.appendChild(child)
  }
}
```

因为 insert 的执行是在处理子节点后，所以**挂载的顺序是先子节点，后父节点，最终挂载到最外层的容器上**。

![](https://s0.lgstatic.com/i/image/M00/2E/0A/CgqCHl8EPLKAF8u5AAJHdNl56bM640.png)