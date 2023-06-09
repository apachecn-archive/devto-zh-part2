# 请停止破解“chcp 65001”

> 原文：<https://dev.to/mattn/please-stop-hack-chcp-65001-27db>

在 GitHub 上，有[多个代码](https://github.com/search?q=%22chcp+65001%22&type=Code)使用`chcp 65001`。这是黑客在 Windows 命令提示符下显示 UTF-8。很可能，这种方法在非多字节语言环境下也能很好地工作。但是这在多字节语言环境中不能正常工作。比如 jq 就用了这个 hack。[https://github.com/stedolan/jq/pull/824](https://github.com/stedolan/jq/pull/824)

这种攻击会破坏多字节用户 Windows 命令提示符。

[![](img/51cbecdb4d55f05521f0d8efda700142.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BAoWgQfw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y306umvrwreva53p7zlh.gif)

你可以看到字体被改变了，但没有恢复。

在东亚语言环境中，反斜杠的数字在非东亚语言环境中不相同。在非东亚语言环境中，反斜杠显示如下。

[![](img/d929289f03074e138ec733a4ac973c0d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hB_LdU0w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bl7o47hmz99mpz1gyb2z.png)

但是在东亚语言环境中，反斜杠显示如下。

[![](img/2f568f5ab069bb475036f3ad7c4533ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m5SZXO5X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vr8v2hve5yhmi8q5z01b.png)

仅供参考，大多数 Windows 用户通常使用非 UTF-8 代码页。例如，日本人使用代码页 932。

[![](img/1fe5ab05e483f362e73a3fd0c6e44eaf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fE83ec3Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7ov2oigq46bkorwn9573.png)

我写了一个补丁来解决这个问题。[https://github.com/stedolan/jq/issues/1121](https://github.com/stedolan/jq/issues/1121)

此外，curl 使用了这种方法。[https://github.com/curl/curl/issues/3008](https://github.com/curl/curl/issues/3008)

这也让 cmd.exe 崩溃。

[![](img/2d332c75286d871eb3e8b12801d484f2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--usl1VPcS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tw37aeqtojiqlud4ym4d.gif)

我又写了一个类似上面的补丁。[https://github.com/curl/curl/pull/3212](https://github.com/curl/curl/pull/3212)

正如我在这篇 PR 的描述中所写的，当在 Windows 控制台上更改代码页时，尽可能地修改字体。并且恢复代码页也设置合适的字体。因此无法正确恢复原始字体。要在不改变控制台代码页的情况下编写 UTF-8，应该使用宽字符串 API。

请停止使用 hack `chcp 65001`。

# P.S

如果你想运行用 UTF-8 写的批处理文件，你应该用下面的命令运行以避免破坏命令提示符:

```
start /wait /min foo.bat 
```