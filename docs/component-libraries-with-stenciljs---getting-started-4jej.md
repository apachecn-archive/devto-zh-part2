# Stencil.js 组件库-入门

> 原文：<https://dev.to/johnbwoodruff/component-libraries-with-stenciljs---getting-started-4jej>

*这是关于使用 Stencil.js 创建 web 组件库的系列文章的第二篇——请看第一篇文章*

既然我们已经讨论了选择 Stencil 来构建我们的 web 组件库的原因，让我们直接进入主题。首先，我们需要建立我们的基础项目结构。谢天谢地，Ionic 团队已经完全为我们解决了这个问题。确保您运行的是版本 6 或更高版本的`npm`，并运行以下命令:

```
$ npm init stencil 
```

Enter fullscreen mode Exit fullscreen mode

您应该会得到类似下面的提示:

```
? Pick a starter › - Use arrow-keys. Return to submit.

   ionic-pwa     Everything you need to build fast, production ready PWAs
   app           Minimal starter for building a Stencil app or website
❯  component     Collection of web components that can be used anywhere 
```

Enter fullscreen mode Exit fullscreen mode

爱奥尼亚团队为我们提供了一些首发。我们对使用 Stencil 构建一个完整的应用程序或 PWA 不感兴趣(尽管你肯定可以！)所以我们要选择`component`选项。这将对它进行结构化，这样我们就可以构建一个可重用的组件库来进行分发。

你会被要求为你的系列命名，所以你可以随意命名。我将要构建的组件库是一个我称之为`mountain-ui`的组件库，因为我住在犹他州美丽的瓦萨奇山脉中。

一旦创建了启动器，您就可以`cd`进入您新创建的目录并运行`npm start`。这将在您的浏览器中打开项目，其中包含一个基本的 web 组件。我们将马上开始编写一个组件，但是让我们首先检查一下项目结构，并弄清楚事情的发展方向。

## 项目结构

您会注意到几个目录。你最想关注的是`src`。其他需要注意的重要目录是`www`，这是您在开发时编译的组件所在的位置，以及`dist`，这是您在运行生产构建后的实际发行版所在的位置。

在我们讨论组件之前，还有一些其他重要的文件要看。如果你以前写过 TypeScript，你会认出定义我们的 TypeScript 编译器选项的`tsconfig.json`。另一个重要文件是`stencil.config.ts`。这个文件定义了你的模板构建和它的各种选项。在这篇文章的后面，我们将改变一些东西。最后还有`src/components.d.ts`，这是一个你永远不会自己修改的文件。这是一个由 stencil 在构建时生成的文件，它使您的库的所有 TypeScript 定义保持最新。

## 组件

目录是你花费大部分时间的地方。我们将把每个组件保存在单独的文件夹中。您会注意到默认情况下有一个`my-component`目录，里面有三个文件。主要文件是`my-component.tsx`。这是 TSX 文件，保存组件类、其渲染方法以及与组件相关的其他方法和属性。还有一个相应的`my-component.spec.ts`文件，用于测试您的组件。最后是`my-component.css`，这是你的风格生活的地方。默认情况下，您有 CSS 文件，但您可以使用 CSS 预处理程序，如 SASS。

## 配置本库

在开始构建组件之前，我们需要做一些事情。让我们从`stencil.config.ts`文件开始。我们首先要改变的是`namespace`值。默认情况下，它被设置为`mycomponent`，但是我们将把它改为我们库的名称。在我的例子中，我将把值改为`mountain-ui`。因为我们更改了名称空间，所以让我们也确保名称在`src/index.html`文件中得到正确反映。确保您的文件顶部有以下脚本标签:

```
<script type="module" src="/build/mountain-ui.esm.js"></script>
<script nomodule src="/build/mountain-ui.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

接下来要做的是设置一个 CSS 预处理器。这不是必需的，许多人更喜欢使用普通的 CSS，这很好。模板的可用插件列在[插件](https://stenciljs.com/docs/plugins)页面上。我个人喜欢使用 SASS，所以我将在我的项目中设置它。

首先我们需要安装合适的插件。在我的例子中，我将运行下面的命令(注意我使用的是 yarn，但是您可以使用您喜欢的任何东西):

```
$ yarn add --dev @stencil/sass 
```

Enter fullscreen mode Exit fullscreen mode

一旦插件安装完毕，我将把它导入到配置文件中，并把它传递给配置对象中的`plugins`数组。请看下面的完整文件:

```
import { Config } from '@stencil/core';
import { sass } from '@stencil/sass';

export const config: Config = {
  namespace: 'mountain-ui',
  outputTargets: [
    {
      type: 'dist',
      esmLoaderPath: '../loader'
    },
    {
      type: 'docs-readme'
    },
    {
      type: 'www',
      serviceWorker: null // disable service workers
    }
  ],
  plugins: [
    sass()
  ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

这就是为 SASS 设置项目所需做的全部工作。如果您想使用其他预处理程序，只需安装适当的插件并遵循上述相同的步骤。我需要做的最后一件事是将`my-component.css`更改为`my-component.scss`，我将更改`my-component.tsx`中的`styleUrl`，从现在开始我创建的任何新组件都将有一个`scss`文件，而不是`css`文件。

## 下一步

现在我们已经完全配置了我们的项目，我们可以自由地构建我们的组件，而不用担心配置或构建。在下一篇文章中，我们将开始详细构建我们的第一个组件！

*单纯想看最终结果回购？来看看[这里](https://github.com/johnbwoodruff/mountain-ui)T3】*