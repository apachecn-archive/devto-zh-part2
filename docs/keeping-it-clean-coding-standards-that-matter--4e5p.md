# 保持整洁:重要的编码标准

> 原文：<https://dev.to/lhuria94/keeping-it-clean-coding-standards-that-matter--4e5p>

一年前，我在做一个项目，这个项目教会了我如何保持代码整洁、模块化、可重用，以及所有那些看似花哨，但实际上从长远来看对你有好处的术语。有意思？是的有一点。

但是在进入那些实践之后，我做了什么呢？

我犯了错误。相信我，他们中的很多人。但是每一次失误，我都学到了很多以前从未想过的东西。它在我的思考过程中帮助了我，关于我们应该如何构建东西，当我们开发/扩展一个特性时我们需要考虑什么步骤。最重要的是，这些学习不仅对个人有帮助，对团队成长也至关重要。

起初，我们常常感到沮丧，因为我们必须遵循额外的过程，如添加文档、维护变更日志文件、遵循代码标准，并保持它们在整个团队中的一致性。这些额外的步骤看起来很麻烦，我们也不知道这对我们(团队)有什么用。在这方面，我们每天都在学习/提高。但是几个月后，我们开始喜欢并即兴创作这个过程。

所以我在这里，分享我所学到的。相信我，当我把这个付诸实践后，你不能不遵循它。

这篇文章关注的是我们每天遵循什么样的实践来使我们的生活变得更容易(更关注 PHP/Drupal，但也可以遵循，我们遵循相当一般的实践)。

## 让我们从简单的事情开始:

**评论和文档标准**

注释并不意味着在编码时在任何地方添加一堆注释和随机文档。有些事情你应该提一下，让你的同事和你的生活更轻松。

*   从描述你的组件开始，你为什么要创建它，你想要完成的目标是什么，它有什么作用等等。
*   如果进行了修改，那么应该通过创建一个附加到组件的 Changelog.md 文件来记录这些修改。保持一个特定的格式，以便始终保持一致。

这是我们所关注的，希望这是相当清楚的:
CHANGELOG.md 文件示例:

