# Vue.js 简化了动态和异步组件

> 原文：<https://dev.to/lobo_tuerto/dynamic-and-async-components-made-easy-with-vuejs-579j>

* * *

你可以在 [lobotuerto 的笔记查看这篇文章的最新更新版本——使用 Vue.js 简化动态和异步组件。](https://lobotuerto.com/blog/dynamic-and-async-components-made-easy-with-vuejs/)

* * *

我认为这篇文章对那些仍在思考 [Vue.js](https://vuejs.org/) 到底有什么大惊小怪的高级 **Angular/AngularJS** 开发人员最有用——就像我几周前一样。

尽管如此，我还是提供了一步一步的指导，对初学者也有帮助。

所以，在这个固执己见的教程中，我希望你能:

*   意识到为 Vue.js 组件设置**按需/延迟加载**是多么的简单可笑。
*   向您展示如何组合**动态组件+异步加载**以获得最大效果！

# 用例

这些是我们将在本教程中回顾的用例:

*   应用程序路由器应该只加载你实际访问的应用程序部分的代码。
*   您应该能够动态地添加/删除/交换页面上的组件。
*   在一个部分中，应用程序应该只加载实际呈现的组件的代码。一个组件可能被声明为可用于呈现，但它的代码只应在您显示它时加载。

# 先决条件

这些是与 [Node.js](https://nodejs.org/en/) 相关的包和版本——还有一些——我将在本教程中用到:

```
git --version #git version 2.16.1
nvm --version #0.33.8
node --version #v.9.5.0
npm --version #5.6.0
yarn --version #1.3.2
vue --version #2.9.3 
```

Enter fullscreen mode Exit fullscreen mode

如果你没有安装[纱](https://yarnpkg.com/)，你可以用`npm install -g yarn`来安装。

或者查看本安装指南[如何在 Ubuntu](https://dev.to/lobo_tuerto/cmo-instalar-nodejs-en-ubuntu-3pi-temp-slug-8227842) 中安装 Node.js。

如果你还没有安装`vue-cli`，你可以用`yarn add global vue-cli`来安装。

用`vue --version`测试一下。

# 创建新的 Vue.js 应用

我们将使用`vue-cli`和`webpack`模板。

让我们创建一个新的 Vue.js 应用:

```
vue init webpack dynamic-async-components-demo 
```

Enter fullscreen mode Exit fullscreen mode

接受所有默认值，除非 NPM/纱线之间有要求；选择纱线。

完成安装过程后，让我们为这个应用程序初始化一个 Git 存储库，并进行第一次提交。

```
cd dynamic-async-components-demo/
git init
git add .
git commit -m "Initial commit"
yarn dev 
```

Enter fullscreen mode Exit fullscreen mode

您可以在:`http://localhost:8080`看到您全新的应用程序。

# 异步路线加载

让我们创建一个新的**单文件组件**(一个**)。vue 【T4 文件】——难道你不喜欢这样吗？—展示此功能。

创建一个内容为
的`dynamic-async-components-demo/src/components/Playground.vue`文件**

```
<template>
  <section class="playground">
    <h1>Welcome to the component playground!</h1>
  </section>
</template>

<script>
export default {
  name: 'Playground'
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

利用 Webpack 的代码分块，我们可以通过只在开始时加载我们需要的代码，然后按需加载其他所有代码，来使我们的应用程序的初始渲染**非常快**。

让我们让新的`Playground.vue`组件异步加载。

打开`dynamic-async-components-demo/src/router/index.js`文件，将其修改如下:

```
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    },
    {
      path: '/playground',
      name: 'Playground',
      component: () => import('@/components/Playground')
    }
  ]
}) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

现在是保存我们进展的好时机:

```
git add .
git commit -m "Add Playground.vue to router and load it async" 
```

Enter fullscreen mode Exit fullscreen mode

* * *

要查看运行中的**异步**加载，打开浏览器控制台(按 F12)并转到**网络**选项卡。

现在访问`http://localhost:8080/#/playground`，观察当您更改 URL 时，它是如何请求一个`.js`文件的。

好事是什么？该应用程序只会发出一次请求，然后它会缓存它！

使这一切成为可能的代码行是这样的:

```
component: () => import('@/components/Playground') 
```

Enter fullscreen mode Exit fullscreen mode

路由器组件的异步加载有多难？:)

# 动态组件渲染

这个在 Vue.js 里太容易了…自己看看就判断了。

## 定义新组件

让我们再创建三个组件在`Playground`上使用:

*   一个**按钮**组件。`dynamic-async-components-demo/src/components/dynamic/Button.vue`:

```
<template>
  <button>I'm a button</button>
</template>

<script>
export default {
  name: 'Button'
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

*   一个**头**组件。`dynamic-async-components-demo/src/components/dynamic/Header.vue`:

```
<template>
  <h1>I'm a header</h1>
</template>

