# 为 Vuejs 实施 Vuesax 新框架

> 原文：<https://dev.to/luisdanielroviracontreras/implement-vuesax-new-framework-for-vuejs-13gh>

[![Vuesax logo](img/5ae2040014a4fb8a5f9fc2cceee9ffc9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zhhbvHcL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/22gpstrydiklaeksom7u.png)

Vuesax 是一个基于 vuejs 的组件框架，这是一个从头开始设计的可增量采用的框架。

该框架致力于促进应用程序的开发，在不删除必要功能的情况下改进应用程序的设计。我们希望所有的组件在颜色、形状和设计方面都是独立的，以获得我们喜欢的所有前端的自由，但又不会失去创作和生产的速度。

## 安装

本安装教程适用于 vuesax 与以下设备的配合使用:

*   网络包
*   CLI 3 视图
*   NPM
*   节点. js

如果你打算在 CDN 的一个项目中实现 vuesax，只需将脚本的导入放在 vuejs 之后，就没有什么好解释的了

## 创建项目

首先，我们需要一个文件夹来存放我们的伟大项目，但由于我们将使用 Vue CLI，所以我们不需要创建它，我们在创建项目时会自动创建它
因此，我们将使用 Vue CLI 3 启动一个项目，当然，如果我们的计算机上已经全局安装了它，如果没有，请执行此脚本

```
npm install -g @vue/cli 
```

Enter fullscreen mode Exit fullscreen mode

已经在全球范围内安装了，我们位于所有项目所在的文件夹中(我们不创建项目文件夹，Vue CLI 会为我们创建)

我们执行脚本来启动一个 Vue CLI 项目

```
vue create my-project 
```

Enter fullscreen mode Exit fullscreen mode

准备好了，我们有了一个包含 vuejs
项目的所有必要文件的文件夹，然后我们进入这个文件夹，在这个例子中它被称为 my oriject(在`vue create`之后是项目的名称)

在项目中启动我们的测试服务器，看到一切顺利，我们执行

```
npm run serve 
```

Enter fullscreen mode Exit fullscreen mode

过一会儿，我们的 Vue CLI 将启动我们的服务器，几乎总是服务器路径是`localhost:8080`

[![Vuesax-init-vue](img/2d4b6c4701eb050f7401ea6c5b13ee26.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xOqUduz0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7j1c06n1o2y1ho2vsekv.png)

准备好了，我们已经有了带有 vuejs 的活动服务器和带有 vuesax 的项目所需的一切

## 安装 Vuesax

现在我们已经准备好了项目，所有必要的东西(Vue CLI)都为我们做好了，我们将使用命令
添加 Vuesax

```
npm install vuesax 
```

Enter fullscreen mode Exit fullscreen mode

我们必须等待依赖项被安装到我们的项目中

现在完成安装后，应用程序中的任何地方都只缺少使用的实现

我们打开主文件，在本例中是

*   my-project/src/main.js

```
import Vue from 'vue'
import Vuesax from 'vuesax' //import dependency
import 'vuesax/dist/vuesax.css' // import css style

Vue.use(Vuesax) // implement Vuesax throughout the application 
```

Enter fullscreen mode Exit fullscreen mode

文件应该是这样的

```
import Vue from 'vue'
import App from './App.vue'
import Vuesax from 'vuesax'
import 'vuesax/dist/vuesax.css'

Vue.use(Vuesax)

Vue.config.productionTip = false

new Vue({
  render: h => h(App)
}).$mount('#app') 
```

Enter fullscreen mode Exit fullscreen mode

我们的应用程序中已经有了 vuesax 的所有组件和功能

## 添加一个组件

我们在整个应用程序中已经有了 vuesax，但是现在我们在模板中添加一个组件。

我们将添加一个按钮并替换文件`my-project/src/components/HelloWorld.vue`中的链接

文件应该只清除模板
的一部分

```
<template>
  <div class="hello">
   <vs-button vs-type="filled">Primary</vs-button>
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

实现之后，让我们回顾一下我们的`vs-button`组件是什么样子的

[![Vuesax-implement-vue-gif](img/2f439c4b4c0ad9351e07dd0b5311a95e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RqU5K483--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qlyek9jdujlubcd5zqzv.gif)

准备好了我们的 Vuesax 应用程序正在运行，现已实施

非常感谢您花时间阅读关于 Vuesax 的信息

## 链接

*   [Vuesax](https://lusaxweb.github.io/vuesax/)
*   [Github Vueasx](https://github.com/lusaxweb/vuesax)
*   vista CLI
*   [网络包](https://webpack.js.org/)
*   NPM·维萨克斯