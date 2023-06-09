# 在 Vue 的 Nuxt.js 中添加像 Foundation 这样的 SCSS 库

> 原文：<https://dev.to/kball/adding-scss-libraries-like-foundation-to-vues-nuxtjs-5329>

我最近写了关于将 Vue.js 添加到一个 Foundation ZURB 堆栈项目中的文章。今天我想看一下相反的情况:将作为 SCSS 库的 Foundation 集成到 Vue 项目中，或者更具体地说，集成到 Nuxt.js 项目中。

[Nuxt.js](https://nuxtjs.org/) 是一个构建在 Vue.js 之上的高级框架，它提供了两个主要优势:

1.  Nuxt 使得建立一个带有服务器端渲染的“通用 JavaScript”应用程序变得非常容易。
2.  Nuxt 预打包了一组您可以使用的许多组件的“黄金路径”集合。

这两个好处都非常棒——我已经在几个客户项目中使用了 Nuxt，它使得设置一个应用程序变得非常简单，这个应用程序具有服务器渲染应用程序的所有 SEO 和初始页面加载好处，以及 SPA 的流畅性和强大功能。拥有一组关于应用程序结构、路由和预先设置的布局功能的合理缺省值也非常有帮助。

#### 挑战:超越默认

与预打包解决方案一样，当添加创作者想象之外的新功能时，挑战有时会到来。Nuxt 在简化这方面做得很好，但是有一个地方我一开始就碰到了，花了我一点时间才弄明白。

放入新的 CSS 文件不成问题，从独立的 SCSS 构建完整的 CSS 文件也不成问题。然而，我喜欢使用 Foundation 的 SCSS 作为一个库来导入到我自己的文件中，所以当我建立一个项目时，我将 webpack 的`sass-loader`指向包括来自 Foundation 和 Motion UI 的路径。使用其他 SCSS 库中的 mixins 或函数需要相同类型的设置。

这样做的挑战是 Nuxt 生成它的 web pack config _ programmatically _——没有`webpack.config.js`可以修改。

我们可以用一个简单的`npm install --save-dev sass-loader node-sass`来安装 sass-loader(以及它的对等依赖节点-sass)，但是现在怎么办？

#### 答案:扩展构建配置

虽然 Nuxt 没有要修改的`webpack.config.js`文件，但是它们允许您扩展`nuxt.config.js`中的构建配置。我们可以在那里添加新的 webpack 规则...但是还有一个问题:Nuxt 已经有了一个针对`scss`的规则，所以添加另一个规则不会像我们预期的那样工作。相反，我们需要找到那个规则并修改它，就像这样:

```
build: {
  extend(config) {
    for (const rule of config.module.rules) {
      if (rule.use) {
        for (const use of rule.use) {
          if (use.loader === 'sass-loader') {
            use.options = use.options || {};
            use.options.includePaths = ['node_modules/foundation-sites/scss', 'node_modules/motion-ui/src'];
          }
        }
      }
    }
  },
}, 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。现在我们可以在任何我们想要的地方从 Foundation 和 Motion UI 中进行对话！

如果你想从这个工作的样本项目开始，我在 githug 这里建立了一个资源库:[https://github.com/kball/nuxt-foundation-demo](https://github.com/kball/nuxt-foundation-demo)

* * *

又及——如果你对这类话题感兴趣，我会发送一份名为“[星期五前端](https://zendev.com/friday-frontend.html)”的每周时事通讯。每周五我会发出 15 个链接，链接到 CSS/SCSS、JavaScript 和其他各种精彩的前端新闻中的最佳文章、教程和公告。在这里报名:【https://zendev.com/friday-frontend.html T2】