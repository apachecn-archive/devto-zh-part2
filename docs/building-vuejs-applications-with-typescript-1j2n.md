# 用 TypeScript 构建 VueJS 应用程序

> 原文：<https://dev.to/georgehanson/building-vuejs-applications-with-typescript-1j2n>

近年来，TypeScript 无疑越来越受欢迎。web 行业中越来越多的开发人员希望使用静态类型语言，随着 2016 年 Angular 2 的发布，这只会增加对使用 TypeScript 的开发人员的需求。

当我开始用 TypeScript 编写 Angular 应用程序时，我认为它非常棒。我喜欢静态类型检查系统，我喜欢能够将模板存储在 TypeScript 文件之外，这使我能够与逻辑层和表示层分离。不幸的是，我不喜欢 Angular 需要如此多的设置，更不用说你必须做三四件不同的事情来构建一个组件。对我来说，时间太宝贵了。

在此之前，我曾使用 VueJS 构建单页面应用程序，我非常喜欢它。我一直希望能够将 TypeScript 引入 VueJS，于是研究开始了！

现在我发现很多教程解释了如何将 TypeScript 与 VueJS 一起使用，但是其中很多都集中在单个文件组件上。在我看来，只使用 JavaScript 就可以了，但我真的很喜欢 Angular 将模板存储在 HTML 文件中的方式。

```
@Component({
    selector: 'my-dashboard',
    templateUrl: 'dashboard.component.html',
})
export class DashboardComponent {} 
```

Enter fullscreen mode Exit fullscreen mode

当我认为我运气不好的时候，我找到了解决所有问题的方法。令人惊叹的 VueJS 团队最近发布了 Vue CLI 3——它使得用 VueJS 编写 TypeScript 应用程序的过程变得更加容易！让我们看看如何使用 TypeScript 设置 VueJS 应用程序。

## 安装视图 CLI

第一步是安装 Vue CLI，为此，只需运行以下命令之一(取决于您使用的工具)。

```
npm install -g @vue/cli
# OR
yarn global add @vue/cli 
```

Enter fullscreen mode Exit fullscreen mode

一旦完成，您可以通过运行`vue --version`来验证它是否已经正确安装。它应该显示类似于`3.0.1`的内容。

# 创建一个 TypeScript 项目

新的 Vue CLI 工具允许您使用 TypeScript 轻松创建新项目。要开始，只需运行`vue create my-app`。然后会要求您选择一个预置。使用箭头键，选择`Manually select features`。

接下来，你只需要确保你已经选择了`TypeScript`和`Babel`选项。你可以在下面看到，我还选择了一些其他可选功能。

[![Select your options](img/b9dd9d82943edfae74dfe3cb82b1aeba.png "Select your options")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rR11VHyD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A920vRlqxtqvsKtG6XpALNQ.png)

一旦你这样做了，它会问你是否愿意使用`class-style component syntax`。你会想要选择这个选项。

然后配置其余的设置，它应该看起来像下图。

[![Configuration options](img/d3f52a63318fb9bd6f9a1e58479eda48.png "Configuration options")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bffNOZHG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Ay1IvrXFH4oPnuql0Vahy-g.png)

Vue CLI 工具现在将安装所有依赖项并设置项目。

## 添加额外的依赖项

为了达到我们想要的效果，我们需要安装一些额外的依赖项。您可以通过运行以下命令之一来安装它们。

```
npm install --save vue-template-loader webpack-stream
# OR
yarn add vue-template-loader webpack-stream 
```

Enter fullscreen mode Exit fullscreen mode

您现在应该能够运行`yarn serve`来查看您当前的应用程序。

## 使用类型脚本类代替单个文件组件

接下来，我们希望消除对`.vue`文件的需求，代之以使用类型脚本类。在`components`目录中，你可以看到有`HelloWorld.vue`。我们将使用 TypeScript 类来重新创建它。

因此，首先，在`components`目录中创建一个新文件，并将其命名为`HelloWorld.ts`。我们将添加以下样板文件来开始。

```
import { Component, Vue } from 'vue-property-decorator';

@Component
export default class HelloWorld extends Vue {

} 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们一个现成的空白组件。我们需要做的第一件事是为组件的表示层准备一个外部`.html`文件。为此，创建一个名为`hello-world.html`的新文件。您可以将该文件放在任何您想放的地方，但是出于演示的目的，我将它放在与我们的组件相同的文件夹中。

现在我们需要将演示文稿从`HelloWorld.vue`组件复制到新的`hello-world.html`文件中。所以我们的文件现在应该是这样的。

```
<div class="hello">
    <h1>{{ msg }}</h1>
    <p>
      For guide and recipes on how to configure / customize this project,<br>
      check out the
      <a href="https://cli.vuejs.org" target="_blank" rel="noopener">vue-cli documentation</a>.
    </p>
    <h3>Installed CLI Plugins</h3>
    <ul>
      <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-babel" target="_blank" rel="noopener">babel</a></li>
      <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-typescript" target="_blank" rel="noopener">typescript</a></li>
    </ul>
    <h3>Essential Links</h3>
    <ul>
      <li><a href="https://vuejs.org" target="_blank" rel="noopener">Core Docs</a></li>
      <li><a href="https://forum.vuejs.org" target="_blank" rel="noopener">Forum</a></li>
      <li><a href="https://chat.vuejs.org" target="_blank" rel="noopener">Community Chat</a></li>
      <li><a href="https://twitter.com/vuejs" target="_blank" rel="noopener">Twitter</a></li>
      <li><a href="https://news.vuejs.org" target="_blank" rel="noopener">News</a></li>
    </ul>
    <h3>Ecosystem</h3>
    <ul>
      <li><a href="https://router.vuejs.org" target="_blank" rel="noopener">vue-router</a></li>
      <li><a href="https://vuex.vuejs.org" target="_blank" rel="noopener">vuex</a></li>
      <li><a href="https://github.com/vuejs/vue-devtools#vue-devtools" target="_blank" rel="noopener">vue-devtools</a></li>
      <li><a href="https://vue-loader.vuejs.org" target="_blank" rel="noopener">vue-loader</a></li>
      <li><a href="https://github.com/vuejs/awesome-vue" target="_blank" rel="noopener">awesome-vue</a></li>
    </ul>
  </div> 
