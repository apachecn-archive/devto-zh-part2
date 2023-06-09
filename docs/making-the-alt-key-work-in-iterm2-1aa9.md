# 让 Alt 键在 iTerm2 中工作

> 原文：<https://dev.to/clairecodes/making-the-alt-key-work-in-iterm2-1aa9>

[iTerm2](https://www.iterm2.com/) 是 MacOS 上默认终端应用的替代品。它有许多比终端更方便的[功能](https://www.iterm2.com/features.html)，可以免费下载，也是[开源的](https://github.com/gnachman/iTerm2)🎉。

然而，在初次安装时，你不能像在其他应用程序中那样使用 Option 或 Alt 键(看起来像这样的键:`⌥`):按 Alt 和左右键是不可能跳过或跳过单词的。相反，你会看到类似于`[D`或`[C`的序列:

[![Broken alt key displaying escape sequences](img/705d27f03ae9f01a1c17bc07409eece3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nPAPRdBa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zy18ortvk2ibppsyrldx.gif)

这可以通过调整几个设置来改变，我将在下面解释。

*注意:*我在这篇文章中提到了[【Alt】](https://en.wikipedia.org/wiki/Alt_key)键，因为这是写在我的旧 2013 MacBook Pro 键盘上的文字，但这通常在 MacOS 中被称为“Option”键。

* * *

打开“首选项”菜单:要么在屏幕顶部的“iTerm2”下拉菜单中找到它，要么按下 Command 和逗号键。`⌘` + `,`

[![iTerm2 general preferences menu](img/6188d25ca52b31ddd262bc1cbb56d223.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--11GS9dfW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ecm2tjv3notlkfbvpedd.png)

选择“配置文件”菜单。

[![iTerm2 profiles menu](img/29accb82d59e7ac64c78c1f14d315f14.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ap36MkWv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kd0la834fq3ea3gdqky3.png)

选择“密钥”选项卡。

[![iTerm2 keys tab](img/3281449a167a92dfcf3fc0d1befa794a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BFtpwK6F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g17oyzlcjpys4cs49s34.png)

在“Key Mappings”窗格中，找到 Alt 和 left 键的映射，如下所示:`⌥←`。双击它。

[![iTerm2 keys tab with Alt Mappings](img/7756736c32a52ddb82ccfe00aa01aede.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Fqi1cgkM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l39qmmtdew74qmuq7xzb.png)

将动作从“发送十六进制代码”更改为“发送转义序列”(您可能需要滚动一点才能找到)。

在“Esc +”字段中，键入小写字母“b ”,然后单击“确定”。

[![iTerm2 Alt left mapping](img/b8276a5680891979d716736d839f360e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Uc_QID5R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cmds3b1wxv8koorlm1g5.png)

为 Alt+right`⌥→`打开相同的上下文菜单，再次将动作更改为“发送转义序列”。

这一次，在“Esc +”字段中，键入小写的“f”。单击“确定”。

[![iTerm2 Alt right mapping](img/08e94b65f116069e14e2c4b9f70cc56e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hgf-PT7e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yke5jeb6rfwwdv4lhev5.png)

关闭首选项菜单。这些更改会立即生效。当你按下`⌥` + `→`或`←`时，光标会像在其他应用程序上一样跳过整个单词。现在，您可以更精确、更快速地浏览命令行。

[![Working alt key moving between words](img/60d18e805661faa48ceb8555327f793b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ho40fcJA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jvp67tmm7likx0bwjq9n.gif)

## 奖励快捷键

`Control` + `e`将光标移动到行尾。

`Control` + `a`将光标移动到行首。