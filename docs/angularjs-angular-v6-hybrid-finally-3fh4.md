# AngularJS + Angular (v6)混动(终于！)

> 原文：<https://dev.to/roelofjanelsinga/angularjs-angular-v6-hybrid-finally-3fh4>

[!["Sports car"](img/134baa84f796c654550c339d1d91df2a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kKfvJfDe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/czqt5aux9jsggfdkjprt.jpeg)

# AngularJS + Angular (v6)混血儿(终于！)

终于到了实现这一切的时候了！我运营和维护一个网站已经有 2.5 年了。这个网站是用 angular js(1 . 6 . 9 版)搭建的。这工作得相当不错，但与 Angular 的新版本相比就不算什么了。所以我最终采取了第一步，逐步将所有东西迁移到新版本。我是这样做的:

1.  创建新的 Angular CLI 项目
2.  将所有 JavaScript 文件复制并转换为 TypeScript
3.  用相对 URL 替换中的绝对模板 URL
4.  调整角度 CLI 设置以适应角度
5.  在你的 Angular 应用程序中包含 AngularJS

## 创建新的 Angular CLI 项目

因为没有人想重新发明轮子，遵循这个“官方”指南来创建一个新项目:[https://angular.io/guide/quickstart](https://angular.io/guide/quickstart)

## 复制&将所有 JavaScript 文件转换成 TypeScript

这看起来很简单，事实也的确如此...但不仅仅是改变文件扩展名。重命名来自的所有文件。js“to”。ts”，你可以做任何你想做的事。你可以用 NPM 分机或通过你的终端手动操作。我选择使用 NPM 扩展:Renamer。如果您想使用相同的，请遵循下面的命令:

```
npm i -g renamer 
```

Enter fullscreen mode Exit fullscreen mode

实际重命名文件:

```
renamer --find '.js' --replace '.ts' 'root/folder/of/app/**/*.js' 
```

Enter fullscreen mode Exit fullscreen mode

这将把所有的 JavaScript 文件重命名为 TypeScript 文件。接下来，如果你还没有使用 ES6/ES2015，你需要将你的 Javascript 转换成这种格式。TypeScript 不适用于非箭头函数。此外，您将希望开始使用 JavaScript 的“导入”和“导出”指令，而不是“要求”。这将有助于 Webpack(内置 Angular CLI)稍后构建您的应用程序。

一旦您重命名并重写了您的代码，您可以将其复制到您之前创建的 Angular CLI 项目中。遵循以下指南，从“创建导入链”直到“配置 Angular CLI”:制作混合。此时，您应该将所有文件都转换为 TypeScript 格式，并集成到您的“新”Angular CLI 项目中。

## 用相对 URL 替换模板中的绝对 URL

此时，你已经可以开始编译你的应用了，但是如果你像我一样使用绝对模板 URL，你会遇到错误。Angular CLI 使用 Webpack 将其 TypeScript 文件编译成 Javascript，然后编译成一个包。Webpack 要求您使用相对路径。所以现在用相对路径替换所有的绝对模板路径。这些可能位于指令(或组件)、路由器或任何控制器中。

## 调整角度 CLI 设置以适应角度

此时，您将能够完全编译您的混合应用程序，但仅用于开发目的。一旦你尝试用生产标志编译你的应用:

```
ng build --prod 
```

Enter fullscreen mode Exit fullscreen mode

您将无法在浏览器中加载该应用程序。这是因为 Webpack 会尝试解析任何和所有函数，将它们编译成基本的 Javascript。这适用于 Angular (v6)，但不适用于 AngularJS。要解决这个问题，请在“angular.json”文件中编辑以下设置:

```
/*This  is  the  old  situation*/  "configurations":  {  "production":  {  "fileReplacements":  [  {  "replace":  "src/environments/environment.ts",  "with":  "src/environments/environment.prod.ts"  }  ],  "optimization":  true,  "outputHashing":  "all",  "sourceMap":  false,  "extractCss":  true,  "namedChunks":  false,  "aot":  true,  "extractLicenses":  true,  "vendorChunk":  false,  "buildOptimizer":  true,  "serviceWorker":  true  }  }  /*And  this  is  the  new  situation*/  "configurations":  {  "production":  {  "fileReplacements":  [  {  "replace":  "src/environments/environment.ts",  "with":  "src/environments/environment.prod.ts"  }  ],  "optimization":  true,  "outputHashing":  "all",  "sourceMap":  false,  "extractCss":  true,  "namedChunks":  false,  "aot":  false,  //Updated,  remove  this  comment  if  you  copy/paste  "extractLicenses":  true,  "vendorChunk":  false,  "buildOptimizer":  false,  //Updated,  remove  if  copy/paste  "serviceWorker":  true  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 在您的 Angular app 中包含 AngularJS

你快完成了！最后一步是将你的 AngularJS 应用程序包含在你的新的、闪亮的 Angular 应用程序中。要做到这一点，你可以按照我之前给你指出的指南“引导混合动力车:制造混合动力车”去做。如果您希望能够在 AngularJS 应用程序中使用新的角度组件，请遵循以下步骤:角度升级。本指南还将向您展示如何在 Angular 中使用 AngularJS 组件，但我建议您尽可能多地将这些组件升级到 Angular (v6)。无论如何，它们在某个时候都必须升级，所以这是一个绝佳的机会！

现在，您终于可以构建用于生产目的的应用程序了！一旦你把所有东西都转换成 Angular (v6)，你就可以使用 AOT 并再次构建优化器，让你的应用程序更加高效。这可能是我在我自己的过程中犯了一个错误，这就是为什么 AOT 目前不工作，但这将需要等待修改。

## 备注

这个指南并不适用于每个人，我个人使用了 3 或 4 个不同的指南，甚至更多的谷歌搜索来找到正确的地方。这次升级不是你做过的最容易的事情，但是非常值得。这将大大提高你的应用程序的稳定性和可靠性。它也将解决你可能遇到的任何 SEO 问题，因为 AngularJS 实际上能够在一个(节点)服务器上渲染！

如果你有任何问题，或者更好的建议，关于我如何能使这个过程对你我来说更容易，请留下评论。我很乐意帮助你，或者从你进行这种地狱般升级的经历中学习！如果你想了解更多关于我在 Angular 和 SEO 上的挣扎，请看:如何索引 AngularJS 构建的单页应用程序。