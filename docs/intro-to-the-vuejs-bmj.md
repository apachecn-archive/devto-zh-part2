# Vuejs 简介🐎🐎🐎

> 原文：<https://dev.to/sait/intro-to-the-vuejs-bmj>

## 你用过 Vuejs 还是你还在迷茫？

今天我在这里简单地解释一下 Vuejs 及其用途。让我们从写代码而不是读大段开始学习吧。

对于这些，我用的是笔。
首先，我从用 id app 写 div 标签开始。

```
<div id="app"></div> 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们写一些 Vuejs 代码

Vue 为我们提供了新的 Vue 实例，它接受一个对象作为参数。

```
let app = new Vue({
  el:'#app'
}) 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码所做的是，如果我们在 div 元素中编写任何代码，它都是由 vue 控制的。

第一个属性是 el，它表示我们的 HTML 代码 Vue 需要以哪个元素为目标。

**数据属性**:管理 Vue 内部的状态。

**方法属性**:是我们可以定义函数的地方。

现在让我们编写数据属性和方法

```
var app = new Vue({
  el:'#app',
  data:{
    name:'Welcome to Vuejs',
    show:true,
    persons:['Gowtham','Aaron','Tonny']
  },
  methods:{
     changeme(){
       this.show = !this.show
    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

数据和方法属性是对象。

在数据属性中，我定义了一些静态数据，在方法属性中，我声明了 **changeme** 函数。

现在让我们在 div 元素中使用这些属性。

```
<div id="app">
  <h1 v-if="show">{{name}}</h1> 
  <ul v-for="person in persons" v-if="show">
    <li>{{person}}</li>
  </ul>
  <button @click="changeme">
    {{show ? 'Hide' : 'Show'}}
  </button> </div> 
```

Enter fullscreen mode Exit fullscreen mode

我用 v-if 指令定义了一个 h1 元素，你见过我用 show property 定义的东西吗，它是我们已经在 Vue 实例的 data 属性中定义的。

#### v-if 只在给定条件为真时才在 dom 中显示。

第二件事，我用 v-for 指令使用了一个无序列表

v-for 帮助遍历数组，我已经使用了 **persons** 数组，这是我们已经在 Vue 实例的数据属性中定义的。

我使用了双花括号，因为 Vue 使用了类似数据绑定语法的模板。

最后一个是按钮元素，我注册了一个点击处理程序
，为此我使用了@click 指令，它是 v-on 的简写:click
你可以使用它们中的任何一个。

最终输出是

[https://codepen.io/saigowthamr/embed/qYxjYN?height=600&default-tab=result&embed-version=2](https://codepen.io/saigowthamr/embed/qYxjYN?height=600&default-tab=result&embed-version=2)

你可以在小应用或大应用中使用 Vue，也可以用 Vue 制作一个完整的单页应用。

希望你们喜欢这些来了解更多关于 Vuejs 的信息，你可以查看文档

**资源**
[Vue 文档](https://vuejs.org/v2/guide/)
[Vue 指令](https://vuejs.org/v2/guide/syntax.html#Directives)
[Vue js 事件处理](https://vuejs.org/v2/guide/events.html)