```

Enter fullscreen mode Exit fullscreen mode

那么我们如何在我们的`HelloWorld.ts`类中使用这个模板文件呢？我们安装的额外依赖项使我们能够使用另一个装饰器，`WithRender`。这允许我们导入我们的 HTML 文件，并告诉我们的 Vue 组件使用我们的文件进行渲染。将它添加到我们的 TypeScript 文件后，它应该看起来像这样。

```
import { Component, Vue } from 'vue-property-decorator';
import WithRender from './hello-world.html';

@WithRender
@Component
export default class HelloWorld extends Vue {

} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要连接 Vue 路由器来使用我们新的 TypeScript 类，而不是`HelloWorld.vue`文件。为此，打开`views/Home.vue`文件。实际上，您也可以为这个组件创建一个 TypeScript 类，但是对于本指南，我们将只编辑它。

在该文件中，将 import 语句改为使用我们的 TypeScript 文件。因此，我们将从
开始更改以下行

```
import HelloWorld from '@/components/HelloWorld.vue' 
```

Enter fullscreen mode Exit fullscreen mode

至

```
import HelloWorld from '@/components/HelloWorld.ts'; 
```

Enter fullscreen mode Exit fullscreen mode

然而，如果你现在去你的浏览器，你会看到有一个错误。在我们的终端中，我们得到错误:

```
Cannot find module './hello-world.html' 
```

Enter fullscreen mode Exit fullscreen mode

这是因为 TypeScript 不知道如何处理`.html`文件。所以我们需要添加一个`shim`文件。为此，在`src`文件夹中，创建一个`shims-html.d.ts`文件。粘贴下面的代码，使你的文件看起来像这样:

```
declare module '*.html' {
    import Vue, { ComponentOptions, FunctionalComponentOptions } from 'vue'
    interface WithRender {
        <V extends Vue, U extends ComponentOptions<V> | FunctionalComponentOptions>(options: U): U
        <V extends typeof Vue>(component: V): V
    }
    const withRender: WithRender
    export default withRender
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要更新我们的`tsconfig.json`文件，以便 TypeScript 知道加载`.html`文件。将下面一行添加到`include`数组中:`"src/**/*.html"`。所以它应该是这样的:

```
 ...  "include":  [  "src/**/*.ts",  "src/**/*.tsx",  "src/**/*.vue",  "tests/**/*.ts",  "tests/**/*.tsx",  "src/**/*.html"  ],  ... 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们需要在构建过程中添加一些定制的 webpack 配置，告诉 Vue 通过它的模板编译器传递 html 文件。为此，在项目的根目录下创建一个`vue.config.js`文件，并添加以下内容:

```
module.exports = {
  configureWebpack: {
    module: {
      rules: [
        {
          test: /.html$/,
          loader: "vue-template-loader",
          exclude: /index.html/
        }
      ]
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要重新启动编译过程，以便 TypeScript 加载我们的更改。关闭当前终端进程，并再次运行以下命令之一。

```
npm run serve
# OR
yarn serve 
```

Enter fullscreen mode Exit fullscreen mode

您现在应该看到应用程序像以前一样加载，这一次它使用 TypeScript 类文件和 html 模板文件。

您可能注意到的最后一件事是,`msg`数据属性不再存在。所以现在我们来补充一下。

在您的`HelloWorld.ts`文件中，添加以下属性

```
public msg: string = 'I am using TypeScript classes with Vue!'; 
```

Enter fullscreen mode Exit fullscreen mode

如果您现在回头看看您的浏览器，您应该会看到这是在页面上呈现。

这就是全部，你现在可以使用 Vue 构建你的应用，但是使用 TypeScript 类和`html`文件模板。虽然有些人可能不同意这种方法，认为你应该只使用`.vue`文件，但我发现这种
方法更干净，尤其是当一些文件变得很长的时候。

这是系列文章的第一部分。下一次我将更深入地用 TypeScript 编写 VueJS 应用程序，并解释方法、数据属性、道具、子组件等等！

## 更新

第二部分现已发布-[https://dev . to/Georg Hanson/building-vue-js-applications-with-typescript-part-two-2808](https://dev.to/georgehanson/building-vue-js-applications-with-typescript-part-two-2808)