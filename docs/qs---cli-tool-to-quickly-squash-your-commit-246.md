# QS - CLI 工具，快速粉碎你的承诺

> 原文：<https://dev.to/chaspy/qs---cli-tool-to-quickly-squash-your-commit-246>

# 问题

在 GitHub 上开发时，您可能希望将多个提交编译成一个提交。

届时，您可能会执行以下操作:

1.  git rebase -i
2.  从`pick`更新到`squash`
3.  更新提交消息
4.  退出编辑器

要走很多这样的交互程序，很麻烦。

# 解

我和 [@kamontia](https://dev.to/kamontia) 发布了 QS 作为 CLI 工具。

QS 只需要一个命令就可以做到这些。

[![](../Images/05815de48a1c757b908ebfdd4f32a85a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_dsxyIib--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ym6ruacgroslyp2do8tx.jpeg)

来自:[https://speaker deck . com/chaspy/look-back-on-our-OSS-activities](https://speakerdeck.com/chaspy/look-back-on-our-oss-activities)

[![](../Images/ca6f0bc59ff8d9fc87fda82f222beeda.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J1scPYOa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/kamontia/qs/raw/assets/assets/logo_transparent.png)

*   存储库: [kamontia/qs](https://github.com/kamontia/qs)
*   语言:戈兰语

# 如何安装

选择`go get`或`brew`，你喜欢的方式。您也可以从[发布页面](https://github.com/kamontia/qs/releases)下载预构建的二进制文件，并将其放在 PATH 经过的目录中。

要了解更多信息，请参阅存储库中的[自述文件](https://github.com/kamontia/qs)。

# 总结

QS 是一个非常强大而简单的工具，可以快速粉碎你的承诺

请使用它，如果您给我们改进请求或印象，我会很高兴。

谢谢大家！