# Vue.js 快速介绍

> 原文：<https://dev.to/adityasridhar/a-quick-introduction-to-vuejs-1pp8>

**本帖原载于[medium.freecodecamp.org](https://medium.freecodecamp.org/a-quick-introduction-to-vue-js-72937ee8880d)T3】**

你对前端开发感兴趣吗？

如果是这样，那么这篇文章是给你的！

要开始使用 Vue.js，你需要了解 HTML、CSS、JavaScript、火箭科学、核物理、量子理论等…

嗯（表示踌躇等）😱…

不完全是。仅仅是 HTML、CSS 和 JavaScript 就足以让你开始使用 Vue.js😃。

这篇文章将介绍如何轻松创建一个简单的 Vue.js 应用程序，并解释创建的应用程序的文件夹结构。此外，我们将创建一个简单的 Vue.js 组件。

所以让我们开始吧。

# 先决条件

## 如果 Node.js 不存在，请安装它

您需要 Node.js，因为 Vue 所需的库是使用节点包管理器(npm)下载的。参考[https://nodejs.org/en/](https://nodejs.org/en/)安装 Node.js

## 安装 CLI 视图

使用以下命令安装 Vue CLI:

```
npm install -g @vue/cli 
```

Vue CLI 有助于轻松创建 Vue.js 项目。虽然它被称为 CLI，但它也有一个 UI 来创建我将在下面介绍的项目😄。

# 创建项目

在命令提示符下键入以下命令:

```
vue ui 
```

这将在浏览器中打开以下屏幕:

[![Page1](img/2ed251c5164bba078243c477eb334e92.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OcuB5Ef3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/97ex3jxyyd5jp5oecsyb.jpg)

点击**创建。**然后输入创建项目的目的地。

[![Page2](img/e28577ab41aa38f2d7d406ff7f025ff0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--d3MH1YR1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/srl52orisqauxs7gj6c1.jpg)

然后点击**在这里创建一个新项目。**

[![Page3](img/b0174e97c749aa0f1f608bb2a91a4274.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DhWFn9v4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gsshhomk005fthaul3us.jpg)

这将打开以下屏幕:

[![Page4](img/e170602108811a136331262a04b42eb8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--id8PE8ki--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dewse0lpbt7rxv1btdod.jpg)

进入项目文件夹 **sample-vue-app** ，点击**下一步。**

在下一个屏幕中，选择**默认预设，**如下图所示。对于这个帖子，**默认预设**是最简单的开始。

[![Page5](img/e8feefd6c82a7db9c5d0e1b59ade4f1c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--csqokDXx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wm8h71zzqwzoj9xenfhx.jpg)

最后，点击**创建项目**

为了测试项目是否设置良好，进入项目文件夹并使用以下命令启动应用程序:

```
cd sample-vue-app
npm run serve 
```

应用程序在本地主机 8080 上运行。下图显示了应用程序在浏览器中的外观:

[![Output](img/46c1c5b17ff5d52a7de0d0a2a68deb82.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xE92YuF6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yp10u3a7frn9szeabgvj.jpg)

恭喜你，你已经创建了你的第一个 Vue.js 项目！😄

但是等一下，这个项目有很多自动创建的文件和文件夹。

**真的有必要知道这些文件是什么意思吗？**

当代码以奇怪的方式运行时，了解它们肯定会有所帮助，这在开发人员的世界中经常发生。😈

# 应用程序文件夹结构

[![Folder Structure](img/d0f3a9bd3690863d349ae18ffc3239a1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--enFk0cV_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/86ad49j03ncauit49dvi.jpg)

1.  **package.json:** 这个文件拥有所有的节点依赖关系。
2.  **public/index.html:** 这是应用程序启动时加载的第一个文件。此外，这个文件有下面的代码片段`<div id=”app”></div>.`所有组件都加载在这个 id 为 **app 的 div 中。**
3.  **src/main.js** :这是创建 Vue 实例的文件。这个文件有下面的代码片段`new Vue({ render: h => h(App)}).$mount(‘#app’).`这个代码片段告诉我们 **App** 组件需要被加载到一个 id 为 **app** 的元素中(也就是 div 元素)。
4.  **src/App.vue:** 该文件对应于 **App** 组件，该组件充当所有其他组件的容器。它有一个用于 **HTML** 代码的**模板**，有一个用于 **JavaScript** 代码的**脚本**，还有一个用于 **CSS 的**样式**。**
5.  src/components :这是你开发的所有组件的存放地。所有组件都有一个模板、脚本和样式标签，分别用于 HTML、JavaScript 和 CSS 代码。
6.  **dist** :这是存储构建文件的文件夹。要获得这个文件夹，运行命令`npm run build`。当这个命令运行时，代码被缩小、打包并存储在 dist 文件夹中。该文件夹中的代码通常会部署到生产环境中。

# 创建您的第一个组件

在 **src/components** 内部创建一个名为 **First.vue** 的文件。该文件将包含 HTML、JavaScript 和 CSS。让我们一个接一个地添加它们。本部分所有代码均属于 **First.vue** 文件。 **First.vue** 将是我们的 **Vue 组件**

## 半铸钢ˌ钢性铸铁(Cast Semi-Steel)

```
<style scoped>
.demo {
    background-color: cyan;
}
</style> 
```

这是基本的 CSS。`<style scoped>`中的参数**作用域**意味着 CSS 仅适用于该组件。

## Java Script 语言

```
<script>
export default {
    name: 'First',
    props: {
    msg: String
    }
}
</script> 
```

**name** 参数表示第一个为**的组件的名称。**

**props** 参数表示该部件的**输入**。这里我们将有一个称为 **msg** 的输入，它属于类型 **String。**

## 超文本标记语言

```
<template>
    <div class="demo">
    <h1>{{ msg }}</h1>
    </div>
</template> 
```

`{{msg}}`是在 **HTML** 代码中访问 **Vue** 组件的输入参数的方式。

## 第一个组件的完整代码

首先是文件**的内容，vue:**

```
<template>
    <div class="demo">
    <h1>{{ msg }}</h1>
    </div>
</template>

<script>
export default {
    name: 'First',
    props: {
    msg: String
    }
}
</script>

<style scoped>
.demo {
    background-color: cyan;
}
</style> 
```

太好了，组件现在已经创建好了！😃

现在使用`npm run serve`运行应用程序，您将看到以下屏幕:

[![Page6](img/46c1c5b17ff5d52a7de0d0a2a68deb82.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xE92YuF6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yp10u3a7frn9szeabgvj.jpg)

等一下，这不是和之前的输出一样吗。我们刚刚创建的组件在哪里？

问题是，我们创建了组件，但我们从未在任何地方使用它。现在让我们使用这个组件。😄

# 使用组件

让我们将这个组件添加到主 **App** 组件中。以下是 **App.vue:** 的更新代码

```
<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <First msg="First Component"/>
  </div>
</template>
<script>
import HelloWorld from './components/HelloWorld.vue'
import First from './components/First.vue'
export default {
  name: 'app',
  components: {
    First
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

1.  **首先**组件需要导入到 **App** 组件中。这是通过命令`import First from ‘./components/First.vue’`完成的
2.  接下来，在 JavaScript 中，我们需要提到**应用**组件将使用**第一个**组件。这是由代码片段`components: {First}`完成的
3.  最后，我们需要使用 **App** 组件中的**第一个**组件。这是在 HTML 模板中使用代码片段`<First msg=”First Component”/>`完成的
4.  这里 **msg** 是**第一个**组件的输入参数， **msg** 的**值**是从 **App** 组件发出的

现在使用`npm run serve`运行应用程序，您将看到下面的输出:

[![Final Output](img/68073a22b5136aecbb585628afb246dd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jZG3U_ix--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ij5j1rw8w053y0vsueqb.jpg)

# 密码

[点击此处](https://github.com/aditya-sridhar/vuejs-blog-demo-part1)从 GitHub 库获取此处显示的代码。GitHub repo 有关于克隆和运行代码的说明。

[点击此处](https://aditya-sridhar.github.io/vuejs-blog-demo-part1/)查看应用程序的外观。它是使用 Github 页面部署的。

# 恭喜😃

现在你已经成功构建了你的第一个 Vue.js App。您还学习了如何创建一个非常简单的组件。在我的下一篇关于 Vue.js 的文章中，我将介绍更多的概念。敬请期待！

# 参考

view . js:[https://vuej . org/v2/guide/](https://vuejs.org/v2/guide/)

CLI 视图:https://CLI . vuej . org/guide/

### 请随时在 [LinkedIn](https://www.linkedin.com/in/aditya1811/) 与我联系，或者在 [twitter](https://twitter.com/adityasridhar18) 关注我。

### 如果你喜欢这篇文章，你可以去我的网站[https://adityasridhar.com](https://adityasridhar.com)看看其他类似的文章