<script>
export default {
  name: 'Header'
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

*   一个**文本输入**组件。`dynamic-async-components-demo/src/components/dynamic/TextInput.vue`:

```
<template>
  <input type="text" placeholder="I'm a text input"/>
</template>

<script>
export default {
  name: 'TextInput'
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

## 静态渲染

如果您想看到您的新组件运行，修改`Playground.vue`文件如下:

```
<template>
  <section class="playground">
    <h1>Welcome to the component playground!</h1>

    <my-button/>
    <my-header/>
    <my-text-input/>
  </section>
</template>

<script>
import Button from '@/components/dynamic/Button'
import Header from '@/components/dynamic/Header'
import TextInput from '@/components/dynamic/TextInput'

export default {
  name: 'Playground',
  components: {
    'my-button': Button,
    'my-header': Header,
    'my-text-input': TextInput
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

然后访问:`http://localhost:8080/#/playground`。你会看到这个:

[![Static Rendering](img/ef2e03e9428d869d314191d617e5b616.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JDP6mKY---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lobotuerto.com/blog/dynamic-and-async-components-with-vuejs-made-easy/static-rendering.png)

让我们保存我们的进度:

```
git add .
git commit -m "Add Button, Header, TextInput components and display them" 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 动态渲染

我们在这一节要做的是呈现一个`<select>`输入，作为从列表中选择一个组件并显示它的手段。

这可以使用强大的`<component></component>`元素来完成。

修改`Playground.vue`文件，如下所示:

```
<template>
  <section class="playground">
    <h1>Welcome to the component playground!</h1>

    <select v-model="selectedComponent">
      <option
        v-for="(component, index) in componentList"
        :key="index"
        :value="component"
      >
        {{ component.name }}
      </option>
    </select>

    <hr>
    <component :is="selectedComponent"></component>
  </section>
</template>

<script>
import Button from '@/components/dynamic/Button'
import Header from '@/components/dynamic/Header'
import TextInput from '@/components/dynamic/TextInput'

export default {
  name: 'Playground',
  data: function () {
    return {
      componentList: [Button, Header, TextInput],
      selectedComponent: null
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

访问:`http://localhost:8080/#/playground`，点选**文本输入**。你会看到这个:

[![Dynamic Rendering](img/df44038791f793f00422740ee12c1495.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LfuQjKcl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lobotuerto.com/blog/dynamic-and-async-components-with-vuejs-made-easy/dynamic-rendering.png)

让我们保存我们的进度:

```
git add .
git ci -m "Dynamic component rendering" 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# 异步加载+动态渲染

为上面的 **TextInput** 组件启用异步加载需要什么？

嗯，你只需要把`Playground.vue`改成这个:

```
<template>
  <section class="playground">
    <h1>Welcome to the component playground!</h1>

    <select v-model="selectedComponent">
      <option
        v-for="(item, index) in componentList"
        :key="index"
        :value="item.component"
      >
        {{ item.label }}
      </option>
    </select>

    <hr>
    <component :is="selectedComponent"></component>
  </section>
</template>

<script>
import Button from '@/components/dynamic/Button'
import Header from '@/components/dynamic/Header'
// Comment out the line below, since we will be loading it asynchronously
// import TextInput from '@/components/dynamic/TextInput'

export default {
  name: 'Playground',
  data: function () {
    return {
      componentList: [
        { label: 'Button', component: Button },
        { label: 'Header', component: Header },
        {
          label: 'TextInput',
          // Async loading!
          component: () => import('@/components/dynamic/TextInput')
        }
      ],
      selectedComponent: null
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

很简单，不是吗？

* * *

保存您的进度:

```
git add .
git commit -m "Load TextInput asynchronously" 
```

Enter fullscreen mode Exit fullscreen mode

* * *

您可以通过查看浏览器控制台中的 Network 选项卡并选择 **TextInput** 选项来验证它是否异步加载。此时，将请求检索该组件的代码！

## 保持活力

敏锐的读者可能已经注意到，当您切换到另一个组件时，无论您在 **TextInput** 组件中键入什么都会丢失。如果你想在内存中保存动态组件上的内容，你所要做的就是用`<keep-alive></keep-alive>`标签包围`<component></component>`元素，就像这样:

```
<keep-alive>
  <component :is="selectedComponent"></component>
</keep-alive> 
```

Enter fullscreen mode Exit fullscreen mode

去试试吧！选择**文本输入，**输入一些内容，然后选择其他内容，然后返回**文本输入**，你会看到你之前输入的内容仍然存在。厉害！

* * *

让我们为本教程做最后的提交:

```
git add .
git ci -m "Preserve dynamic components state in memory" 
```

Enter fullscreen mode Exit fullscreen mode

# 一些观察结果

你知道还有什么证明了 Vue 的力量和灵活性吗？那种[角材](https://vuetifyjs.com/)比[角材](https://material.angular.io/)本身更先进、更有特色、更完整地贯彻了[材料设计准则](https://material.io/guidelines/)。

前几天我浏览了它的组件和 API，对它们已经拥有的大量元素流口水和大笑。:)

* * *

如果你是一个 **Angular** 开发者，比较一下我们上面做的东西和你在 Angular 中如何做[惰性加载。](https://angularfirebase.com/lessons/how-to-lazy-load-components-in-angular-4-in-three-steps/)

# GitHub 资源库

如果不想键入所有代码，只需在:

[https://github . com/lobo-tuer to/vuejs-dynamic-async-components-demo](https://github.com/lobo-tuerto/vuejs-dynamic-async-components-demo)克隆 repo 即可