---
title: Vue快速上手-未完待续
top: false
cover: false
toc: true
mathjax: false
img: 'https://pic.imgdb.cn/item/65211657c458853aef4b1cdd.png'
summary: 学习vue时的笔记
tags: 
 - vue
 - web
categories: web
abbrlink: 249
date: 2023-10-07 16:24:57
updated: 2023-10-07 16:24:57
author:
coverImg:
password:
---
# 基础

- 本地开发必需环境: [node.js](https://nodejs.org/en)
- 学习Vue前建议先学习: `html`, `css`, `JavaScript`, Vue的本质是**渐进式
  JavaScript 框架**, 所以打好js的基础是十分重要的。
- 关于Vue的一切具体请参考:[官方文档](https://v2.cn.vuejs.org/)

## 1.起步

### 1.1引入

通过最简单的script标签方式引入Vue：

```html
<!-- 开发环境版本，包含了有帮助的命令行警告 -->
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>

<!-- 生产环境版本，优化了尺寸和速度 -->
<script src="https://cdn.jsdelivr.net/npm/vue@2"></script>
```

### 1.2响应式数据与插值表达式

```html
<div id="app">
    <p>{{ title }}</p> 
    <p>{{ message }}</p>
    <p>{{ output() }}</p>
</div>
```

插值表达式中也可以做简单的数学运算与逻辑运算

```html
<div id="app">
    <p>{{ 1 + 2 + 3 }}</p> 
    <p>{{ 1 > 2 ? '对' : '错' }}</p>
</div>
```

```js
const vm = new Vue({
    // 响应式数据与插值表达式
    el:'#app',
    data(){
        return{
            title:"Vue",
            message:"Hello"
        }
    },
    // methods属性
    methods:{
        output(){
            return '标题为' + this.title + ',' + '内容为' + this.message
        }
    },
})
```

看起来这跟渲染一个字符串模板非常类似，但是 Vue 在背后做了大量工作。现在数据和 DOM 已经被建立了关联，所有东西都是**响应式的**。我们要怎么确认呢？打开你的浏览器的 JavaScript 控制台 (就在这个页面打开)，并修改 `app.message` 的值，你将看到上例相应地更新。

注意我们不再和 HTML 直接交互了。一个 Vue 应用会将其挂载到一个 DOM 元素上 (对于这个例子是 `#app`) 然后对其进行完全控制。那个 HTML 是我们的入口，但其余都会发生在新创建的 Vue 实例内部。

### 1.3计算属性

```html
<div id="app">
    <p>{{ outputContent }}</p>
</div>
```

```Vue
computed:{
	outputContent(){
		console.log('computed执行了')
		return '标题为' + this.title + ',' + '内容为' + this.message
	}
},
```

与 `methods`属性不同的是计算属性具有**缓存性**，如果响应式数据没有变就不会多次计算。

- 注意在插值表达式中没有 `()`

### 1.4侦听器

```vue
watch:{
	title(newValue, oldValue){
		console.log(newValue, oldValue)
}
}
```

 监听对象必须是 `响应式数据`，数据有变化就可以做一些相应操作，比如调用库、发请求等。

### 1.5指令

#### 1.5.1内容指令

```html
<p v-text="content"></p> <!-- 不渲染html标签 -->
<p v-html="content"></p> <!-- 渲染html标签 -->
```

#### 1.5.2渲染指令

```html
<p v-for="item in arr">{{ item }}</p> <!-- 循环渲染 -->
<p v-show="bool">内容</p> <!-- 显示/隐藏 -->
```

#### 1.5.3属性指令

```html
<p v-bind:title="message"></p> <!-- 用响应式数据message绑定p标签的title属性 -->
<p :title="message"></p> <!-- 语法糖 -->	
```

#### 1.5.4事件指令

```html
<button v-on:click="output"></button> <!-- 给button绑定click事件 -->
<button @click="output"></button> <!-- 语法糖 -->
```

#### 1.5.5表单指令

```html
<!-- 可以实现双向数据绑定 -->
<input type="text" v-model="inputValue">
<p v-text="inputValue"></p>
```

## 2.Vue CLI脚手架

### 2.1安装

使用npm进行安装

```shell
npm i @vue/cli -g
```

### 2.2创建项目

```shell
vue create 项目名
```

### 2.3运行项目

```shell
npm run serve
```

### 2.4打包项目

```shell
npm run build
```

## 3.组件化开发

### 3.1组件

是用来封装页面部分功能的一种方式，页面由各种组件构成，而大组件中又由许多小组件构成，有效提高代码的可维护性和复用度。

每个 `.vue`单文件组件中都包含结构、样式、逻辑的封装，即包含 `template`,`script`,`style`三部分。

以CLI生成的示例如下：

```vue
<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    HelloWorld
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

### 3.2组件通信

#### 3.2.1父传子

父组件向子组件传值，在子组件中设置 `props`

```vue
<!--子组件-->
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>props中的count: {{ count }}</p>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String,
    count: {
      type: [String, Number], <!--数据类型是string或number-->
      default: 100,
      require: true <!--代表这个值是必选的-->
    }
  }
}
</script>
```

```vue
<!--父组件-->
<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld 
     msg="Welcome to Your Vue.js App"
     count=10
    ></HelloWorld>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    HelloWorld
  }
}
</script>
```

也可以结合 `vue`的指令绑定属性:

```vue
    <HelloWorld 
     msg="Welcome to Your Vue.js App"
     :count="parentCount"
    ></HelloWorld>
