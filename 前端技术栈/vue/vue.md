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

# 基本标签



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



# Vue实例

## el属性

    用来指示vue编译器从什么地方开始解析 vue的语法，可以说是一个占位符。

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



## 局部组件、全局组件

局部组件：只能在一个Vue实例中生效
全局组件：可以在多个Vue实例中生效

局部组件注册：

```vue

```

全局组件注册：

```vue

```





# 路由



## vue-router基本使用

1. 安装vue-router
2. 工程下router文件夹
3. 导入vue-router

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