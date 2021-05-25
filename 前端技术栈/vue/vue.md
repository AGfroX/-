# 概念





# 生命周期

## 生命周期图解：

![Vue生命周期](vue.assets/Vue%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.jpg)

![1060770-20170716220427050-1577910455](vue.assets/1060770-20170716220427050-1577910455.png)



## 生命周期函数：



| 钩子函数      | 详情                                                         | 场景                                                         | 备注                                                   |
| ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------ |
| beforeCreate  | 实例初始化之后，组件被创建时期，这个时候的 el，data，message 都是 underfined | 可以加入 loading 事件；在服务器端的应用场景中，这个时候发送数据请求比较多一些 |                                                        |
| created       | 实例创建完成后，data、methods 被初始化                       | 结束 loading 事件；推荐这个时候发送请求数据，尤其是返回的数据与绑定事件有关时 |                                                        |
| beforeMount   | 挂载初始之前，完成 el 初始化，render 被初次调用              | 也可以发送数据请求                                           | 在服务器端渲染期间不会被调用                           |
| mounted       | 完成挂载                                                     | 获取 el 中 DOM 元素，进行 DOM 操作；如果返回的数据操作依赖 DOM 完成，推荐这个时候发送数据请求 | 在服务器端渲染期间不会被调用                           |
| beofreUpdate  | 挂载开始之前调用                                             | 挂载完成之前访问现有 DOM ，比如手动移除已添加的事件监听器；也可以进一步修改数据 | 在服务器渲染期间不会被调用，只有初次渲染会在服务端调用 |
| updated       | 由于数据更改，重新渲染界面时调用                             | 可执行依赖于 DOM 的操作                                      | 服务器端渲染期间不会被调用                             |
| activated     | keep-alive 组件激活时调用                                    |                                                              | 服务器端渲染期间不会被调用                             |
| deactivated   | 组件停用时调用                                               |                                                              | 服务器端渲染期间不会被调用                             |
| beforeDestroy | 实例销毁之前调用                                             | 删除钱发出确认信息；清理定时器                               | 服务器端渲染期间不会被调用                             |
| destroyed     | 实例销毁后调有。调用后，所有东西都会被解绑，所有的事件监听器会被移动，子实例也会被销毁 | 提示已删除                                                   | 服务器端渲染期间不会被调用                             |
| errorCaptured | 当捕获一个来自子孙组件的错误时被调用。收到三个参数：错误对象、发生错误的组件实例以及一个包含错误来源信息的字符串。可返回 false 以阻止该错误继续向上传播。 | 修改组件状态                                                 | 2.5.0新增                                              |

------



## 核心组件

网络通信（axios（实现ajax））：
页面跳转（router）：
状态管理（vuex）:



UI库：
Element-UI
iView

# 基本指令



v-if
v-show
v-for
v-on
v-model
v-bind
v-text
v-show
v-pre
v-cloak
v-once



v-html
使用 v-html 指令用于输出 html 代码：

```typescript
<div id="app">
    <div v-html="message"></div>
</div>
    
<script>
new Vue({
  el: '#app',
  data: {
    message: '<h1>菜鸟教程</h1>'
  }
})
</script>
```



v-bind
HTML 属性中的值应使用 v-bind 指令。

```typescript
<div id="app">
  <label for="r1">修改颜色</label><input type="checkbox" v-model="use" id="r1">
  <br><br>
  <div v-bind:class="{'class1': use}">
    v-bind:class 指令
  </div>
</div>
    
<script>
new Vue({
    el: '#app',
  data:{
      use: false
  }
});
</script>

---------------------------------
Vue.js 都提供了完全的 JavaScript 表达式支持。

JavaScript 表达式
<div id="app">
    {{5+5}}<br>
    {{ ok ? 'YES' : 'NO' }}<br>
    {{ message.split('').reverse().join('') }}
    <div v-bind:id="'list-' + id">菜鸟教程</div>
</div>
    
<script>
new Vue({
  el: '#app',
  data: {
    ok: true,
    message: 'RUNOOB',
    id : 1
  }
})
</script>
```



v-mode
在 input 输入框中我们可以使用 v-model 指令来实现双向数据绑定：

```typescript
<div id="app">
    <p>{{ message }}</p>
    <input v-model="message">
</div>
    
<script>
new Vue({
  el: '#app',
  data: {
    message: 'Runoob!'
  }
})
</script>
```

