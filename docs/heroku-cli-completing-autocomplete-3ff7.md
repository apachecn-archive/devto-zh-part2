# Heroku CLI:完成自动完成

> 原文：<https://dev.to/heroku/heroku-cli-completing-autocomplete-3ff7>

Heroku CLI 团队努力创造直观且高效的 CLI 用户体验。多年来，我们一直将“构建 CLI 自动完成”放在路线图的冰箱里。但是如果我们要发布它，它必须补充现有的 CLI 体验。这很有挑战性，因为 Heroku CLI 是非常动态的:它包含用户可安装的插件，完成所需的数据在 API 后面。

最近，我们花了一些时间集思广益，从 Heroku CLI 自动完成中获得我们想要的体验，并决定是时候了。我们把“构建自动完成”从冰箱里拿出来，然后[把它运出去](https://blog.heroku.com/announcing-cli-autocomplete)。

这篇文章将讨论我们在构建 Heroku CLI Autocomplete 时面临的主要挑战，以及我们是如何解决这些挑战的。

## 挑战

下面是每个挑战的快速概述。

基于插件的 CLI:Heroku CLI 的命令集可使用用户安装的插件进行扩展。这意味着不同的 CLI 用户可能安装了不同的命令。Heroku CLI Autocomplete 需要为用户安装的任何插件集处理命令完成。

多种多样的 shell 配置 : Heroku CLI Autocomplete 必须能够适应多种多样的 shell 配置，并允许我们更新 Autocomplete 代码，而不需要用户在每次更新时编辑他们的 shell 配置文件。

API 背后的完成数据:大多数自动完成系统使用本地数据，如文件名和 git 分支，而大多数 Heroku CLI 数据，如应用程序名或配置变量，都在 API 背后。需要获取和缓存这些数据。

**缓存失效**:使用缓存意味着我们需要处理缓存失效。如果没有缓存失效，完成数据可能看起来“停留在过去”，与 API 相比，应用程序或配置变量的列表不一致。

**上下文命令完成**:为了让 autocomplete 对更高级的用例真正有用，我们希望它能完成只有在其他数据比特已经被指定之后才能知道的数据。例如，要完成一个附加组件的名称，我们首先必须知道是哪个应用程序，然后 autocomplete 可以返回附加到该应用程序的附加组件的名称。

## 基于插件的 CLI

从概念上讲，自动完成很简单。您定义了一个函数，每当用户提示完成帮助时，shell 的完成系统都会调用这个函数——通常是通过点击`Tab`。这个函数向 shell 的完成系统返回可能的完成值。这个函数的内部工作——返回什么完成值以及何时返回——是复杂性所在。

大多数命令行工具的命令、参数和值没有太大变化。例如，下面是`cat`命令可用的选项，用户不能更改这些选项，除非他们安装不同版本的`cat`。

[![cat-command](img/875f75189a55ce755868ef67503b2b8b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vrm4phPh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://heroku-blog-files.s3.amazonaws.com/posts/1528766530-cat-command.png)

大多数自动完成功能的实现——如`cat`的自动完成——是一个充满 case 语句的静态文件。然而，Heroku CLI 的超能力之一是能够使用插件来增强其功能。用户可以添加和删除插件，根据自己的需求定制 CLI。没有两个用户的 Heroku CLI 可以假设完全相同。这意味着我们不能只定义 case 语句的静态文件。相反，我们需要一个能够处理任何 Heroku CLI 插件集以及所有相关命令、参数和标志的自动完成功能。

对于 Heroku CLI Autocomplete，我们没有定义数百个 case 语句，而是定义了一个包含适当完成值的变量。然而，直到您要求完成值(即点击`Tab`)时，这个变量才被赋值。

为了让这个变量在你点击`Tab`时有一个合适的值，我们需要事先做一些工作。当您运行`heroku autocomplete`并看到输出`Building the autocomplete cache...`时，自动完成缓存构建器正在遍历所有可用的命令，包括来自您已经安装的插件的命令。随着它的迭代，我们创建 setter——为变量赋值的函数——用所有必要的信息为安装的命令提供完成结果。当用`Tab`执行时，autocomplete 函数调用适当的 setter 来提供所有可用命令的列表。或者确定命令名已经存在，并使用该命令名调用相应的 setter，该 setter 包含完成该命令的标志名或标志值所需的所有信息。

这种使用生成的 setters 的动态完成有助于 autocomplete 适应每个用户定制的 Heroku CLI。

## 广泛可变的外壳配置

Heroku CLI Autocomplete 的初始设置要求用户修改他们的 shell 配置文件——`.bashrc`或`.zshrc`文件。向 shell 配置文件中添加任何内容都很棘手。贝壳就像人的办公室。开发人员在其中花费了大量时间，它们的平稳运行对于完成工作至关重要。有些是高度定制和装饰。有些很简单。有些使用预定义的设置(例如 [oh-my-zsh](http://ohmyz.sh/) 、 [prezto](https://github.com/sorin-ionescu/prezto) 或 [bash-it](https://github.com/Bash-it/bash-it) )。有些保养得很好，有些有点破损。通过自动完成，我们正在将软件部署到类似的环境中。我们不知道它将如何建立，我们对它几乎没有控制，我们试图帮助永远不应该成为障碍。

我们用一个垫片来解决这个问题。在安装过程中，Heroku CLI Autocomplete 会要求您在 shell 配置文件中提供一个 shim 路径。这个垫片是用户缓存目录中一个在我们控制下的文件(更多关于 [XDG 数据目录规范](https://standards.freedesktop.org/basedir-spec/basedir-spec-latest.html))。如果由于意外的问题而找不到 shim 文件，我们会静默失败，以免阻塞用户的工作流。如果 Heroku CLI Autocomplete 不工作，那是不理想的，但它的失败不应该打破用户外壳的其他方面。获得这个 shim 文件还允许我们在将来的更新中修复错误和添加特性，而用户不必再次编辑他们的 shell 配置文件。

## API 背后的完井数据

对于大多数命令行工具，完成标志或参数所需的数据在本地磁盘上。例如，`git` autocomplete 从磁盘获取分支、远程和标记名的完成值。相比之下，Heroku CLI 的标志和参数值大多不在磁盘上。相反，它们是 Heroku API 的幕后推手。这包括应用程序名称、配置变量、管道和一些其他值。

当您点击`Tab`时，autocomplete 函数从 API 中获取这些值。因为[网络请求可能比磁盘读取](http://norvig.com/21-days.html#answers)慢三个数量级，我们将这些值缓存到磁盘以备将来完成。您可能会注意到一次完成比另一次稍长，这可能是因为缓存无效，需要网络请求来重新填充它。

## 缓存失效

因为我们为完成数据使用了缓存，所以我们需要一些缓存过期的机制。当我们第一次开始构建 Heroku CLI Autocomplete 时，我们使用计时器来使缓存无效——这是一种常见的做法。但是在一些 Heroku CLI 用例中，这可能会导致混乱的用户体验。例如，如果用户创建了一个新的应用程序，并且缓存到期计时器还有一个小时，则新的应用程序直到一个小时后才会显示在自动完成结果中。同样，如果用户删除了一个应用程序，该应用程序将继续显示在自动完成结果中，直到计时器触发缓存刷新。

缓存失效是计算机科学中的两大难题之一。然而，今年春天我们将 Heroku CLI 迁移到了最近开源的 CLI 框架 [oclif](https://oclif.io/) 。这样，使用 [oclif 的定制钩子](https://oclif.io/docs/hooks.html#custom-events)，更加智能的缓存失效变得轻而易举。现在，单个命令可以发出一个定制的钩子事件，Heroku CLI Autocomplete 插件可以订阅该事件。然后，插件挂钩会失效，在某些情况下，会重建适当的完成缓存。更好的是，有了 oclif，就没有了与定制钩子的依赖耦合。如果触发了一个挂钩事件，但没有订阅任何内容(例如，没有安装自动完成功能)，CLI 生命周期将继续，不会产生错误。

## 上下文命令完成

这是 Heroku CLI Autocomplete 最有趣和最复杂的特性，也是它提供巨大好处的地方。通常，很难记住应用程序的确切加载项名称或配置变量，但用户必须在许多 CLI 命令中键入这些值。如果没有自动完成，解决此问题的方法是调用另一个 CLI 命令来检索附加组件的名称或配置变量，并在需要时将它们复制/粘贴到下一个 CLI 命令中。消除这个额外的手动步骤是 autocomplete 要解决的理想问题。

解决这个问题是迄今为止最困难的挑战，需要另一个帖子来全面解释。但简而言之，autocomplete 读取已经输入到命令行的内容，例如，`heroku addons:info --app=serene-hollows-34516`，并解析它以确定当前的上下文。在解析中，我们可以判断是否提供了所有的参数，存在什么标志以及已经提供了什么标志，然后寻找只有在解析的上下文中才能知道的附加完成值。

例如，在上面提到的`addons:info`示例中，应用程序名称`serene-hollows-34516`已经在命令中指定，因此我们可以从 Heroku API 获取应用程序的附加别名，并将其作为完成值返回。

[![heroku-cli-addon-autocomplete](img/642e18bb5a36194afbcf94625210b2b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JLBGRbMI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://heroku-blog-files.s3.amazonaws.com/posts/1528768570-heroku-cli-addon-autocomplete.gif)

## 向前移动

许多开发人员正在我们的开源框架上构建他们自己的 CLI。我们致力于为 Heroku CLI 构建特性，作为 oclif 的开源组件。为此，我们正在将我们在开发 Heroku CLI Autocomplete 中所学到的内容整合到一个 [oclif 插件](https://oclif.io/docs/plugins.html#useful-plugins)中。Oclif 开发者可以在我们的 [oclif Gitter](https://gitter.im/oclif/oclif) 中了解更多关于试用这个插件的信息。

我们希望您和我们一样喜欢使用 Heroku CLI 自动完成功能。请将任何反馈发送给[ecosystem-feedback@heroku.com](mailto:ecosystem-feedback@heroku.com)。