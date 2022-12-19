# 带 PowerShell 的新节点模块

> 原文：<https://dev.to/hf-solutions/new-node-modules-w-powershell-2fje>

我从来都不是一个 OS/平台纯粹主义者。从我记事起，由于各种原因，我就经常在基于 Unix/Windows 的系统之间切换。也就是说，我主要在 Windows 机器上进行开发，我的大多数项目最终都运行在 Linux 服务器(节点模块)或某种 Linux 内核(Android)上，所以从长远来看，熟悉*nix 学派是非常有帮助的。

[![Coding_Image.png](../Images/516e9febc4370283ace78caa560922be.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w_0P89A8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1487058792275-0ad4aaf24ca7%3Fixlib%3Drb-0.3.5%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26s%3D6726719ee78dabe78033950d9f3f7145%26auto%3Dformat%26fit%3Dcrop%26w%3D1950%26q%3D80)

我不想过多地列举生活在不同平台之间的个人例子，而是想分享一些我写的东西或我用来让我的生活变得更轻松的工具。

这篇文章明确介绍了我编写的一个脚本，并添加到我的当前用户**，当前主机控制台** [PowerShell 配置文件](https://blogs.technet.microsoft.com/heyscriptingguy/2012/05/21/understanding-the-six-powershell-profiles/)中，以使初始化新的 Node.js 模块更加容易，并符合我的偏好。

如果你愿意，你可以直接跳到 GitHub 上的代码，如果这更符合你的风格，或者继续阅读我对我的问题和解决方案的解释。

## 什么问题？

嗯，与其说这是个问题，不如说是个麻烦。当创建新的节点模块时，我的标准工作流程是这样的:

1.  打开 Visual Studio 代码
2.  进入**文件**->-**打开文件夹**
3.  导航到 my Node.js 模块文件夹->为模块创建一个文件夹
4.  打开新创建的文件夹并启动集成终端
5.  使用`npm init`用我的默认值设置`package.json`
6.  使用`touch index.js; code index.js`创建新文件并开始工作

说这有点乏味和烦人是一种轻描淡写的说法。

## 我的方案是什么？

我创建了一个名为`New-NodeModule.ps1`的 PowerShell 脚本，只有一个函数`New-NodeModule`，我在我的`Profile.ps1`中点源。这个函数做了我在创建新模块时做的最常见的事情，比如用许可证、作者信息等的默认值创建一个`package.json`。

创建新模块唯一需要的信息是模块的名称，它被接受为第一个参数或者使用一个像`New-NodeModule -ModuleName outside-cli`这样的命名参数。如果需要，您可以使用`-ModulePath`参数设置模块的存储位置。此外，关于接受的参数，我更喜欢在创建完新创建的模块后立即在 **Visual Studio 代码**中打开它，因此有一个默认为`$true`的`-OpenInVSCode`参数，但可以设置为`$false`以防止打开新创建的模块。

## 默认值是什么？

当创建一个新的节点模块时，我倾向于使用相同设置的 *lot* 。例如，我总是使用 [Jest](https://facebook.github.io/jest/) 进行测试，我所有的项目都使用[standards](https://standardjs.com/)进行格式化/标记。更具体地说:

1.  当我创建模块时，我可以确信我是作者，所以我总是让作者块充满关于我自己的信息
2.  我总是将`start`脚本作为一个简单的`node .\index.js`
3.  我提到我用**笑话**测试，所以我的`test`脚本将总是以`jest`开始
4.  我更喜欢我的项目被授予麻省理工学院的许可，因为这最符合我对 OSS 的看法

要了解更多细节，请参考源代码本身，因为它就在那里。🤓

## 结论

总之，我为了让这个新文件更容易使用而做的最后一个重大改变是给`New-NodeModule`函数添加别名(`nodemod`、`new-nodemod`、`newmod`、`newmodule`、`newnpm`)。现在，每当我想创建一个新模块时，我只需在我的终端中键入`newmod`,它就会做我需要的事情😊

如果你有任何问题，评论，或关注，请随时直接联系我或评论帖子！