# 在单个文件中编写多个 Vue 组件

> 原文：<https://dev.to/hugo__df/writing-multiple-vue-components-in-a-single-file-hgj>

在一个文件中编写多个组件是 React 的一种模式，其中一些文件包含多个组件。

其中一些组件是文件/导出组件的“私有”组件，因为没有其他组件需要使用它们。

下面是 Swizec 对此的抱怨:

> 创建新的微型 Vue 组件很麻烦，所以会导致这样的重复代码🤨
> 
> 预订描述应该是一个很小的组件，只是从道具中获取东西，甚至可能与真正的组件在同一个文件中。
> 
> 使用两次，但不可重复使用或原子🤔[pic.twitter.com/6WzEO5knen](https://t.co/6WzEO5knen)
> 
> —瑞士信贷柜员(@瑞士信贷)【2018 年 9 月 6 日

由于我没有上面的完整代码，我们将使用默认的“Hello World”组件，该组件来自一个以`vue-cli`为例搭建的项目。

默认有两个文件，一个用于`App`，一个用于`HelloWorld`(在`src/App.vue`和`components/HelloWorld.vue`)。`HelloWorld`拿一个`msg`道具并渲染。

要在一个文件中编写这些内容，使用 React 可能是这样的:

```
const HelloWorld = ({ msg }) => (<div>
  <h1>Hello world</h1>
  <div>{msg}</div>
</div>);

const App = () => (<div id="app">
  <HelloWorld msg="Welcome to Your React App" />
</div>);

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

因为 React“只是 JavaScript ”,所以您可以在一个文件中有多个组件定义，而不是导出其中的一些(只是为了保持导出的组件干燥)。

在 Vue 中，这仍然是可能的，但会稍微复杂一点，因为实现这一点的方法不止一种:

*   [使用渲染功能](#using-a-render-function)
*   [使用 Vue.component 和一个模板](#using-vuecomponent-and-a-t%20%20%20%20emplate)
*   [使用模板和无 Vue.component](#using-a-template-and-no%20%20%20%20-vuecomponent)
    *   [vista CLI 3+](#vue-cli-3)
    *   [vista CLI<3.0](#vue-cli--30)

在[github.com/HugoDF/vue-multiple-components-in-sfc](https://github.com/HugoDF/vue-multiple-components-in-sfc)回购的例子。

## 使用渲染功能

```
<template>
  <div id="app">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>
<script>
// inline component
const HelloWorld = {
  props: ['msg'],
  render(h) {
    return h('div', [
      h('h1', 'Hello world'),
      h('div', this.msg)
    ])
  }
};
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

[![](img/e860e4ed48616d58ca35af4f5b52c7a3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oNPeDihA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_C77B3833CA1DF6A1ECE1A6427FA5D87D901D3E0465D27B32C42538222EBFCCF1_1536749104796_Screen%2BShot%2B2018-09-12%2Bat%2B11.44.47.png)

## 使用 Vue.component 和一个模板

```
<template>
  <div id="app">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>
<script>
import Vue from 'vue';
// inline component with template string :+1:
const HelloWorld = Vue.component('hello-world', {
  props: ['msg'],
  template: `<div>
    <h1>Hello world</h1>
    <div>{{ this.msg }}</div>
  </div>`
});
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

如果不包含运行时，这是否有效？

**没有**

```
[Vue warn]: You are using the runtime-only build of Vue where the template compiler is not available. Either pre-compile the templates into render functions, or use the compiler-included build.

found in

--------> <HelloWorld>
        <App>
          <Root> 
```

Enter fullscreen mode Exit fullscreen mode

幸运的是，我们可以使用带有模板编译器的构建来修复它(参见[https://code . Lua software . com/tutorials/vue js/vue-CLI-3-include-runtime-compiler/):](https://code.luasoftware.com/tutorials/vuejs/vue-cli-3-include-runtime-compiler/):)差不多，创建(如果不存在)`vue.config.js`并添加:

```
module.exports = {
  runtimeCompiler: true
}; 
```

Enter fullscreen mode Exit fullscreen mode

正如在链接文章中指出的，这将 Vue 模板编译器添加到您的包中…大约 10KB。重新启动开发服务器:`npm run serve`:

[![](img/b89e2fad46f683f870a72176b73d9d90.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lI5aLye5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_C77B3833CA1DF6A1ECE1A6427FA5D87D901D3E0465D27B32C42538222EBFCCF1_1536749628943_Screen%2BShot%2B2018-09-12%2Bat%2B11.44.47.png)

## 使用一个模板而没有 Vue.component

```
<template>
  <div id="app">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>
<script>
// inline component with template string :+1:
const HelloWorld = {
  props: ['msg'],
  template: `<div>
    <h1>Hello world</h1>
    <div>{{ this.msg }}</div>
  </div>`
};
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

仍然有效(如果我们有正确的`vue.config.js`并启用了`runtimeCompiler`):

[![](img/861de5404fb5f6a97670f6acbe08ba1b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LDRb2qIy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_C77B3833CA1DF6A1ECE1A6427FA5D87D901D3E0465D27B32C42538222EBFCCF1_1536749695600_Screen%2BShot%2B2018-09-12%2Bat%2B11.44.47.png)

由于我没有上面的完整代码，w#使用 JSX(编译成渲染函数)

我们可以用 JSX: `App.js` :
重写我们最初的渲染函数的例子

```
<template>
  <div id="app">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>
<script>
// inline component with JSX
const HelloWorld = {
  props: ['msg'],
  render() {
    return (<div>
      <h1>Hello world</h1>
      <div>{this.msg}</div>
    </div>);
  } 
};

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

### CLI 3+视图

如果您使用的 Vue-cli 版本高于或等于 3.0，那么您很幸运，因为它支持 JSX。[https://scotch . io/tutorials/using-jsx-with-vue-and-why-you-should-care](https://scotch.io/tutorials/using-jsx-with-vue-and-why-you-should-care)

### CLI 视图< 3.0

你需要四处挖掘，看看[babel-plugin-transform-vue-jsx](https://github.com/vuejs/babel-plugin-transform-vue-jsx#usage)。

我可能会安装以下软件包:

```
npm install\
  babel-plugin-syntax-jsx\
  babel-plugin-transform-vue-jsx\
  babel-helper-vue-jsx-merge-props\
  babel-preset-env\
  --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

再次举例说明在[github.com/HugoDF/vue-multiple-components-in-sfc](https://github.com/HugoDF/vue-multiple-components-in-sfc)的回购。

随时给我发微博 [@hugo__df](https://twitter.com/hugo__df) 。

这篇文章最初发表于 https://codewithhugo.com

雷蒙德·拉斯姆森