**v-model** 指令用来在 input、select、textarea、checkbox、radio 等表单控件元素上创建双向数据绑定，根据表单上的值，自动更新绑定的元素的值。

按钮的事件我们可以使用 v-on 监听事件，并对用户的输入进行响应。

以下实例在用户点击按钮后对字符串进行反转操作：

```typescript
<div id="app">
    <p>{{ message }}</p>
    <button v-on:click="reverseMessage">反转字符串</button>
</div>
    
<script>
new Vue({
  el: '#app',
  data: {
    message: 'Runoob!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
</script>
```



## [v-text](https://vuejs.bootcss.com/api/#v-text)

- **预期**：`string`

- **详细**：

  更新元素的 `textContent`。如果要更新部分的 `textContent`，需要使用 `{{ Mustache }}` 插值。

- **示例**：

  ```
  <span v-text="msg"></span>
  <!-- 和下面的一样 -->
  <span>{{msg}}</span>
  ```

- **参考**：[数据绑定语法 - 插值](https://vuejs.bootcss.com/guide/syntax.html#插值)

## [v-html](https://vuejs.bootcss.com/api/#v-html)

- **预期**：`string`

- **详细**：

  更新元素的 `innerHTML`。**注意：内容按普通 HTML 插入 - 不会作为 Vue 模板进行编译**。如果试图使用 `v-html` 组合模板，可以重新考虑是否通过使用组件来替代。

  在网站上动态渲染任意 HTML 是非常危险的，因为容易导致 [XSS 攻击](https://en.wikipedia.org/wiki/Cross-site_scripting)。只在可信内容上使用 `v-html`，**永不**用在用户提交的内容上。

  在[单文件组件](https://vuejs.bootcss.com/guide/single-file-components.html)里，`scoped` 的样式不会应用在 `v-html` 内部，因为那部分 HTML 没有被 Vue 的模板编译器处理。如果你希望针对 `v-html` 的内容设置带作用域的 CSS，你可以替换为 [CSS Modules](https://vue-loader.vuejs.org/en/features/css-modules.html) 或用一个额外的全局 `<style>` 元素手动设置类似 BEM 的作用域策略。

- **示例**：

  ```
  <div v-html="html"></div>
  ```

- **参考**：[数据绑定语法 - 插值](https://vuejs.bootcss.com/guide/syntax.html#纯-HTML)

## [v-show](https://vuejs.bootcss.com/api/#v-show)

- **预期**：`any`

- **用法**：

  根据表达式之真假值，切换元素的 `display` CSS property。

  当条件变化时该指令触发过渡效果。

- **参考**：[条件渲染 - v-show](https://vuejs.bootcss.com/guide/conditional.html#v-show)

## [v-if](https://vuejs.bootcss.com/api/#v-if)

- **预期**：`any`

- **用法**：

  根据表达式的值的 [truthiness](https://developer.mozilla.org/zh-CN/docs/Glossary/Truthy) 来有条件地渲染元素。在切换时元素及它的数据绑定 / 组件被销毁并重建。如果元素是 `<template>`，将提出它的内容作为条件块。

  当条件变化时该指令触发过渡效果。

  当和 `v-if` 一起使用时，`v-for` 的优先级比 `v-if` 更高。详见[列表渲染教程](https://vuejs.bootcss.com/guide/list.html#v-for-with-v-if)

- **参考**：[条件渲染 - v-if](https://vuejs.bootcss.com/guide/conditional.html)

## [v-else](https://vuejs.bootcss.com/api/#v-else)

- **不需要表达式**

- **限制**：前一兄弟元素必须有 `v-if` 或 `v-else-if`。

- **用法**：

  为 `v-if` 或者 `v-else-if` 添加“else 块”。

  ```
  <div v-if="Math.random() > 0.5">
    Now you see me
  </div>
  <div v-else>
    Now you don't
  </div>
  ```

- **参考**：[条件渲染 - v-else](https://vuejs.bootcss.com/guide/conditional.html#v-else)

## [v-else-if](https://vuejs.bootcss.com/api/#v-else-if)

> 2.1.0 新增

- **类型**：`any`

- **限制**：前一兄弟元素必须有 `v-if` 或 `v-else-if`。

- **用法**：

  表示 `v-if` 的“else if 块”。可以链式调用。

  ```
  <div v-if="type === 'A'">
    A
  </div>
  <div v-else-if="type === 'B'">
    B
  </div>
  <div v-else-if="type === 'C'">
    C
  </div>
  <div v-else>
    Not A/B/C
  </div>
  ```

- **参考**：[条件渲染 - v-else-if](https://vuejs.bootcss.com/guide/conditional.html#v-else-if)

## [v-for](https://vuejs.bootcss.com/api/#v-for)

- **预期**：`Array | Object | number | string | Iterable (2.6 新增)`

- **用法**：

  基于源数据多次渲染元素或模板块。此指令之值，必须使用特定语法 `alias in expression`，为当前遍历的元素提供别名：

  ```
  <div v-for="item in items">
    {{ item.text }}
  </div>
  ```

  另外也可以为数组索引指定别名 (或者用于对象的键)：

  ```
  <div v-for="(item, index) in items"></div>
  <div v-for="(val, key) in object"></div>
  <div v-for="(val, name, index) in object"></div>
  ```

  `v-for` 的默认行为会尝试原地修改元素而不是移动它们。要强制其重新排序元素，你需要用特殊 attribute `key` 来提供一个排序提示：

  ```
  <div v-for="item in items" :key="item.id">
    {{ item.text }}
  </div>
  ```

  从 2.6 起，`v-for` 也可以在实现了[可迭代协议](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols#可迭代协议)的值上使用，包括原生的 `Map` 和 `Set`。不过应该注意的是 Vue 2.x 目前并不支持可响应的 `Map` 和 `Set` 值，所以无法自动探测变更。

  当和 `v-if` 一起使用时，`v-for` 的优先级比 `v-if` 更高。详见[列表渲染教程](https://vuejs.bootcss.com/guide/list.html#v-for-with-v-if)

  `v-for` 的详细用法可以通过以下链接查看教程详细说明。

- **参考**：

  - [列表渲染](https://vuejs.bootcss.com/guide/list.html)
  - [key](https://vuejs.bootcss.com/guide/list.html#key)

## [v-on](https://vuejs.bootcss.com/api/#v-on)

- **缩写**：`@`

- **预期**：`Function | Inline Statement | Object`

- **参数**：`event`

- **修饰符**：

  - `.stop` - 调用 `event.stopPropagation()`。
  - `.prevent` - 调用 `event.preventDefault()`。
  - `.capture` - 添加事件侦听器时使用 capture 模式。
  - `.self` - 只当事件是从侦听器绑定的元素本身触发时才触发回调。
  - `.{keyCode | keyAlias}` - 只当事件是从特定键触发时才触发回调。
  - `.native` - 监听组件根元素的原生事件。
  - `.once` - 只触发一次回调。
  - `.left` - (2.2.0) 只当点击鼠标左键时触发。
  - `.right` - (2.2.0) 只当点击鼠标右键时触发。
  - `.middle` - (2.2.0) 只当点击鼠标中键时触发。
  - `.passive` - (2.3.0) 以 `{ passive: true }` 模式添加侦听器

- **用法**：

  绑定事件监听器。事件类型由参数指定。表达式可以是一个方法的名字或一个内联语句，如果没有修饰符也可以省略。

  用在普通元素上时，只能监听[**原生 DOM 事件**](https://developer.mozilla.org/zh-CN/docs/Web/Events)。用在自定义元素组件上时，也可以监听子组件触发的**自定义事件**。

  在监听原生 DOM 事件时，方法以事件为唯一的参数。如果使用内联语句，语句可以访问一个 `$event` property：`v-on:click="handle('ok', $event)"`。

  从 `2.4.0` 开始，`v-on` 同样支持不带参数绑定一个事件/监听器键值对的对象。注意当使用对象语法时，是不支持任何修饰器的。

- **示例**：

  ```
  <!-- 方法处理器 -->
  <button v-on:click="doThis"></button>
  
  <!-- 动态事件 (2.6.0+) -->
  <button v-on:[event]="doThis"></button>
  
  <!-- 内联语句 -->
  <button v-on:click="doThat('hello', $event)"></button>
  
  <!-- 缩写 -->
  <button @click="doThis"></button>
  
  <!-- 动态事件缩写 (2.6.0+) -->
  <button @[event]="doThis"></button>
  
  <!-- 停止冒泡 -->
  <button @click.stop="doThis"></button>
  
  <!-- 阻止默认行为 -->
  <button @click.prevent="doThis"></button>
  
  <!-- 阻止默认行为，没有表达式 -->
  <form @submit.prevent></form>
  
  <!--  串联修饰符 -->
  <button @click.stop.prevent="doThis"></button>
  
  <!-- 键修饰符，键别名 -->
  <input @keyup.enter="onEnter">
  
  <!-- 键修饰符，键代码 -->
  <input @keyup.13="onEnter">
  
  <!-- 点击回调只会触发一次 -->
  <button v-on:click.once="doThis"></button>
  
  <!-- 对象语法 (2.4.0+) -->
  <button v-on="{ mousedown: doThis, mouseup: doThat }"></button>
  ```

  在子组件上监听自定义事件 (当子组件触发“my-event”时将调用事件处理器)：

  ```
  <my-component @my-event="handleThis"></my-component>
  
  <!-- 内联语句 -->
  <my-component @my-event="handleThis(123, $event)"></my-component>
  
  <!-- 组件中的原生事件 -->
  <my-component @click.native="onClick"></my-component>
  ```

- **参考**：

  - [事件处理器](https://vuejs.bootcss.com/guide/events.html)
  - [组件 - 自定义事件](https://vuejs.bootcss.com/guide/components.html#监听子组件事件)

## [v-bind](https://vuejs.bootcss.com/api/#v-bind)

- **缩写**：`:`

- **预期**：`any (with argument) | Object (without argument)`

- **参数**：`attrOrProp (optional)`

- **修饰符**：

  - `.prop` - 作为一个 DOM property 绑定而不是作为 attribute 绑定。([差别在哪里？](https://stackoverflow.com/questions/6003819/properties-and-attributes-in-html#answer-6004028))
  - `.camel` - (2.1.0+) 将 kebab-case attribute 名转换为 camelCase。(从 2.1.0 开始支持)
  - `.sync` (2.3.0+) 语法糖，会扩展成一个更新父组件绑定值的 `v-on` 侦听器。

- **用法**：

  动态地绑定一个或多个 attribute，或一个组件 prop 到表达式。

  在绑定 `class` 或 `style` attribute 时，支持其它类型的值，如数组或对象。可以通过下面的教程链接查看详情。

  在绑定 prop 时，prop 必须在子组件中声明。可以用修饰符指定不同的绑定类型。

  没有参数时，可以绑定到一个包含键值对的对象。注意此时 `class` 和 `style` 绑定不支持数组和对象。

- **示例**：

  ```
  <!-- 绑定一个 attribute -->
  <img v-bind:src="imageSrc">
  
  <!-- 动态 attribute 名 (2.6.0+) -->
  <button v-bind:[key]="value"></button>
  
  <!-- 缩写 -->
  <img :src="imageSrc">
  
  <!-- 动态 attribute 名缩写 (2.6.0+) -->
  <button :[key]="value"></button>
  
  <!-- 内联字符串拼接 -->
  <img :src="'/path/to/images/' + fileName">
  
  <!-- class 绑定 -->
  <div :class="{ red: isRed }"></div>
  <div :class="[classA, classB]"></div>
  <div :class="[classA, { classB: isB, classC: isC }]">
  
  <!-- style 绑定 -->
  <div :style="{ fontSize: size + 'px' }"></div>
  <div :style="[styleObjectA, styleObjectB]"></div>
  
  <!-- 绑定一个全是 attribute 的对象 -->
  <div v-bind="{ id: someProp, 'other-attr': otherProp }"></div>
  
  <!-- 通过 prop 修饰符绑定 DOM attribute -->
  <div v-bind:text-content.prop="text"></div>
  
  <!-- prop 绑定。“prop”必须在 my-component 中声明。-->
  <my-component :prop="someThing"></my-component>
  
  <!-- 通过 $props 将父组件的 props 一起传给子组件 -->
  <child-component v-bind="$props"></child-component>
  
  <!-- XLink -->
  <svg><a :xlink:special="foo"></a></svg>
  ```

  `.camel` 修饰符允许在使用 DOM 模板时将 `v-bind` property 名称驼峰化，例如 SVG 的 `viewBox` property：

  ```
  <svg :view-box.camel="viewBox"></svg>
  ```

  在使用字符串模板或通过 `vue-loader`/`vueify` 编译时，无需使用 `.camel`。

- **参考**：

  - [Class 与 Style 绑定](https://vuejs.bootcss.com/guide/class-and-style.html)
  - [组件 - Props](https://vuejs.bootcss.com/guide/components.html#通过-Prop-向子组件传递数据)
  - [组件 - `.sync` 修饰符](https://vuejs.bootcss.com/guide/components-custom-events.html#sync-修饰符)

## [v-model](https://vuejs.bootcss.com/api/#v-model)

- **预期**：随表单控件类型不同而不同。

- **限制**：

  - `<input>`
  - `<select>`
  - `<textarea>`
  - components

- **修饰符**：

  - [`.lazy`](https://vuejs.bootcss.com/guide/forms.html#lazy) - 取代 `input` 监听 `change` 事件
  - [`.number`](https://vuejs.bootcss.com/guide/forms.html#number) - 输入字符串转为有效的数字
  - [`.trim`](https://vuejs.bootcss.com/guide/forms.html#trim) - 输入首尾空格过滤

- **用法**：

  在表单控件或者组件上创建双向绑定。细节请看下面的教程链接。

- **参考**：

  - [表单控件绑定](https://vuejs.bootcss.com/guide/forms.html)
  - [组件 - 在输入组件上使用自定义事件](https://vuejs.bootcss.com/guide/components-custom-events.html#将原生事件绑定到组件)

## [v-slot](https://vuejs.bootcss.com/api/#v-slot)

- **缩写**：`#`

- **预期**：可放置在函数参数位置的 JavaScript 表达式 (在[支持的环境下](https://vuejs.bootcss.com/guide/components-slots.html#解构插槽-Props)可使用解构)。可选，即只需要在为插槽传入 prop 的时候使用。

- **参数**：插槽名 (可选，默认值是 `default`)

- **限用于**

  - `<template>`
  - [组件](https://vuejs.bootcss.com/guide/components-slots.html#独占默认插槽的缩写语法) (对于一个单独的带 prop 的默认插槽)

- **用法**：

  提供具名插槽或需要接收 prop 的插槽。

- **示例**：

  ```
  <!-- 具名插槽 -->
  <base-layout>
    <template v-slot:header>
      Header content
    </template>
  
    Default slot content
  
    <template v-slot:footer>
      Footer content
    </template>
  </base-layout>
  
  <!-- 接收 prop 的具名插槽 -->
  <infinite-scroll>
    <template v-slot:item="slotProps">
      <div class="item">
        {{ slotProps.item.text }}
      </div>
    </template>
  </infinite-scroll>
  
  <!-- 接收 prop 的默认插槽，使用了解构 -->
  <mouse-position v-slot="{ x, y }">
    Mouse position: {{ x }}, {{ y }}
  </mouse-position>
  ```

  更多细节请查阅以下链接。

- **参考**：

  - [组件 - 插槽](https://vuejs.bootcss.com/guide/components-slots.html)
  - [RFC-0001](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0001-new-slot-syntax.md)

## [v-pre](https://vuejs.bootcss.com/api/#v-pre)

- **不需要表达式**

- **用法**：

  跳过这个元素和它的子元素的编译过程。可以用来显示原始 Mustache 标签。跳过大量没有指令的节点会加快编译。

- **示例**：

  ```
  <span v-pre>{{ this will not be compiled }}</span>
  ```

## [v-cloak](https://vuejs.bootcss.com/api/#v-cloak)

- **不需要表达式**

- **用法**：

  这个指令保持在元素上直到关联实例结束编译。和 CSS 规则如 `[v-cloak] { display: none }` 一起用时，这个指令可以隐藏未编译的 Mustache 标签直到实例准备完毕。

- **示例**：

  ```
  [v-cloak] {
    display: none;
  }
  ```

  ```
  <div v-cloak>
    {{ message }}
  </div>
  ```

  

  不会显示，直到编译结束。

  

## [v-once](https://vuejs.bootcss.com/api/#v-once)

- **不需要表达式**

- **详细**：

  只渲染元素和组件**一次**。随后的重新渲染，元素/组件及其所有的子节点将被视为静态内容并跳过。这可以用于优化更新性能。

  ```
  <!-- 单个元素 -->
  <span v-once>This will never change: {{msg}}</span>
  <!-- 有子元素 -->
  <div v-once>
    <h1>comment</h1>
    <p>{{msg}}</p>
  </div>
  <!-- 组件 -->
  <my-component v-once :comment="msg"></my-component>
  <!-- `v-for` 指令-->
  <ul>
    <li v-for="i in list" v-once>{{i}}</li>
  </ul>
  ```

- **参考**：

  - [数据绑定语法- 插值](https://vuejs.bootcss.com/guide/syntax.html#插值)
  - [组件 - 对低开销的静态组件使用 `v-once`](https://vuejs.bootcss.com/guide/components-edge-cases.html#通过-v-once-创建低开销的静态组件)



# 响应式

# Vue实例

## el属性

    用来指示vue编译器从什么地方开始解析 vue的语法，可以说是一个占位符。

$mount(’#app’) ：手动挂载到id为app的dom中的意思

当Vue实例没有el属性时，则该实例尚没有挂载到某个dom中；
假如需要延迟挂载，可以在之后手动调用vm.$mount()方法来挂载

需要注意的是：该方法是直接挂载到入口文件index.html 的 id=app 的dom 元素上的

```typescript
new Vue({
router,
render: h => h(App)
}).$mount("#app");
```

- 解释一下render函数

```typescript
render: h=> h(App)
//这里的render: h=> h(App)是es6的写法
//暂且可理解为是渲染App组件
// 转换过来就是：  
// render:(function(h){
//  return h(App);
// });
 
```

或者

```typescript
new Vue({
el: '#app',
router,
render: h => h(App)
// render: x => x(App)
// 这里的render: x => x(App)是es6的写法
// 转换过来就是：  暂且可理解为是渲染App组件
// render:(function(x){
//  return x(App);
// });
});

```





## data属性

    用来组织从view中抽象出来的属性，可以说将视图的数据抽象出来存放在data中。

## template属性

    用来设置模板，会替换页面元素，包括占位符。

## methods属性

    放置页面中的业务逻辑，js方法一般都放置在methods中

## render属性

    创建真正的Virtual Dom

## computed属性

    保存计算结果    

## watch属性

    watch:function(new,old){}
    监听data中数据的变化
    两个参数，一个返回新值，一个返回旧值，



# 组件



## 组件的使用步骤

1. 创建组件构造器（调用Vue.extend()方法创建组件构造器）
2. 注册组件（调用Vue.component()方法注册组件）
3. 使用组件（在Vue实例的作用范围内使用组件）



### 创建组件构造器



### 注册组件



### 使用组件



## 局部组件、全局组件

局部组件：只能在一个Vue实例中生效
全局组件：可以在多个Vue实例中生效

局部组件注册：

```vue

```

全局组件注册：

```vue

```



# Prop



# 路由



## vue-router基本使用

1. 安装vue-router
2. 工程下router文件夹
3. 导入vue-router



 **$router和$route的区别**

```typescript
router为VueRouter的实例，相当于一个全局的路由器对象，里面含有很多属性和子对象，例如history对象。。。经常用的跳转链接就可以用this.$router.push，和router-link跳转一样。。。

this.$router.push会往history栈中添加一个新的记录。。详细见vue官方文档https://router.vuejs.org/zh/guide/essentials/navigation.html

 

route相当于当前正在跳转的路由对象。。可以从里面获取name,path,params,query等。。

```





## vue-router嵌套路由



## vue-router参数传递



## vue-router导航守卫

### 全局守卫

前置钩子

后置钩子

### 路由独享守卫

### 组件守卫

## keep-alive

keep-alive是Vue的一个内置组件，可以使被包含的组件保留状态，或避免重新渲染

router-view也是一个组件，其被包含于keep-alive里面时，所有路径匹配到的视图组件会被缓存





# Vuex

```vue
export default new Vuex.Store({
  state: {
	//存放属性
  },
  mutations: {
    //操作属性的具体方法
  },
  actions: {
    //异步操作，然后通过调用mutations修改state
  },
  modules: {
  },
  getters:{
    //类似于组件中的computed
  }

})

```

## state



## mutations



## actions



## modules



## getters



# 脚手架

key

# webpack

官方文档：https://webpack.docschina.org/concepts/

## 认识webpack

本质上，**webpack** 是一个用于现代 JavaScript 应用程序的 *静态模块打包工具*。当 webpack 处理应用程序时，它会在内部构建一个 [依赖图(dependency graph)](https://webpack.docschina.org/concepts/dependency-graph/)，此依赖图对应映射到项目所需的每个模块，并生成一个或多个 *bundle*。

**webpack的核心概念**

```
入口(entry)
输出(output)
loader
插件(plugin)
模式(mode)
浏览器兼容性(browser compatibility)
环境(environment)
```





## webpack的安装



## webpack的起步





## webpack的配置



## loader的使用





## webpack中配置Vue





## plugin的使用





## 搭建本地服务器