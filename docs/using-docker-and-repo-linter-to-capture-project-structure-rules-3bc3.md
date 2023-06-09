# 使用 Docker 和 Repo Linter 捕获项目结构规则

> 原文：<https://dev.to/youknowfordevs/using-docker-and-repo-linter-to-capture-project-structure-rules-3bc3>

拥有大量开源项目令人沮丧的一件事是，嗯…拥有大量开源项目。有些问题是显而易见的，比如如何让项目继续进行，响应拉取请求，修复 bug 和添加特性。但是还有很多不太明显的事情需要处理，比如一旦你决定了一些最佳实践，如何保持整个项目的一致性。

# 标准

[![Standard JavaScript](img/86de9936dd6a264e2b932ac162ac8969.png)T2】](https://standardjs.com)

假设我们已经遇到了[standards](https://standardjs.com/)(我们已经有了)，并且我们想把它应用到我们所有的项目中(我们已经做了)；我们该怎么做？显然，我们必须在本地克隆每个项目，运行 StandardJS 来重新格式化文件，对结果进行测试，提交更改，并提交一个 pull 请求。然后，当我们意识到我们忘记给每个文件添加 StandardJS 徽章时，我们要重新跳一遍。

即使我们将对大量项目进行大量变更的困难放在一边(至少目前是这样)，我们如何确保我们创建的任何新项目都遵循我们采用的任何新约定？

# 奥卡姆剃刀

拿一些非常简单的东西，比如一个`AUTHORS`文件。在一个节点项目中，我们可以将贡献者的[名字放到`package.json`文件](https://docs.npmjs.com/files/package.json#people-fields-author-contributors)中，或者 NPM 将从`AUTHORS`文件中提取贡献者的名字，如果有的话。哪种方法更好？在我看来，后者更好，因为`AUTHORS`文件是一种约定，你会在许多不同类型的开源项目中发现，而不仅仅是 Node。我个人的原则是，如果有一个独立于语言的约定可以免费遵循，那么我会一直遵循它，而不是使用一个特定于语言的对等物。(想想[奥卡姆剃刀](https://simple.wikipedia.org/wiki/Occam%27s_razor)。)

[![Stained-glass window showing William of Ockham](img/d17d58a14c90d2c8774ffab4a427a216.png)](https://commons.wikimedia.org/wiki/File:William_of_Ockham.png#/media/File:William_of_Ockham.png) 

By 自创(Moscarlop) -自己的作品， [CC BY-SA 3.0](https://creativecommons.org/licenses/by-sa/3.0) ，[链接](https://commons.wikimedia.org/w/index.php?curid=5523066)

但是不管我们以哪种方式来跟踪贡献者，关键是我们已经思考了一个问题，我们已经做出了一个决定，结果是一个简单的规则，我们可以很容易地在我们所有的项目中遵循它(不管语言如何)——在我的例子中是*总是有一个`AUTHORS`文件*。如果我们能记住这条规则，那么我们就可以避免每次开始一个新项目时都要考虑它；毕竟，没有什么比再次经历完全相同的决策过程，只是为了得出相同的结论更糟糕的了。

所有这些都意味着我们把问题向前推进了一点。以前我们每次开始一个新的节点项目时都必须决定是将贡献者的名字放在`package.json`文件还是`AUTHORS`文件中，现在的问题是*我们如何记得我们上次决定了什么？*至少这个问题的好处是，无论我们试图解决什么问题，它都是一样的……我应该使用哪个许可证呢？我应该把哪个版本的节点放在我的`Dockerfile`里？诸如此类。

一旦决定了这些规则，我会考虑各种方法来捕捉它们。它们可以写在笔记本上，放在你桌子最上面的抽屉里。但是我想要的一个标准是让规则可以分享和讨论。这意味着，即使讨论规则的唯一的人是我自己*和我自己*，有一个空间来权衡使用[的优点和缺点，比如说，直接使用 StandardJS 而不是 ESLint](https://standardjs.com/#i-disagree-with-rule-x-can-you-change-it)，也是很方便的。(更别提要不要用分号了。)因此，虽然某种共享文档会很好，但 Git 回购会更好。

# 指定规则

假设这些知识是在一个存储库中获取的，并具有问题跟踪和评论的所有好处，那么规则实际上应该采取什么形式呢？

它们可以用散文来写，比如*总是使用`AUTHORS`文件*，这肯定会是一个很好的开始。但是，更好的办法是找到一种方式来表达这些规则，使得它们可以通过软件来执行。在我研究和谷歌搜索的这一点上，我遇到了[回购 Linter](https://github.com/todogroup/repolinter) 。

[![](img/bdeef2dc672aa4afa1a8b90d18b8f598.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--y1Vl13n8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/todogroup/repolinter/raw/master/doimg/P_RepoLinter01_logo_only.png)

## 回购短绒

Repo Linter 包含一组通用测试，如*检查某个文件是否存在*或*检查一组文件是否包含指定的文本*。这些测试可以用来创建更具体的*规则*，比如*readme . MD 文件存在吗？*或*是否所有源文件的顶部都有版权信息？*规则然后被进一步组合到*规则集*中，以创建您想要为您的项目实施的策略的精确集合。

举例来说，包含两个规则的规则集——一个检查是否有许可证，另一个检查是否有`README`——可能如下所示:

```
{  "rules":  {  "all":  {  "license-file-exists:file-existence":  [  "error",  {"files":  ["LICENSE*",  "COPYING*"]}  ],  "readme-file-exists:file-existence":  [  "error",  {"files":  ["README*"]}  ]  }  }  } 
```

这非常强大，但是 Repo Linter 更进一步，允许我们指定特定于某些语言的规则(上面的规则将适用于所有语言)。例如，为了确保我们所有的节点项目都包含一个`package.json`文件，我们应该添加这个:

```
{  "rules":  {  "all":  {  ...  },  "language=javascript":  {  "package-metadata-exists:file-existence":  [  "error",  {"files":  ["package.json"]}  ]  }  }  } 
```

所以现在我们有了一种在简单的 JSON 文件中表达规则的方法。剩下的就是将它们用于回购。

# 答案是 Docker(现在…问题是什么？)

将这些规则转换成可重用形式的最简单的方法是创建一个 Docker 映像，它将 Repo Linter 和一个配置文件捆绑在一起，用于一些特定的规则集。我已经创建了一个安装 Repo Linter 及其依赖项的基本 Docker 映像，并添加了一些简单的规则来检查许可证、`AUTHORS`和`README`文件。还有一些说明可以帮助在此基础上创建新的图像，以便可以添加更具体的规则。回购是[markbirbeck/docker-repo linter](https://github.com/markbirbeck/docker-repolinter)，它生成的 Docker 映像在[markbirbeck/Docker-repo linter](https://hub.docker.com/r/markbirbeck/docker-repolinter/)的 Docker Hub 中。

你也可以跳过这一步，直接进入我建立的回购协议，做我在这篇文章中描述的事情——捕获规则，并有一个可以讨论它们的地方。该回购位于[markbirbeck/my-repo linter](https://github.com/markbirbeck/my-repolinter)，复制它并创建一个捕获您自己的策略的 Docker 映像应该非常简单。

# 下一步去哪里？

在以后的文章中，我会看看我们如何去*生成*任何我们检测到丢失的文件。