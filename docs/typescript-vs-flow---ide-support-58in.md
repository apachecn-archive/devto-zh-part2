# Typescript 与 Flow - IDE 支持

> 原文：<https://dev.to/michaeljota/typescript-vs-flow---ide-support-58in>

这是比较 Typescript 和 Flow 系列文章的第二篇。

*   [入门](https://michaeljota.github.io/typescript-vs-flow-getting-started)
*   IDE 支持

* * *

在这篇文章中，我将寻找最流行的 IDE，并看看它们如何支持和集成 Typescript 和 Flow。我不会使用任何 IDE，所以这不包括可用性，那是另一篇文章。如果支持来自第一方或第三方，我将检查有多少 IDE 支持，以及每种支持有多少特性。出场顺序将按字母顺序排列。为了 web 开发，我还会添加文本编辑器。

# [Atom](https://atom.io)

> Atom 是一个免费的开源流行文本编辑器，由 Github 使用 web 技术创建。它有插件支持，并且有许多现成的功能。

## 流量

Atom 不支持开箱即用的流程。但是它有几个可用的插件。

### [核素](https://nuclide.io)

> 核素是一个完整的 ide，由脸书的人创建，内置了对流量的支持。它提供了一个 linter，自动完成和类型覆盖支持，点击定义和悬停类型描述。
> 
> 然而，目前**还不支持即时类型检查**(在你保存文件之前显示你的类型错误)。

### [Flow-IDE](https://atom.io/packages/flow-ide)

> Flow-IDE 是一个更小的包，它只为您提供了 linter 和 autocomplete 功能。它目前也缺乏对实时林挺的支持。

### [棉绒流](https://atom.io/packages/linter-flow)

> 如果你在寻找更小的东西，棉绒流可能值得你注意。它只链接你的代码，不提供其他功能，但是它支持动态林挺。

### [自动完成-流程](https://atom.io/packages/autocomplete-flow)

> autocomplete-flow 是另一个专门构建的工具，它只做一件事。顾名思义，这个包将为您的启用流的代码自动完成提供建议，除此之外别无其他。

* * *

所以有四个选项，但实际上我们似乎至少要结合其中的三个才能有真正的 IDE 体验。我想如果我不得不选择 Atom，我可以选择`Nuclide`和`Linter-Flow`，看看它是如何工作的，但我使用 Windows 进行开发，只是因为，`Nuclide`并不完全支持 Windows。所以，那个。

## 打字稿

### [原子打字稿](https://atom.io/packages/atom-typescript)

> 由 TypeStrong 开发的 Atom 的类型脚本语言服务

嗯，我们没有太多的 Typescript 选项，实际上，有几个地方我们会有那么多的选项。这个要看其他包`atom-ide-ui`。另一方面，它有几个常用功能，如自动完成、实时错误分析、悬停时的类型描述、点击定义和保存时编译等。所以，我想你会从中获得所有的 IDE 经验。

# [Emacs](https://www.gnu.org/software/emacs)

> 一个可扩展、可定制的免费/自由文本编辑器，等等。

## 流量

### [流换电子邮件](https://github.com/flowtype/flow-for-emacs)

> 一个用于 Flow 的 emacs 插件，一个用于 JavaScript 的静态类型检查器。
> 
> *   显示了用 Flow 对 JavaScript 代码进行类型检查时发现的错误。
> *   提供了一系列由 Flow 支持的通用 IDE 特性，以帮助读写 JavaScript 代码。

我没用过 emacs，所以不知道对此有什么期待。我知道你需要设置你的环境来处理这个问题。然而，它是官方支持的，所以这是一件大事。

## 打字稿

### [潮](https://github.com/ananthakumaran/tide)-Emacs 的打字稿交互开发环境

我对此也有同样的问题。但它似乎比 Flow 有更多可用的功能。也许你们其中一个可以帮我。

# [崇高的文字](https://www.sublimetext.com/)

> 一个复杂的代码、标记和散文的文本编辑器。

## 流量

### [流](https://packagecontrol.io/packages/Flow)

> SublimeText 2 和 3 的流 JavaScript 分析器插件

### [【潜流】](https://packagecontrol.io/packages/SublimeLinter-flow)

> 用于 JavaScript 静态类型检查的 SublimeLinter 插件，使用 flow。

* * *

再说一次，我们有选择。第一个实际上已经超过 3 年没有更新了。它似乎只能在 Linux 和 MacOS 上运行，但它确实具有 Flow 的所有常见功能。另一个，看到了更多的最新版本，但它似乎是一个只有 Linter 的插件。

## 打字稿

### [打字稿崇高插件](https://github.com/Microsoft/Typescript-Sublime-Plugin)

> 该插件在处理类型脚本代码时，使用类型脚本语言服务周围的 IO 包装器来提供增强的崇高文本体验。

这是微软的一个官方插件，它似乎拥有 Typescript 的所有常用功能。它定期更新，也支持 Linux，Windows 和 MacOS。

# [维姆](https://www.vim.org/)(以及[尼奥维姆](https://neovim.io/))

> Vim 是一个高度可配置的文本编辑器，可以非常高效地创建和更改任何类型的文本。

## 流量

### [ale](https://github.com/w0rp/ale) -异步 Lint 引擎(Vim 和 NeoVim)

> 用于 Vim 8+和 NeoVim 的异步 Lint 引擎(ALE) vim-ale 插件在文件保存之前的编辑期间提供了许多语法的林挺。支持的 JavaScript linters 包括 eslint、jscs、jshint、flow、standard 和 xo。

### [Vim-flow](https://github.com/flowtype/vim-flow) (仅限 Vim)

> 一个流的 vim 插件。
> 
> *   向 omnifunc 添加完成
> *   保存时检查 JavaScript 文件的类型错误

* * *

这两个选项似乎涵盖了基本的功能，但是`ale`在运行中进行检查，而`vim-flow`等待您保存。另外，`ale`集成了其他常用的工具用来除毛。`vim-flow`是官方工具，但是，即使在`flow` IDE 页面也是第二次选择了，所以。我想我会选择第一个。

## 打字稿

### [nvim-typescript](https://github.com/mhartington/nvim-typescript) (仅限 NeoVim)

> 提供类似于 IDE 的标准功能，例如自动完成、查看文档和类型签名、转到定义以及查找参考。

### [打字稿-vim](https://github.com/leafgarland/typescript-vim) (仅 vim)

> Vim 的 Typescript 语法。

### [yats.vim](https://github.com/HerringtonDarkholme/yats.vim) (仅 vim)

> 另一种类型脚本语法:Vim 中最高级的类型脚本语法突出显示

### [Tsuquyomi](https://github.com/Quramy/tsuquyomi) (仅限 Vim)

> 让您的 Vim 成为一个类型脚本 IDE。

* * *

你可能不得不选择一个语法高亮，并结合 Tsuquyomi。Tsuquyomi 是 TSServer 的一个客户端，它允许你使用补全、转到定义和其他特性来获得类似 IDE 的体验。它需要一个缓冲区来保存检查文件。您可以使用命令手动检查，也可以使用另一个命令检查整个项目。

# [Visual Studio 代码](https://code.visualstudio.com/)

> 代码编辑。重新定义。免费。开源。到处跑。

## 流量

### [流语言支持](https://marketplace.visualstudio.com/items?itemName=flowtype.flow-for-vscode)

> 这提供了你所期望的所有功能——林挺、智能感知、类型工具提示和点击定义。它很稳定，正在积极开发中。

### 

> Visual Studio 代码的替代 Flowtype 扩展。Flowtype 是一个静态类型检查器，用于查找 Javascript 程序中的错误。

* * *

官方插件 Flow Language Support 似乎拥有你在类似插件中看到的所有特性，比如智能感知、转到定义、错误和警告诊断等等。Flow IDE 似乎也具备所有这些特性。

## 打字稿

VSCode 内置了 Typescript 支持。VSCode 提供了自己的 Typescript 版本，因此您不需要进行任何配置。

# [网络风暴](https://www.jetbrains.com/webstorm/)

> 最智能的 JavaScript IDE。现代 JavaScript 开发的强大 IDE。

Flow 和 Typescript 在 Webstorm 中都有一流的支持。但是，您需要首先配置 Flow 才能使用它，而要使用 Typescript，不需要预先配置。

* * *

# 附加支持的 IDE

## 流量

实际上，根据文档，流支持仅限于已经列出的 IDE。

## 打字稿

Typescript 支持其他 IDE:

*   [alm.tools](https://alm-tools.github.io) -完整的打字稿 IDE。用打字稿写的。
*   CATS-完整的打字稿集成开发环境。用打字稿写的。
*   [月食](https://www.eclipse.org/)
    *   [Typescript IDE](https://github.com/angelozerr/typescript.java/wiki/Getting-Started) -一组 Typescript 插件。
    *   [Eclipse Typescript](https://github.com/palantir/eclipse-typescript)——一个用 Typescript 语言开发的 Eclipse 插件。
*   [NetBeans](https://netbeans.org/)
    *   [nbts](https://github.com/Everlaw/nbts) - NetBeans 类型脚本编辑器插件
*   [记事本++](https://notepad-plus-plus.org)
    *   [Notepad ++ Typescript](https://github.com/chai2010/notepadplus-typescript)-Notepad ++对 Typescript 的彩色化支持。
*   Visual Studio -一个完整的 IDE，支持多种开发工作流。仅在 2015 年和 2017 年支持最新的 Typescript。

# 结论

我认为，总的来说，Typescript 支持似乎更好，大多数支持 Typescript 的插件和 IDE 都使用 Typescript 语言服务，我认为这是向客户端并最终向用户提供功能的好方法。

Flow 好像也有这个权利，Flow 语言服务器也早就出了，但是像今天这样用这个的好像只有 Atom。我希望将来更多的插件和 IDE 能够改进流支持，因为最终他们会实现一个很长的扩展协议，语言服务器协议。今天，Windows 似乎不是一个非常受支持的工作流平台，但他们最近发布了一个 Windows 二进制文件，所以我认为这可以改变。

> 用 Flow 开发更好的有 Unix 类的机器使用核素。
> 
> 即使在有更多选择的情况下使用 Typescript 进行开发，使用 VSCode 也是安全的。
> 
> ~~无论哪种方式，你都会被消耗你灵魂的电子所困~~

即使你可能会有同样的经历，用核素编码流，用 VSCode 编码类型脚本，你甚至不能选择 SO 的事实让我很烦。如果您想在安全区域之外，您将不得不在需要配置 Typescript 的大部分时间里安装和配置 Flow。因此，我会给 Typescript 打 9 分，给 Flow 打 6 分。你可能会认为这不公平，但请坚持我的观点，主要问题是你需要一台 Linux 机器或 Mac，以便能够运行具有最佳流支持的编辑器，但如果你不想要一个微软编辑器，你可能会以一个非常好的体验结束。

# 当前得分

| 特征 | 以打字打的文件 | 流动 |
| --- | --- | --- |
| 入门指南 | Seventeen | Sixteen |
| 编辑器支持 | nine | six |
| 总数 | Twenty-six | Twenty-two |

# 那都是乡亲们！

感谢您的阅读。给我所有的新粉丝。请检查我的其他职位。我主要写角度和打字稿。我期待在你的评论中读到你对这个系列的想法。