[![Changelog sample](../Images/a5b05c7e21109ff7a0cc35e6ebabda40.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5Y9nyIta--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lhuria94.github.io/assets/img/2017-07-26-coding-standards-that-matter/changelog.png)

*   添加@see 是一个很好的做法，引用类，这将有助于通过使用 IDE(如 PHPStorm)或编辑器(如 Sublime 等)轻松导航到该类定义，只需单击一下。

[![Function referencing](../Images/1b890e2b096ea4a27fb487a931ab36fe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XEw5ai9Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lhuria94.github.io/assets/img/2017-07-26-coding-standards-that-matter/fn-referencing.png)

*   必要时添加@TODOS。如果你觉得你的代码在未来可以改进，并且你有如何改进的想法，但目前没有足够的时间，这是非常重要的。在代码片段上面提到需要改进的地方。一个很好的例子是:

[![Todo example](../Images/cc4b74b2c4659425f8b24c10d499e65e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IU5-yv5t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lhuria94.github.io/assets/img/2017-07-26-coding-standards-that-matter/todo-example.png)

*   创建 README.md 文件，以便其他人可以轻松理解该模块的工作原理。例如:

[![Readme example](../Images/29386ffc00d57037386445a6d2bf4082.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9TZj29_p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lhuria94.github.io/assets/img/2017-07-26-coding-standards-that-matter/readme-example.png)

*   “文档块”是一种特殊的代码注释，它解释了代码块的用途、参数、返回值和抛出异常。

[![Docblock example](../Images/be0dd5c7e9faed3731d545c3f90a13cc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YPIQ3llH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lhuria94.github.io/assets/img/2017-07-26-coding-standards-that-matter/docblock-example.png)

我在一条简单的推文中发现了一些非常有用的信息:

> ![Larry Garfield profile image](../Images/b87cea881053fdbf440c0f0799557a13.png)拉里·加菲尔德@ crell![twitter logo](../Images/74b03a903e302feaec877e37a7689665.png)如果你的 docblock 没有提供比读取 func 签名更多的信息，你只是在浪费我的时间。[# RealDocs](https://twitter.com/hashtag/RealDocs)2014 年 12 月 01 日 22 点 32 分[![Twitter reply action](../Images/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=539547553608761344)[![Twitter retweet action](../Images/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=539547553608761344)7[![Twitter like action](../Images/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=539547553608761344)6

**格式化**

这可能包括缩进、空白、括号位置和新行，根据不同的语言可能会有所不同。在我们的例子中，这是特定于 PHP (Drupal)的。编辑器中有很多插件可以美化你的代码。

**命名惯例**

*   当然，命名惯例取决于你使用的语言(例如:Zend，PHP、CSS、Javascript、Java 等的谷歌风格指南)，但主要思想是使用描述性的和有意义的单词。所以，你应该避免调用你的变量:xx，yy2，test1，test2 等等。
*   例如，对于 PHP 变量和函数使用 lower_case，对于 JS 变量和函数使用 camelCase，对于 CSS ID 和类使用小写连字符，对于常量使用 UPPER_CASE。
*   我们应该用一种容易解释变量包含的数据类型的方式来命名变量。类似地，在函数的情况下，它们应该描述它们提供什么样的功能。这被称为自文档化代码。功能应该告诉他们做什么，而不是如何做。这被称为抽象，它允许在不改变函数名的情况下改变底层实现。

**便携性**

尽可能保持代码的松散耦合。它是“可移植的”,因为将它从一个平台移动到另一个平台所需的工作量很少。在编码时，我们应该记住几件事:

*   避免使用硬编码的值，如绝对文件路径、URL 等，除非是生死攸关的事情(:P)
*   避免在代码中使用幻数。基本上，它是一个硬编码的值，在以后的阶段可能会改变，因此很难更新。除了 0、1 或 2 之外，几乎所有的数字都应该在文件的顶部赋给一个常数。如果值改变，这提供了单点改变，而不是可能影响许多文件并可能引入错误的搜索和替换。

**棉绒**

一般来说，有不同类型的工具可以用来查找语法差异，特别是在解释型语言中，如 JavaScript、PHP、Python 等。它们也可以用作简单的调试器来查找常见错误。以下是常见的棉绒:

*   PHP:

    *   我们使用 PHP 代码嗅探器与 Drupal 集成。你可以很容易地用你的编辑器来配置它，比如 Sublime，它会在保存时显示常见的 PHP 错误，这样可以节省很多时间，并在提交你的更改之前防止错误。
*   Javascript:

    *   我们已经有了 Drupal JavaScript 编码标准(注意——这些标准与 Mavericks 标准有一些不同),但是我们使用 [JSHint](http://jshint.com/docs/) 来列出我们的 JS 相关代码检查。
    *   只要 Drupal 的 JS 格式约定与 JSHint 冲突，就以 JSHint 为准。
*   SCSS:

    *   我们没有任何关于 Drupal.org 的 SCSS 相关文档，但是我们有 Drupal CSS 编码标准(这些可以应用于 SCSS 代码)。
    *   你可以在这里找到一些与 SCSSLint [相关的文档。](https://github.com/brigade/scss-lint/blob/master/lib/scss_lint/linter/README.md)
    *   此外，检查 Compass 的最佳实践也很好。

**可重用性**

这是我们正在非常广泛地进行的工作。我们正在构建可重用的组件，这些组件可以跨不同的网站使用(这些网站几乎有相同的目的)。这些组件将提供基本的功能和风格，可以根据自己的要求在不同的网站覆盖。这里最重要的事情是确定哪些功能可以转化为组件。您的代码的可重用程度在很大程度上取决于它与您的代码库的其他部分耦合的紧密程度。一个很好的例子是建立一个可以在大多数网站上使用的横幅滑块。

**模块化**

这基本上意味着保持你的代码独立于其他代码，这样你的代码的一个坏的改变不会破坏其他的一切。这类似于面向对象编程中的耦合概念。这就像把网站分成更易管理的基本独立部分。

**使用持续集成工具**

我们用特拉维斯. CI。这是一种分布式持续集成服务，用于在 github 上构建和测试项目。这项服务对开源项目是免费的。你可能想知道为什么你以前没有使用它！😛不要担心，永远不会太晚，用你的 github 库设置[非常容易。](https://docs.travis-ci.com/user/getting-started/)

*   第一步是注册 Travis-CI，您也可以使用您的 github 帐户进行注册。
*   设置. travis.yml 文件，该文件处理环境的构建以及 phpunit 文件的执行。

您可以在这里查看最简单的基本配置:

[![travis repo example](../Images/4546e6aab787e9b7dc78e6dce7e075c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uFzot9EC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lhuria94.github.io/assets/img/2017-07-26-coding-standards-that-matter/travis-repo.png)

当您准备好 phpunit 测试并且通过了测试时，它将在您的提交中显示如下内容:

[![travis success message](../Images/6ce570b5e9a4fa1a6c7f50fbb4b5ef51.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A73HLU_X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lhuria94.github.io/assets/img/2017-07-26-coding-standards-that-matter/travis-success-msg.png)

你可以点击查看简单的 Travis 设置[:](https://github.com/lhuria94/drupal/pull/3)

这个`.travis.yml`应该是项目的根。
在 Travis CI 中启用了存储库之后，Travis 只在您推送的提交上运行构建。

代码审查
我们有非常棒的代码审查流程。但是这篇博客已经太长了，所以我会在下一篇博客中讨论。敬请关注。

此处访问原帖[。感谢阅读:)](https://lhuria94.github.io/coding-standards-that-matter/)