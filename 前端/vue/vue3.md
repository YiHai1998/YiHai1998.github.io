# Vue3

main.js

```typescript
//程序的主入口文件,ts文件，是main.ts
//引入createApp函数，创建对应的应用，产生应用的实例对象
import { createApp } from 'vue'

//引入App组件(所有组件的父级组件)
import App from './App.vue'

//创建App应用返回对应的实例对象，调用mount方法进行挂载，所有组件产生完毕后，会在index.html中的div渲染
createApp(App).mount('#app')
```

index.html

```html
  <body>
    <noscript>
      <strong>We're sorry but <%= htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
```



App.vue

```typescript
<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <!-- 使用这个子级组件 -->
  <HelloWorld msg="Welcome to Your Vue.js + TypeScript App"/>
</template>

<script lang="ts">
// 这里可以使用ts代码

// defineComponent函数，目的是定义一个组件，内部可以传入一个配置对象
import { defineComponent } from 'vue';
// 引入一个子级组件
import HelloWorld from './components/HelloWorld.vue';

// 暴露一个定义好的组件
export default defineComponent({
  // 当前组件的名字
  name: 'App',
  // 注册组件
  components: {
    // 注册一个子级组件
    HelloWorld
  }
});
</script>

<style>

</style>
```







测试代码，setup是组合api第一个测试的函数

```typescript
<template>
  <div>哈哈，我是溪云</div>
  <h1>{{number}}</h1>
</template>

<script lang="ts">
// 这里可以使用ts代码

// defineComponent函数，目的是定义一个组件，内部可以传入一个配置对象
import { defineComponent } from 'vue';
// 引入一个子级组件
import HelloWorld from './components/HelloWorld.vue';

// 暴露一个定义好的组件
export default defineComponent({
  // 当前组件的名字
  name: 'App',
  setup(){
    const number = 10
    return {
      number
    }
  }

});
</script>
```



## 安装

```bash
npm init @vitejs/app <project-name>
cd <project-name>
npm install
npm run dev
```



## 路由

安装

```bash
npm install vue-router@4
```

创建一个router.js插件

```js
import {createRouter, createWebHashHistory, createWebHistory} from "vue-router"

// 1. 定义路由组件， 注意，这里一定要使用 文件的全名（包含文件后缀名）
import home from './views/home.vue'
import train from './views/train/train.vue'
import result from './views/train/result.vue'

// 2. 定义路由配置
const routes = [
    {
        path: "/home", component: home
    },
    { path: "/train", component: train },
    { path: "/result", component: result },
];

// 3. 创建路由实例
const router = createRouter({
    // 4. 采用hash 模式
    history: createWebHashHistory(),
    // 采用 history 模式
    // history: createWebHistory(),
    routes, // short for `routes: routes`
});

export default router
```

在main.js中引入

```js
import { createApp } from 'vue'
import App from './App.vue'

import router from './router.js'

const app = createApp(App)

app.use(ElementPlus)
app.use(router) // 使用router实例
app.mount('#app')
```

应用

```vue
<template>
  <div>
    <button @click="show('home')">主页</button>
    <button @click="show('train')">训练</button>
    <button @click="show('result')">结果</button>
  </div>
</template>

<script lang="ts">

export default ({
  name: 'Home',
  props: {
    msg: {
      type: String,
      required: true
    }
  },

  methods: {
    show(index) {
      if(index == 'home') {
        this.$router.push('/')
      }
      if(index == 'train') {
        this.$router.push('train')
      }
      if(index == 'result') {
        this.$router.push('/result')
      }
    }
  },

})
</script>

<style scoped>
a {
  color: #42b983;
}
label {
  margin: 0 0.5em;
  font-weight: bold;
}

code {
  background-color: #eee;
  padding: 2px 4px;
  border-radius: 4px;
  color: #304455;
}
</style>
```

## axios

安装

```bash
npm install axios --save
```



```js
import { createApp } from 'vue'
import App from './App.vue'
import axios from 'axios'

const app = createApp(App)
// 全局挂载axios
app.config.globalProperties.$axios = axios;

app.mount('#app')
```



具体使用

```vue
  methods: {
    getEposides() {
      var _this = this;
      axios.get("http://localhost:8000/").then(function (response) {
        console.log(response.data)
        _this.percentage = response.data
      })
    },
  },
```







# 遇到的问题

.map() is not a function

后端传的是对象，前端需要接收的是数组

此时需要将对象转换为数组

```js
    getResult() {
      var _this = this;
      axios.get("http://localhost:8099/result").then(function (response) {
        console.log(response.data)
        let result = Array.of(response.data)
        console.log(result)
        _this.tableData = result
      })
    },
```

