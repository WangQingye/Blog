---
title: Vue 自定义指令

date: 2019-5-7

tag: [vue]
---

## Vue 自定义指令

参考文章：[Vue自定义指令](https://cn.vuejs.org/v2/guide/custom-directive.html)

vue除了常见的v-model 和 v-show指令外，还可以自定义模板中的指令。

#### 使用方式

全局方式：

```javascript
// 注册一个全局自定义指令 `v-focus`
Vue.directive('focus', {
  // 当被绑定的元素插入到 DOM 中时……
  inserted: function (el) {
    // 聚焦元素
    el.focus()
  }
})
```

组件中局部注册：

```javascript

```

简化方式：

```javascript
Vue.directive('color-swatch', function (el, binding) {
  el.style.backgroundColor = binding.value
})
```

这样书写等同于在钩子函数中的bind和update书写，即每次更新时调用。

使用方式：

```html
<input v-focus>
```

#### 钩子函数

上面代码中的inserted是来自于指令参数对象提供的钩子函数：

- `bind`：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。
- `inserted`：被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)。
- `update`：所在组件的 VNode 更新时调用，**但是可能发生在其子 VNode 更新之前**。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新 (详细的钩子函数参数见下)。

- `componentUpdated`：指令所在组件的 VNode **及其子 VNode** 全部更新后调用。
- `unbind`：只调用一次，指令与元素解绑时调用。

#### 钩子函数的参数

- `el`：指令所绑定的元素，可以用来直接操作 DOM 。
- binding：一个对象，包含以下属性：
  - `name`：指令名，不包括 `v-` 前缀。
  - `value`：指令的绑定值，例如：`v-my-directive="1 + 1"` 中，绑定值为 `2`，value也可以是对象，取法：binding.value.xxx。
  - `oldValue`：指令绑定的前一个值，仅在 `update` 和 `componentUpdated` 钩子中可用。无论值是否改变都可用。
  - `expression`：字符串形式的指令表达式。例如 `v-my-directive="1 + 1"`中，表达式为 `"1 + 1"`。
  - `arg`：传给指令的参数，可选。例如 `v-my-directive:foo` 中，参数为 `"foo"`。
  - `modifiers`：一个包含修饰符的对象。例如：`v-my-directive.foo.bar` 中，修饰符对象为 `{ foo: true, bar: true }`。
- `vnode`：Vue 编译生成的虚拟节点。移步 [VNode API](https://cn.vuejs.org/v2/api/#VNode-%E6%8E%A5%E5%8F%A3) 来了解更多详情。
- `oldVnode`：上一个虚拟节点，仅在 `update` 和 `componentUpdated` 钩子中可用。

例子：

```html

```

```js
Vue.directive('demo', {
  bind: function (el, binding, vnode) {
    var s = JSON.stringify
    el.innerHTML =
      'name: '       + s(binding.name) + '<br>' +
      'value: '      + s(binding.value) + '<br>' +
      'expression: ' + s(binding.expression) + '<br>' +
      'argument: '   + s(binding.arg) + '<br>' +
      'modifiers: '  + s(binding.modifiers) + '<br>' +
      'vnode keys: ' + Object.keys(vnode).join(', ')
  }
})

new Vue({
  el: '#hook-arguments-example',
  data: {
    message: 'hello!'
  }
})
```

![](https://user-gold-cdn.xitu.io/2019/5/7/16a915ca52415f0f?w=599&h=327&f=png&s=27450)

#### 指令参数的动态执行

指令的参数可以是动态的。例如，在 `v-mydirective:argument=[dataproperty]`中，`argument` 是一个赋值给这个指令钩子 `binding` 参数中的 *arg* property 的字符串，同时 `dataproperty` 是一个引用到组件实例上并赋值给同一个 *binding* 参数中的 *value* property 的 data property。当指令钩子被调用的时候，`binding` 参数中的 *value*property 会基于 `dataproperty` 的值动态改变。

一个例子：

```html
<div id="app">
  <p>Scroll down the page</p>
  <p v-tack:left="[dynamicleft]">I’ll now be offset from the left instead of the top</p>
</div>
```

```js
Vue.directive('tack', {
  bind(el, binding, vnode) {
    el.style.position = 'fixed';
    const s = (binding.arg == 'left' ? 'left' : 'top');
    el.style[s] = binding.value + 'px';
  }
})

// start app
new Vue({
  el: '#app',
  data() {
    return {
      dynamicleft: 500
    }
  }
})
```

