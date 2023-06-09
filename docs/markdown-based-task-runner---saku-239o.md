# 基于降价的任务运行器- 'saku '

> 原文：<https://dev.to/kt3k/markdown-based-task-runner---saku-239o>

# TL；博士；医生

*   用`make`命令当任务跑者很痛苦。为每一项任务写指令时，这尤其令人难以忍受。
*   我找到了一些简单的替代品(比如 [go-task](https://github.com/go-task/task) 、 [robo](https://github.com/tj/robo) )，但我不喜欢它们。
*   我想到了 markdown 似乎是任务定义的一个更好的语法。
*   我做了一个基于 markdown 的任务运行器，名为 [saku](https://github.com/kt3k/node-saku) ！

# 动机

因为我一直在工作和爱好中使用 JavaScript，所以我使用 [npm run-script](https://docs.npmjs.com/cli/run-script) 作为我的任务运行器。有一些缺陷，比如它不能以一种体面的方式被很好地记录，或者它有点难以阅读，因为它是用 JSON 文件写的。但在我看来，在像 [npm-run-all](https://github.com/mysticatea/npm-run-all) 、 [cross-env](https://github.com/kentcdodds/cross-env) 等工具的支持下，这是定义必要任务的足够工具。

有一天，我开始使用 [terraform](https://www.terraform.io) 进行我们团队的基础设施管理，我正在为它建立 git 存储库。

我在网上搜索如何建立 terraform 项目，许多人似乎在使用 Makefile 来组织其中的命令。

我觉得用 task runner 调用 terraform 是个好主意，因为`terraform`太长了，无法键入，但我也对 2018 年使用 Makefile 感到不舒服。一遍又一遍写高深莫测的`.PHONY`指令，真让人郁闷。首先`make`不是一个运行任务的工具，而是一个编译 C、C++等语言源代码的工具。使用`make`作为任务运行器是一个*黑客*，我认为在 2018 年肯定有一些其他工具更适合运行任务。

# go-任务

我找到了几个工具。其中， [go-task](https://github.com/go-task/task) 似乎最接近我要找的。 [go-task](https://github.com/go-task/task) 是一个简单的工具。它使用 yaml 格式进行任务配置。

然而，我觉得有些不对劲。这是 yaml DSL 中的关键名称。在 [go-task](https://github.com/go-task/task) 中，你编写命令并描述为键`cmds`和`desc`的值。

```
tasks:
  build:
    cmds:
      - go build -v -i main.go

  assets:
    cmds:
      - minify -o public/style.css src/css 
```

在我看来，这些键名看起来有点多余，而且似乎很吵。在 Makefile 中，目标用它们的名字和冒号表示，命令用制表符缩进表示。

```
build:
    go build -v -i main.go

assets:
    minify -o public/style.css src/css 
```

那更优雅。因为我正在寻找更优雅的替代符号`make`，如果符号变得不那么优雅，那就太不幸了。

# 认为语法

我考虑过是否有可能用 yaml 语法构建更简单的配置版本，但我放弃了，因为 yaml 的特性决定了这似乎是不可能的。在 yaml 中，如果你需要定义一些东西，你总是需要`key: value`对任何东西，我觉得不可能有比 [go-task](https://github.com/go-task/task) 的设计更简单的版本了。

然后我考虑了几种我熟悉的语言，最后我发现 markdown 非常适合我的需要，因为它允许在不同的上下文中的许多地方使用裸字符串。

# 基于降价的任务运行器

我找不到基于 markdown 的任务运行器，因此我创建了一个。我给它取名 [saku](https://github.com/kt3k/node-saku) ！

我使用`remark`作为 markdown 解析器，使用`minimist`作为 CLI 选项解析器。我重用了几个工具链来构建 CLI 基础结构，这些工具链是我为编写其他工具如 [kocha](https://github.com/kt3k/kocha) 或 [bulbo](https://github.com/kt3k/bulbo) 而准备的。我从 yarn 那里偷了一些技术，只在 TTY 环境下显示表情符号。

我根据 DDD 书教我的来模拟 saku 的领域。我非常喜欢这种编程风格。建模使得软件开发非常简单，易于推理和测试。在编写 suku 的过程中，我创建了唯一的一个模型类`Task`,整个实现看起来非常有条理，☺️.

# 🐕喂狗

在第 12 次提交时，我开始在 saku.md 中写下构建 saku 所需的任务。

```
# test
> Runs unit tests npx kocha --require src/__tests__/helper src{/,/**/}__tests__/*.js # lint
> Runs lint checks npx standard # fix
> Runs auto fixer of lint tool npx standard --fix # cov
> Makes coverage reports npx nyc --reporter=text-summary --reporter=lcov saku test # codecov
> Posts reports to codecov.io saku cov
    npx codecov 
```

不出我所料，感觉很好写，意外的发现 github UI 中 saku.md 的渲染[好漂亮](https://github.com/kt3k/node-saku/blob/master/saku.md)。

# 总结起来

*   从零开始创造东西真是太有趣了！😄
*   虽然 markdown 没有像 yaml 那样自然的逻辑结构，但我个人认为它作为 DSL 有很大的潜力，因为它有很多地方可以不用引号来编写裸露的字符串。

# 更新时间:2018-05-12

我已经用 go 语言完全重写了 saku。请检查[这个版本](https://github.com/kt3k/saku)😉