# 将 Bootstrap 4 与 Vue 配合使用

> 原文：<https://dev.to/techiediaries/using-bootstrap-4-with-vue-4fl7>

在本文中，我将向您展示如何将最新版本的 Bootstrap 与您的 Vue 应用程序集成。

Bootstrap 4 有许多新特性，例如使用 Flexbox、ES6 模块和一个新的卡组件(您可以使用它来轻松创建基于卡的布局，例如著名的砖石布局，无需 JavaScript/jQuery 插件或复杂的算法)等。

您可以使用 Bootstrap 4 将 mobile first 样式添加到您的 Vue 应用程序中，而无需重新发明轮子，也无需掌握深厚的 CSS 知识，尤其是在您需要创建复杂且响应迅速的布局时。

Bootstrap 4 需要 jQuery 和 popper . js。jQuery 是一个 DOM 库，它可以直接操作 DOM，而 Vue 使用虚拟 DOM 方法，因此您会希望避免在 Vue 应用程序中使用 jQuery，但这意味着您将无法使用许多需要 jQuery 的 Bootstrap 4 组件。

不要担心，尽管社区已经提供了一些将 Bootstrap 4 与 Vue 集成在一起的包，而没有求助于 jQuery。让我们看看最受欢迎的图书馆

因此，让我们从第一个库开始，您可以使用它将您的 Vue 应用程序与 Bootstrap 4 集成

## [自举视图](https://bootstrap-vue.js.org/)

[![](img/5108f1fd9a8fc4348ed3a56cbdc2709a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Co9qOmZP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://screenshotscdn.firefoxusercontent.cimg/030f06e4-c4cc-4e09-aa12-e8843bc4192a.png)

前往您的终端，在您的 Vue 项目中导航，然后运行以下命令来安装`bootstrap-vue`

```
npm install bootstrap-vue bootstrap --save 
```

Enter fullscreen mode Exit fullscreen mode

`bootstrap-vue`需要引导 CSS 文件，所以你也需要安装`bootstrap`。

接下来你需要在你的 Vue 应用入口点中启用 *VueBootstrap* 插件。

```
import Vue from 'vue'
import BootstrapVue from "bootstrap-vue"
import App from './App.vue'
import "bootstrap/dist/css/bootstrap.min.css"
import "bootstrap-vue/dist/bootstrap-vue.css"

Vue.use(BootstrapVue)

new Vue({
  el: '#app',
  render: h => h(App)
}) 
```

Enter fullscreen mode Exit fullscreen mode

你也可以使用这两个模板来搭建你的 Vue 项目 [webpack-simple](https://github.com/bootstrap-vue/webpack-simple) 和 [webpack](https://github.com/bootstrap-vue/webpack) ，它们集成了开箱即用的`bootstrap-vue`。

例如，这就是如何使用 Vue CLI 生成基于`bootstrap-vue/webpack-simple`的新 Vue 项目。

```
vue init bootstrap-vue/webpack-simple my-project
cd my-project 
npm install 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以使用不同的[引导 4 组件](https://bootstrap-vue.js.org/docs/components/)

```
<template>
<div>
  <b-button>This is a button</b-button>
  <b-button href="#">This is a link</b-button>
</div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

使用 Bootstrap-Vue 是将 Bootstrap 4 与 Vue 2 应用程序集成的推荐方式，因为它为需要 jQuery 的 Bootstrap 组件提供了 Vue 组件。