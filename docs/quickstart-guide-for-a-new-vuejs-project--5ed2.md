# 新 Vue.js 项目的快速入门指南

> 原文：<https://dev.to/lobo_tuerto/quickstart-guide-for-a-new-vuejs-project--5ed2>

* * *

有关使用`vue-cli 3.0`的更新文章，请查看使用 vue-cli 3.0 的新 Vue.js 项目的[快速入门指南。](https://lobotuerto.com/blog/quickstart-guide-for-a-new-vuejs-project/)

它还包括一个内容表，便于在那里导航。；)

* * *

# 意见

在我钻研 [Vue.js](https://vuejs.org/) 的短暂时间里，我开始真正地欣赏这个框架及其周围的库。

这本*固执己见的*指南详细介绍了我为一个新的 **Vue.js** 项目打下坚实基础的步骤。

* * *

它包括哪些特别的观点？

嗯，首先:

*   **包管理器:** [纱](https://yarnpkg.com/en/) —快速、可靠、安全的依赖管理。
*   **项目生成工具:** [vue-cli](https://github.com/vuejs/vue-cli/tree/v2.9.3) —用于快速 Vue.js 开发的 cli。
*   **UI 框架:**—材料设计组件框架。
*   **材质图标库:** [谷歌材质图标](https://material.io/icons/) —制作精美，令人愉悦，易于使用。
*   **验证库:**[vue lidate](https://monterail.github.io/vuelidate/)—vue . js 的简单、轻量级的基于模型的验证
*   **Ajax 库:** [Axios](https://github.com/axios/axios) —浏览器和 Node.js 的基于 Promise 的 HTTP 客户端

我发现这些工具和库是高性能的、直观的，并且非常容易使用。

* * *

我为 [Angular](https://angular.io/) 准备了一个类似的堆栈，包括 [Angular Material](https://material.angular.io/) 以及一些用于渲染动态表单、数据表和其他东西的定制组件原语。

我真的很喜欢动态表单实现，它允许用户使用简单的 JSON 规范指定高度可配置的表单。

生成的表单与我们的 [Rails](http://rubyonrails.org/) [JSON API](http://jsonapi.org/) 后端集成得很好。

我计划写一个关于做同样事情的教程，但是这次是用 **Vue.js** 和 **Vuetify** ，但是我跑题了…

# 为成功建立新的 Vue.js 应用程序

在这里，我们将看到如何用 **vue-cli** 设置一个新创建的应用程序，这样我们就可以马上开始使用它了。

## 先决条件

### 安装 Node.js、Yarn 和 vue-cli

*   [如何在 Ubuntu 中安装 Node.js？](https://lobotuerto.com/blog/como-instalar-nodejs-en-ubuntu/)
*   [如何在 Manjaro Linux 中安装 Node.js？](https://lobotuerto.com/blog/how-to-install-nodejs-in-manjaro-linux/)

## 生成新项目

```
vue init webpack my-project 
```

Enter fullscreen mode Exit fullscreen mode

我通常接受所有的默认值，除了包管理器。

我挑**纱**的时候问。

如您所见，我们正在使用 [Webpack](https://webpack.js.org/) 模板。

这里是[官方模板列表。](https://github.com/vuejs/vue-cli/tree/v2.9.3#official-templates)

## 调整 ESLint 规则

将这一行添加到`.eslintrc.js`文件中的`rules`键:

```
'no-multiple-empty-lines': [2, { max: 2 }] 
```

Enter fullscreen mode Exit fullscreen mode

这种变化的原因是，我通常在我的**中的一些元素之间留下两个连续的空行。vue** 组件文件。

例如在`import`段和下面的代码之间。

或者在`<template>`、`<script>`和`<style>`之间。

## 安装项目依赖关系

使用**纱**添加项目依赖:

```
yarn add vuetify material-design-icons vuelidate axios 
```

Enter fullscreen mode Exit fullscreen mode

在编写 **CSS** 规则时，我喜欢拥有 **SCSS /萨斯**的力量。

`--dev`会将依赖项添加到您的`package.json`文件中的`devDependencies`部分:

```
yarn add sass-loader node-sass --dev 
```

Enter fullscreen mode Exit fullscreen mode

## 初始应用配置和设置

要设置**虚拟化**、**谷歌素材图标**和**虚拟化**，您需要将这些行添加到`src/main.js` :

```
import Vuelidate from 'vuelidate'
import Vuetify from 'vuetify'

import 'material-design-icons/iconfont/material-icons.css'
import 'vuetify/dist/vuetify.css'

Vue.use(Vuelidate)
Vue.use(Vuetify) 
```

Enter fullscreen mode Exit fullscreen mode

要查看**验证**的效果，请将您的`src/App.vue`文件更改为:

```
<template>
  <v-app>
    <router-view/>
  </v-app>
</template>

<script>
export default {
  name: 'App'
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

还有你的`src/components/HelloWorld.vue`到:

```
<template>
  <v-content>
    <v-btn>
      Hello!
    </v-btn>
  </v-content>
</template>

<script>
export default {
  name: 'HelloWorld'
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

## 排版

排版的材料设计指南[，](https://material.io/guidelines/style/typography.html)声明**机器人**—**机器人** —是使用的标准字体。

将这个添加到您的`index.html`文件的`<head>`部分:

```
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Roboto:300,400,500,700,400italic"> 
```

Enter fullscreen mode Exit fullscreen mode

## 看一看

从:
开始您的项目

```
yarn dev 
```

Enter fullscreen mode Exit fullscreen mode

并访问:`http://localhost:8080`。

就是这样！

过得好。:)

# 链接

*   [重新思考 Vue.js 的验证](https://www.monterail.com/blog/2016/rethinking-validations-for-vue-js)