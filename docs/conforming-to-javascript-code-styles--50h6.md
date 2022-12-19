# 符合 JavaScript 代码风格

> 原文：<https://dev.to/remotesynth/conforming-to-javascript-code-styles--50h6>

最近几年，我写的代码大多是个人作品，所以我不必遵循任何编码风格指南。这会导致一些坏习惯。然而，最近，我开始作为 [Kinvey](https://www.kinvey.com/) 团队的一部分贡献一些代码，并需要符合他们的风格指南。

事情是这样的——根据风格指南编写代码并不容易。以 AirBnB JavaScrpt 风格指南为例(Kinvey 的很大程度上是基于该指南)，我理解所有的规则，但遵循它们意味着打破许多旧习惯并学习新习惯。

对于那些已经在遵循最佳实践的团队中的人来说，这些提示似乎是显而易见的。但是对于我们这些正在转型的人来说，希望这是有用的。

*注:这最初发布在[我的博客](https://www.remotesynthesis.com/blog/prettier-eslint)T3 上*

## 林挺帮助

幸运的是，像 [ESLint](https://eslint.org/) 这样的工具会告诉我哪里出错了，没有遵循风格指南。这让我可以像平常一样编写代码，然后按照风格指南进行清理。运行`eslint --init`甚至可以让你配置 ESLint 来遵循一些流行的风格指南，而不是默认的 ESLint 推荐的风格。

好的一面是 ESLint 让你[共享配置](https://eslint.org/docs/developer-guide/shareable-configs)，允许团队轻松地遵守相同的标准。许多团队公开发布他们的规则，包括:

*   谷歌
*   [AirBnB](https://www.npmjs.com/package/eslint-config-airbnb)
*   [沃尔玛实验室](https://github.com/walmartlabs/eslint-config-walmart)
*   [可变形实验室](https://github.com/FormidableLabs/eslint-config-formidable)

这很棒，一旦安装，使用`eslint --fix`甚至可以自动修复许多问题，但是如果这只是在您的代码编辑器中工作，让您在编码时修复样式问题，这不是很好吗？

## 越漂亮越容易

更漂亮的是一个支持多种语言和编辑器的代码格式化程序，包括一个 T2 Visual Studio 代码扩展 T3(我选择的编辑器)。

漂亮有默认的样式规则，但可配置。然而，因为我已经为 ESLint 定义了我想要遵循的规则，所以我可以只[配置它来使用那些](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode#user-content-vscode-specific-settings)。

为此，首先点击编辑器左下角的小齿轮，选择“设置”。

[![VS Code Settings](../Images/a1b0d93d011137b5188deca92f45b3c7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tiy_XB8f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x7xbx03qixfqy3ekm4t7.png)

或者输入`cmd/ctrl+shift+p`，搜索“打开用户设置”。

[![VS Code Settings](../Images/075cb469039bec1bccfb6262d4a2ec83.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0Qqa6CG1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hviyup6wq1nlvcsl517m.png)

如果你想知道它们是什么，所有漂亮的默认设置都有前缀`prettier.`。然而，在这种情况下，我只想通过将下面一行添加到我的工作区设置中来为这个项目配置一个用户设置。

```
 "prettier.eslintIntegration": true 
```

Enter fullscreen mode Exit fullscreen mode

这样看起来就像这样(假设您没有任何其他工作区设置)。

[![VS Code Workspace Settings](../Images/c8f94a1de4907481bc012c70b991ddf8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dT7YIJ7g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nw7nmr6w92zgb2pa3nc2.png)

如果你想让这个设置在你的项目中普遍使用，你可以改变你的用户设置，但是在我看来这更像是一个项目一个项目的设置。

现在设置好了，我使用`cmd/ctrl+shift+p`并搜索“格式化代码”,它会根据我之前配置的 ESLint 风格指南自动格式化我的 JavaScript。