# Vue 实例

1. 一个 Vue 应用由一个通过 new Vue 创建的根 Vue 实例，以及可选的嵌套的、可复用的组件树组成

```js
var vm = new Vue({
  // 选项
});
```

2. 只有当实例被创建时就已经存在于 data 中的 property 才是响应式的。特例使用 `Object.freeze()`会阻止修改现有的 property.

```js
// 我们的数据对象
var data = { a: 1 };

// 该对象被加入到一个 Vue 实例中
var vm = new Vue({
  data: data,
});
```

3. 生命周期
   ![Alt text](image.png)

注：

- 生命周期钩子的 this 上下文指向调用它的 Vue 实例
- 不要在选项 property 或回调上使用箭头函数，因为箭头函数并没有 this

# 模版语法

基于 HTML 的模版语法， 再经过 Vue 将模板编译成虚拟 DOM 渲染函数，结合响应系统，Vue 能够智能地计算出最少需要重新渲染多少组件，并把 DOM 操作次数减到最少。

## 插值

1.  mustach 语法 `{{}}`, 绑定的数据对象会响应式更新， `v-once` 能执行一次性地插值，当数据改变时，插值处的内容不会更新。

2.  v-html 解析原始 HTML， 容易受 XSS 攻击，只对可信内容使用 HTML 插值，绝不要对用户提供的内容使用插值。

3.  v-bind 能够 HTML attribute 上使用

```jsd="dynamicId"></div>
```

注： 对于需要 boolean 值的 attribute，赋值 null、undefined 或 false，则 disabled attribute 甚至不会被包含在渲染出来

4. 对于所有的数据绑定，支持 JavaScript 表达式

## 指令

1. 一些指令能够接收一个“参数”，在指令名称之后以冒号表示。

```js
<a v-bind:href="url">...</a>
<a v-on:click="doSomething">...</a>
```

2. 动态参数

```js
<a v-bind:[attributeName]="url"> ... </a>
```

3. 修饰符

修饰符 (modifier) 是以半角句号 . 指明的特殊后缀，用于指出一个指令应该以特殊方式绑定。例如，.prevent 修饰符告诉 v-on 指令对于触发的事件调用 event.preventDefault()：

```js
<form v-on:submit.prevent="onSubmit">...</form>
```

# 计算属性和侦听器

1. 计算属性的 property 默认接受一个 getter 函数， 且无副作用，不过也可以在需要时传入一个 setter 函数

2. 计算属性是基于它们的响应式依赖进行缓存的，只在相关响应式依赖发生改变时它们才会重新求值。方法是每当触发重新渲染时，总会再次执行函数。

3. 当需要在数据变化时执行异步或开销较大的操作时，可以使用侦听器（watch）

# Class 与 Style 绑定

## 绑定 class

1. 对象语法

```javascript
<div class="static" v-bind:class="{ active: isActive, 'text-danger': hasError }"></div>

<div v-bind:class="classObject"></div>
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

2. 数组语法

```js
<div v-bind:class="[activeClass, errorClass]"></div>

<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>

<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

## 绑定 style

1. 对象语法 同上
2. 数组语法 可以将多个样式对象应用到同一个元素上：

```js
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

# 条件渲染

1.  v-if 指令用于条件性地渲染一块内容。这块内容只会在指令的表达式返 truthy 值的时候被渲染。v-else, v-else-if

2.  用 key 管理可复用的元素

3.  v-show 用法和 v-if 一致，不同的是带有 v-show 的元素始终会被渲染并保留在 DOM 中。v-show 只是简单地切换元素的 CSS property display。<br/>
    **注** ：v-show 不支持 `<template>` 元素，也不支持 v-else。

# 列表渲染

1. v-for 遍历数组 v-for="(item, index) in items" ，遍历对象 v-for="(value, key, index) in items"

2. 不推荐在同一元素上使用 v-if 和 v-for，当它们处于同一节点，v-for 的优先级比 v-if 更高

# 事件处理

1. v-on 简写 @ ，当需要在内联语句处理器中访问原始的 DOM 事件。可以用特殊变量 `$event`

2. 事件修饰符， 注意使用顺序。 还有其他如 按键修饰符， 系统修饰符

```js
<!-- 阻止单击事件继续传播 -->
<a v-on:click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div v-on:click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div v-on:click.self="doThat">...</div>
```

# 组件

1. 组件是可复用的 Vue 实例，所以它们与 new Vue 接收相同的选项，例如 data、computed、watch、methods 以及生命周期钩子等。仅有的例外是像 el 这样根实例特有的选项。

```js
// 定义一个名为 button-counter 的新组件 （全局注册）
Vue.component('button-counter', {
  data: function () {
    return {
      count: 0,
    };
  },
  template: '<button v-on:click="count++">You clicked me {{ count }} times.</button>',
});
```

2. 组件的 data 选项必须是一个函数，因此每个实例可以维护一份被返回对象的独立的拷贝

3. 子组件调用父组件的方法不需要通过 props 传下去，可以在父组件通过 v-on 监听子组件实例的任意事件，子组件可以通过调用内建的 $emit 方法并传入事件名称来触发一个事件

```js
// 父组件
<blog-post
  v-on:enlarge-text="postFontSize += 0.1"
></blog-post>

// 子组件
<button v-on:click="$emit('enlarge-text')">
  Enlarge text
</button>
```

4. 使用 $emit 的第二个参数来传递参数， 在父组件监听时通过 $event 访问，或者事件处理函数是一个方法

```js
<blog-post
  v-on:enlarge-text="postFontSize += $event"
></blog-post>

<button v-on:click="$emit('enlarge-text', 0.1)">
  Enlarge text
</button>
```

5. <solot></solot> 插槽 类似于 react 里的 props.children

6. 动态组件
