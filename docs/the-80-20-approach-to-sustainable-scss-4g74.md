# 可持续 SCSS 的 80-20 方法

> 原文：<https://dev.to/kball/the-80-20-approach-to-sustainable-scss-4g74>

在过去的几年里，CSS 和 SCSS 领域有了大量的创新。大量新的命名约定、架构选择和高级库，使您的 CSS 比以前更易于管理、功能更强大。

这太棒了！但是它的另一面是，额外的选项带来了额外的复杂性，对于新的 CSS 开发人员或那些没有跟上每一种新方法的人来说，可能很难知道从哪里开始。

我应该选择 BEM 还是 SMACSS？那么 ITCSS、ECSS 或其他各种选项呢？这些 CSS-in-JavaScript 选项怎么样？它们有什么不同，各有什么优缺点？

决策的复杂性会让你很不愉快，以至于你根本不做任何决定。

### 80%的价值，20%的工作

对于绝大多数的开发者和项目来说，你不需要把一切都优化到极致。

当然，你应该使用 SCSS，因为它是 CSS 的一个干净的超集，允许你进行干净的分解和代码重用。

除此之外，只需在两个方面做出决策，就可以用 20%的工作获得 80%的收益:文件结构和命名约定。

### 文件结构

文件结构指的是你如何将你的 SCS 分成不同的样式。

对于文件结构，我推荐一个简单的结构，看起来像这样。

```
app.scss # top level, @imports and @includes all other files
_variables.scss # file for setting global variables and settings
utils/ # Folder for shared utility functions and mixins.
components/ # Folder for component level styles
pages/ # Folder for page-specific stylings and overrides 
```

Enter fullscreen mode Exit fullscreen mode

app.scss 文件成为所有样式的入口点。它应该导入 _variables.scss，您的依赖项(听起来很像 bootstrap，但也包括您碰巧使用的任何其他 scss 库)，然后是您的 utils，然后是您的组件和页面样式。

将每个组件的样式组织到该组件的特定文件中。例如，如果要扩展按钮样式，应该有一个名为“components/_button.scss”的文件包含这些样式。

如果您有页面特定的覆盖和样式，这些应该放入`pages/_page-name.scss`。

样式应该尽可能地属于一个组件或一个页面，但是您可能还需要一些高级的' _layout.scss '文件来进行全局样式设置。

### 命名约定

命名约定指的是你如何命名你的类。

命名约定有很多选项，您想要选择哪一个取决于团队和项目的规模。对于大型项目和大型团队，您会想要更严格的命名约定，而对于较小的项目和团队，您可能不太关心。

最后，最重要的是您希望您的命名约定在组件之间保持一致，并且您希望保持低特异性。许多命名约定都致力于“单个类 API”，这意味着所有的风格都是特殊性 1，并且包含在单个类中。我通常允许一些级别的修饰符类，但尽量保持特异性在 2 或以下。(即最多 2 节课)。

可能最广泛使用的命名约定是[块-元素-修饰符(BEM)](http://getbem.com/) 。一开始可能会觉得有点吓人，但这是一个非常好的选择。如果你正在寻找一些不太严格的东西， [SMACSS](https://smacss.com/) 是一个很好的指南。

### 有制度总比没有制度好

最重要的不是要有一个完美的制度，而是要有一个制度。在您的 SCSS 组织和命名约定中实施一致性，并在您了解和发现当前系统不工作的地方时逐步改进。

* * *

又及——如果你对这类话题感兴趣，我会发送一份名为“[星期五前端](https://zendev.com/friday-frontend.html)”的每周时事通讯。每周五我会发出 15 个链接，链接到 CSS/SCSS、JavaScript 和其他各种精彩的前端新闻中的最佳文章、教程和公告。在这里报名:【https://zendev.com/friday-frontend.html T2】