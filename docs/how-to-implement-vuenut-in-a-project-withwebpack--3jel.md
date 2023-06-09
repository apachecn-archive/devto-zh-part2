# 如何使用 webpack 在项目 vue CLI 中实现 Vuenut

> 原文：<https://dev.to/luisdanielroviracontreras/how-to-implement-vuenut-in-a-project-withwebpack--3jel>

[![](img/03352cbebd1e31dda8176803ea761729.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IYCGziym--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ASe_6x8t0-knOPoRcEfyfqg.png)

# 首先，vuenut 是什么？

创建这个库是为了在 vuejs (Vuex)的情况下操作和可视化状态管理的模式。主要思想是促进应用程序中数据的开发和维护。

[![](img/b96e06994274289b23288410c9eb63f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LTZK7iNs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AUcUoC5KMNcHGbILdEfZqMw.gif)

# 使用新的 cli 3 de vuejs 启动项目

作为任何开发者，我们都喜欢最新的，所以我们将在 vuejs 的最新版本中实现 vuenut。

首先，如果你不知道什么是 cli de vue，查一下官方文件

### 检视 CLI

是用于快速 Vue.js 开发的完整系统，提供:

*   通过@vue/cli 进行交互式项目搭建。
*   通过@ vue/CLI+@ vue/CLI-service-global 实现零配置快速原型制作。
*   一个运行时依赖项(@vue/cli-service)，它是:
*   可升级；
*   建立在 webpack 之上，具有合理的默认值；
*   可通过项目内配置文件进行配置；
*   可通过插件扩展
*   一个丰富的官方插件集合，集成了前端生态系统中最好的工具。

### 使用 vista CLI 启动项目

在用 vue CLI 初始化一个项目之前，我们必须将它全局安装在我们的机器上，这样它就可以发挥它的魔力了，为此我们执行这个命令(在哪里执行并不重要，因为它是全局安装的)

```
 npm install -g @vue/cli 
```

Enter fullscreen mode Exit fullscreen mode

`-g`命令表示您必须全局安装

* * *

已经安装了 Vue CLI

在要添加项目的文件夹中停止(不是项目文件夹，而是在 vue CLI 启动时创建文件夹之前)。

```
 vue create my-project-vuenut 
```

Enter fullscreen mode Exit fullscreen mode

执行此命令后，CLI 会询问我们一些问题。

*   请选择一个预设:(使用箭头键)

*   请选择预设:默认(babel，eslint)

你必须等待一段时间安装插件和依赖项。

在安装结束时，如果我们进入或多或少具有这种结构的项目，我们会看到一个带有我们的项目名称的新文件夹，在本例中为(my-project-vuenut)。

```
 my-project-vuenut
 - node_modules
 - public
  favicon.ico
  index.html
 - src
 -- assets
 -- components
 App.vue
 main.js
 .gitignore
 package.json
 package-lock 
```

Enter fullscreen mode Exit fullscreen mode

我们将执行命令来初始化本地服务器

```
npm run serve 
```

Enter fullscreen mode Exit fullscreen mode

# 在项目中安装并实现 Vuenut

现在我们已经做好了实现 Vuenut 的一切准备，然后开始工作。

```
npm install vuenut 
```

Enter fullscreen mode Exit fullscreen mode

当在我们的项目中安装完 vuenut 后，我们打开了我们的主文件(main.js ),这样我就可以使用这个库了。

*   添加 vuenut

为了添加 vuenut，我们导入已经安装的依赖项和 css 样式，你需要看起来很棒。

```
import Vuenut from 'vuenut'
import 'vuenut/dist/vuenut.css' 
```

Enter fullscreen mode Exit fullscreen mode

我们让 Vue 使用它来为 Vue 添加全局级别的功能

```
Vue.use(vuenut) 
```

Enter fullscreen mode Exit fullscreen mode

我们已经实现了 vuenut，我们只需要在 App.vue 文件中添加组件。

*   添加组件

为了实现 vuenut，我们打开 App.vue archovo 并在我们的组件中。

```
<vuenut/> 
```

Enter fullscreen mode Exit fullscreen mode

App.vue 文件将保持原样。

```
<template>
<div id="app">
 <vuenut/> <!-- Vuenut component -->
 <img src="./assets/logo.png">
 <HelloWorld msg="Welcome to Your Vue.js App"/>
</div>
</template>
<script>
 import HelloWorld from './components/HelloWorld.vue'
 export default {
  name: 'app',
  components: {
   HelloWorld
  }
}
</script>
<style>
#app {
 font-family: 'Avenir', Helvetica, Arial, sans-serif;
 -webkit-font-smoothing: antialiased;
 -moz-osx-font-smoothing: grayscale;
 text-align: center;
 color: #2c3e50;
 margin-top: 60px;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

准备好了，我们只需要检查我们的本地服务器，看看 vuenut 是如何在这么少的步骤中工作的。

[![](img/5a7c8aad999ec477e115724b13587f29.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n-Jo2I72--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Axe0RAL5jTG7f0ZlSflijbQ.png)

Vuenut 是一个需要 vuex 才能工作的库，所以我们可以在我们的项目中实现 Vuex，并看到 vuenut 为我们提供的所有优势。

# 安装并部署 Vuex

我们在我们的项目上安装了依赖。

```
npm install vuex 
```

Enter fullscreen mode Exit fullscreen mode

安装完成后，我们导入并实现。

```
import Vuex from 'vuex'
Vue.use(Vuex) 
```

Enter fullscreen mode Exit fullscreen mode

我们在项目中已经有 vuex，只是缺少将它添加到 vue 以拥有我们的完整存储。

```
const store = new Vuex.Store({
 state: {
  myName: 'Vuenut'
 },
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们将它添加到我们的实例中

```
new Vue({
 store,
 render: h => h(App)
}).$mount('#app') 
```

Enter fullscreen mode Exit fullscreen mode

App.vue 文件应该是这样的

```
import Vue from 'vue'
import App from './App.vue'
import Vuenut from 'vuenut'
import 'vuenut/dist/vuenut.css'
import Vuex from 'vuex'
Vue.use(Vuex)
Vue.use(Vuenut)
const store = new Vuex.Store({
 state: {
   myName: 'Vuenut'
 },
})
Vue.config.productionTip = false
new Vue({
 store,
render: h => h(App)
}).$mount('#app') 
```

Enter fullscreen mode Exit fullscreen mode

我们已经实现了我们的商店，现在我们要添加 vuenut。

```
<vuenut :store="$store.state"/> 
```

Enter fullscreen mode Exit fullscreen mode

重要的是，在将商店添加到 vuenut 时，让我们将引用放在状态中，这样一切都可以完美地工作

准备好了，我们有了添加了 vuex 的组件，它应该是这样的

[![](img/11392118bf82ff52f03154f1f6f1b763.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1geX2Xij--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Ah39TjuaspkT7trQq4EmUXA.png)

太好了，我们已经实现了 vuenut，并准备在项目中使用，你可以用 vuenut 做一些事情。

*   在运行时更改存储数据。
*   保存存储以备后用，并复制问题或错误。
*   出口商店。json 来导入它或与项目合作伙伴共享它。
*   在运行时编辑整个存储并更改数据的结构。
*   创建在启动应用程序或组合键时执行的函数。库的所有数据都存储在存储器中，从而不必重复任何配置。
*   以及更多的功能。

Vuenut 是一个开源库，我们喜欢创建并不断发展这个社区。

## 链接

*   [Vuenut](https://lusaxweb.github.io/vuenut.org/)
*   [Github](https://github.com/lusaxweb/vuenut)
*   [Vuesax](https://lusaxweb.github.io/vuesax/#/en/home)
*   [vuejs](https://vuejs.org/)
*   [vuex](https://vuex.vuejs.org/en/)
*   [Lusaxweb](http://www.lusaxweb.com.ve/)