```

并在 `script`中设置响应式数据：

```vue
<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    HelloWorld
  },
  data(){
    return{
      parentCount: 1000
    }
  }
}
</script>
```

#### 3.2.2子传父

在子组件中设置自定义事件，`$emit()`用于触发自定义事件。

```vue
<!--子组件-->
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>props中的count: {{ count }}</p>
    <button @click="handler">点我</button>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String,
    count: {
      type: [String, Number],
      default: 100,
      require: true
    }
  },
  data(){
    return{
      childCount: 0
    }
  },
  methods:{
    handler(){
      this.childCount++
      this.$emit("change-on-count", this.childCount)
    }
  }
}
</script>
```

```vue
<!--父组件-->
<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <h1>父组件中接收到的数据{{ childData }}</h1>
    <HelloWorld 
     msg="Welcome to Your Vue.js App"
     :count="parentCount"
     @change-on-count="handler2"
    ></HelloWorld>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    HelloWorld
  },
  data(){
    return{
      parentCount: 1000,
      childData: 0
    }
  },
  methods:{
    handler2(childCount){
      this.childData = childCount
    }
  }
}
</script>
```

### 3.3组件插槽

在子组件中预留一部分开放区域用于功能实现

#### 3.3.1普通插槽

```vue
<!--子组件-->
<template>
  <div class="hello">
    <slot>默认内容</slot>
    <h1>{{ msg }}</h1>
    <p>props中的count: {{ count }}</p>
    <button @click="handler">点我</button>
  </div>
</template>
```

```vue
<!--父组件-->
    <HelloWorld 
     msg="Welcome to Your Vue.js App"
     :count="parentCount"
     @change-on-count="handler2"
    >这是插槽里的内容</HelloWorld>
```

- 如果在父组件标签中不写插槽内容则显示为子组件中的默认内容

#### 3.3.2 具名插槽

```vue
<!--子组件-->
<template>
  <div class="hello">
    <slot name="插槽1">默认内容1</slot>
    <h1>{{ msg }}</h1>
    <p>props中的count: {{ count }}</p>
    <button @click="handler">点我</button>
    <slot name="插槽2">默认内容2</slot>
  </div>
</template>
```

```vue
<!--父组件-->
<HelloWorld 
     msg="Welcome to Your Vue.js App"
     :count="parentCount"
     @change-on-count="handler2"
    >
    	这是无效内容
        <template v-slot:插槽1>插槽1内容</template>
    	<template #插槽2>插槽2内容</template>
</HelloWorld>
```

#### 3.3.3 作用域插槽

```vue
<!--子组件-->
<template>
  <div class="hello">
    <slot name="插槽1">默认内容1</slot>
    <h1>{{ msg }}</h1>
    <p>props中的count: {{ count }}</p>
    <button @click="handler">点我</button>
    <slot name="插槽2" :childCount="childCount">默认内容2</slot>
  </div>
</template>
```

```vue
<!--父组件-->
<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <h1>父组件中接收到的数据{{ childData }}</h1>
    <HelloWorld 
     msg="Welcome to Your Vue.js App"
     :count="parentCount"
     @change-on-count="handler2"
    ><template v-slot:插槽2="dataObj">子组件中的childCount{{ dataObj.childCount }}</template></HelloWorld>
  </div>
</template>
```

- 注意父组件中返回的是 `dataObj`对象，可以通过 `.`进行访问或者 `ES6解构`的方式进行：

```vue
<template v-slot:插槽2="{{ childCount }}">子组件中的childCount{{ childCount }}</template>
```
