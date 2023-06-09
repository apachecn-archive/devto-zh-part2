# 用更漂亮的替换 ESLint

> 原文：<https://dev.to/sirech/replacing-eslint-with-prettier-616>

正如我在谈论 Angular 时提到的[，我最近一直在思考 linter 在一个项目中应该扮演什么角色。](//../angular-from-react-part1/#linting)

总的来说，我是棉绒的超级粉丝。我喜欢将关于如何设计代码风格的无意义讨论外包给一个自动运行的无情程序的想法。我想象管道嘲笑最后破坏构建的人，并责骂他们:

> 没有个人，人类，但如果你不把一个空格放在括号前，我会永远阻止你的功能

Linters 有助于整个代码库的一致性。它们也有助于捕捉潜在的错误。这是真正的内置质量几乎是免费的。

然而，我最近一直在使用一些工具，如 [Terraform](https://www.terraform.io/docs/commands/fmt.html) 或 [Go](https://golang.org/pkg/fmt/) ，它们更进一步，包括一个直接格式化代码的工具，没有林挺和修复阶段。如果你仔细想想，这其实很有道理。自动检查是第一步，但是既然可以自动完成这一步，为什么还要麻烦手动修复问题呢？

## 漂亮些

在我的项目中，我越来越多地使用更漂亮的。有了这个，你实际上可以把代码的格式留给它，并使用 [ESLint](https://eslint.org/) 来捕捉其他错误。我真的很喜欢这个组合。 *ESLint* 仍然可以提供大量的价值，但是我把代码整理和格式化留给一个工具，这个工具可以根据我的喜好经常运行。

将它添加到项目中非常容易。您需要首先添加依赖关系

```
yarn add --dev prettier 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以用一个目标
来扩展`scripts`

```
"format":  "prettier --write \"**/*.+(js|jsx|json|css|scss)\"" 
```

Enter fullscreen mode Exit fullscreen mode

您可以在提交时运行它。对于这个博客来说，如果使用 [lint-staged](https://github.com/okonet/lint-staged) 像 [this](https://github.com/sirech/homepage2/blob/master/package.json#L81-L100) 一样设置它。

这是非常(咄)的。整洁和格式化的代码，您可以与像您一样痴迷于这些细节的人分享。

### 与 ESLint 友好相处

现在你不希望你的格式良好的代码触发一千个 *ESLint* 警告。幸运的是，有人已经想到了这一点。[eslint-config-appellister](https://github.com/prettier/eslint-config-prettier)允许你关闭这些规则，这样两个工具就能一起正常工作。

### 你还要吗？

一旦开始格式化，就无法停止。我们已经让它成为提交挂钩的一部分，但是为什么要等这么久呢？编辑器不是应该在保存时自动运行`prettier`吗？你打赌它应该。在我的例子中，作为一个忠实的 *Emacs/Spacemacs* 用户，我再次使用了一堆现有的包，以便在保存时让`prettier`在我的 *JavaScript* 缓冲区上运行。配置[并不漂亮](https://github.com/sirech/spacemacs.d/blob/master/layers/aj-javascript/packages.el)，但它做的工作。

## 接下来

这太酷了，为什么只为 JS 保留？我已经设法集成了`terraform fmt`和`gofmt`，我不介意为我必须接触的每一种语言做类似的事情(甚至是 *bash* )。