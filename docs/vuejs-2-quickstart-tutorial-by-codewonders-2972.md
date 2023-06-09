# @code_wonders 的 Vue.js 快速入门教程

> 原文：<https://dev.to/code_wonders/vuejs-2-quickstart-tutorial-by-codewonders-2972>

[![Vue JS Logo](img/4ae56adfcc928d87d8e948f010983d33.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BIsD_kQy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://scotch.io/wp-content/uploads/2015/08/vuejs-cover.png) 
**Vue.js 快速入门教程 by @code_wonders**

什么是 Vue.js
Vue 是一个渐进式的 JavaScript 框架，专注于构建用户界面。由于它只在**“视图层”**中工作，所以它没有对中间件和后端做任何假设，因此可以很容易地集成到其他项目和库中。Vue.js 为视图层提供了很多功能，可以用来构建强大的单页面 webapps。在下面，您可以找到功能列表:

*   反应界面
*   声明性渲染
*   数据绑定
*   指令
*   模板逻辑
*   成分
*   事件处理
*   计算属性
*   CSS 过渡和动画
*   过滤

> Vue.js 2 核心库非常小(只有 17 kB)。这确保了通过使用 Vue.js 添加到您的项目中的开销最小，并且您的网站加载速度很快。

Vue.js 网站位于:[https://vuejs.org/](https://vuejs.org/)

**如何使用 Vue.js**
在你的 web 项目中包含 Vue.js 有不同的方法:

*   通过在 HTML 文件中包含标签`<script>`来使用 CDN
*   使用节点程序包管理器安装(NPM)
*   使用凉亭安装
*   使用 Vue-cli 设置您的项目

接下来，我们将进入 Vue-cli 来设置一个新项目并安装 Vue.js 2 库。

**使用 Vue-cli**

首先，我们需要安装 Vue-cli。commend line 接口作为 NPM 软件包提供。确保 Node.js 和 npm 命令在您的系统上可用，并使用以下命令在您的本地系统上全局安装 Vue CLI:

`$ npm install -g vue-cli`

成功安装客户端后，vue 命令变得可用。现在，我们可以通过以下方式使用此命令来启动项目:

`$ vue init webpack learningVue`

我们告诉 vue 启动一个新项目，并使用 webpack 模板。我们还给这个项目命名为 **learningVue** 。执行该命令会在命令行上产生一些问题，如下面的屏幕截图所示:

该项目创建在文件夹 **learningVue** 中。使用以下命令切换到该目录:

`$ cd learningVue`

再次使用 npm 开始安装依赖项:

`$ npm install`

完成软件包安装后，您可以通过以下方式使用 npm 在开发模式下启动 web 服务器:

`$ npm run dev`

这将在端口 8080 上启动服务器，应用程序输出将自动显示在浏览器中:

[![Vue JS Logo](img/e9a5f9c445fdeef010d9d26de73734ab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eKU0H8dl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AGuIRPERP01oedu0iIAtqyA.png)

稍后，如果您想为生产构建，您可以使用下面的命令。在这种情况下，将创建一个 dist 文件夹，其中包含生产部署所需的文件。

`$ npm run build`

**项目结构**
让我们来看看文件夹 *learningVue* 中的初始项目结构:

在项目根文件夹中，您可以找到文件和文件夹。让我们检查最重要的几个。`package.json`文件包含项目的所有依赖项。通过使用命令 npm install，我们已经确保在`package.json`中列出的依赖项被安装到项目的 node_modules 文件夹中。
T3![Vue JS Logo](img/d790f5ed434868b55552e0c1043a18f4.png)T5】

文件`index.html`包含以下 HTML 代码:

T2`<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
vueapp01
</head>
<body>
<div id="app"></div>
<!-- built files will be auto injected -->
</body>
</html>`

这个文件是应用程序的起点。注意，在 body 部分中有一个`<div>`元素，它的 id 属性被设置为 string app。该元素用作 Vue.js 生成的输出的占位符。

接下来看看文件夹`src`中的文件`main.js`。这是初始化 Vue 应用程序的地方:

T2`import Vue from 'vue'
import App from './App'
new Vue({
el: '#app',
template: '<App/>',
components: { App }
})`

在该文件的顶部，您可以找到两条导入语句:

*   从“Vue”导入 Vue:Vue 是框架的主类
*   从'导入应用程序。/App': App 是我们应用程序的根组件

通过使用 new 关键字，创建了主框架类 Vue 的一个新实例。构造函数将一个包含三个属性的对象作为参数:
el:通过将字符串#app 赋给这个属性，我们定义了 Vue 应用程序的输出应该呈现给 index.html 的`<div id="app"></div>`元素。

*   模板:模板包含用于生成 Vue.js 应用程序输出的 HTML 代码。

*   组件:模板中使用的 Vue.js 组件列表。
    模板只包含一个元素:`<App/>`。当然这不是一个标准的 HTML 元素。这是分配给应用程序组件的元素。为了能够在模板中使用`<App/>`, App 组件也列在分配给 components 属性的对象中。

因此，让我们看看文件`App.vue`中的应用程序组件实现的内部内容:

`<template>
<div id="app">
<img src="./assets/logo.png">
<hello></hello>
</div>
</template>`
`<script>
import Hello from './components/Hello'
export default {
name: 'app',
components: {
Hello
}
}
</script>`
T2】

与每个`Vue.js 2`单文件组件一样，应用程序实现分为三个部分:

*   :组件的模板代码
*   :组件的脚本代码
*   :组件的 CSS 代码

让我们关注前两个部分模板和脚本。脚本部分默认导出一个对象，声明名为 app 的组件。同样，components 属性用于声明 App 需要另一个组件(Hello)。这个子组件在 app 的模板代码中使用，在文件夹组件的 hello.vue 文件中实现。为了能够在应用程序中使用 Hello 组件，还需要在脚本部分的顶部包含相应的导入语句。

组件 Hello 的实现如下所示:

`<template>
<div class="hello">
<h1>{{ msg }}</h1>
<h2>Essential Links</h2>
<ul>
<li><a href="https://vuejs.org" target="_blank">Core Docs</a></li>
<li><a href="https://forum.vuejs.org" target="_blank">Forum</a></li>
<li><a href="https://gitter.im/vuejs/vue" target="_blank">Gitter Chat</a></li>
<li><a href="https://twitter.com/vuejs" target="_blank">Twitter</a></li>
<br>
<li><a href="http://vuejs-templates.github.io/webpack/" target="_blank">Docs for This Template</a></li>
</ul>
<h2>Ecosystem</h2>
<ul>
<li><a href="http://router.vuejs.org/" target="_blank">vue-router</a></li>
<li><a href="http://vuex.vuejs.org/" target="_blank">vuex</a></li>
<li><a href="http://vue-loader.vuejs.org/" target="_blank">vue-loader</a></li>
<li><a href="https://github.com/vuejs/awesome-vue" target="_blank">awesome-vue</a></li>
</ul>
</div>
</template>`
`<script>
export default {
name: 'hello',
data () {
return {
msg: 'Welcome to Your Vue.js App'
}
}
}
</script>`
*<-添加“scoped”属性以将 CSS 限制到该组件- >*
`style scoped
h1, h2 {
font-weight: normal;
}
ul {
list-style-type: none;
padding: 0;
}
li {
display: inline-block;
margin: 0 10px;
}
a {
color: #42b983;
}
/style`

默认情况下，组件配置对象被导出。这次组件配置对象包含一个数据方法。此方法返回一个表示组件模型的对象。通过使用插值语法，可以在组件的模板中使用模型对象中定义的属性。在上面的例子中，模型对象只有一个属性:msg。分配给此属性的字符串包含在组件的模板中，方法是使用:

`<h1>{{ msg }}</h1>`

插值语法要求用双花括号将模型数据包含在模板中。

**使用标准指令**
让我们改编 Hello 组件实现，以了解更多关于 Vue.js 标准指令的用法。

**v-for**T3】v-for 指令使得基于源数据多次呈现一个元素成为可能。您可以使用此指令迭代数组，并将数组数据输出到输出。首先向由数据方法:
`users: [
{firstname: 'Sebastian', lastname: 'Eschweiler'},
{firstname: 'Bill', lastname: 'Smith'},
{firstname: 'John', lastname: 'Porter'}
],`
返回的对象添加一个数组，然后使用 v-for 指令在输出中包含一个列表，打印出每个数组元素的名字和姓氏值:

`<div>
<ul>
<li v-for="user in users">
{{user.firstname}} {{user.lastname}}
</li>
</ul>
</div>`

**v-model**T3】v-model 指令在输入元素或组件上创建双向绑定。确保在您的数据对象中定义一个应该用作绑定目标的属性:
input_val: ''
然后使用指令将 input 元素的值绑定到该属性:
`<div>
<input type="text" v-model="input_val">
</div>`
随着绑定的建立，我们将获得两个效果:
每次用户在输入字段中输入值时，input_val 的值都会相应地更新
如果我们在程序中更改 input_val 的值，则显示在 input 元素中的值也会更新

通过使用 v-text 指令来设置元素的文本内容。如果需要设置完整的文本内容，我们可以用它来代替语法。例如，我们可以使用该指令向用户输出输入值:
输入值:

**摘要**
改编后的 Hello 组件实现的完整代码现在应该是这样的:
`<template>
<div class="hello">
<h1>{{ msg }}</h1>
<hr />
<div>
<ul>
<li v-for="user in users">
{{user.firstname}} {{user.lastname}}
</li>
</ul>
</div>
<hr />
<div>
<input type="text" v-model="input_val">
</div>
<div>
Input Value: <span v-text="input_val"></span>
</div>
<hr />
<div>
<button class="btn btn-primary" v-on:click="counter++">You've clicked this button {{counter}} times!</button>
</div>
</div>
</template>`
`<script>
export default {
name: 'hello',
data () {
return {
msg: 'Welcome to Your Vue.js App',
users: [
{firstname: 'Sebastian', lastname: 'Eschweiler'},
{firstname: 'Bill', lastname: 'Smith'},
{firstname: 'John', lastname: 'Porter'}
],
input_val: '',
counter: 0
}
}
}
</script>`
*<！-添加“scoped”属性，将 CSS 限制在该组件内- >*
`<style scoped>
h1, h2 {
font-weight: normal;
}
ul {
list-style-position: inside;
}
a {
color: #42b983;
}
</style>`
结果可以在下面的截图中看到:
[![Vue JS Logo](img/76c41b35f9c4f45fc2e99eb5b52d589d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4NM8CpXD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AxdoMDrZ5XUIFRz8Bg1AFMA.png)

文章功劳归于**CodingTheSmartWay.com**