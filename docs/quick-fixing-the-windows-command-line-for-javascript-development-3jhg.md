# 快速修复 JavaScript 开发的 Windows 命令行

> 原文：<https://dev.to/terabaud/quick-fixing-the-windows-command-line-for-javascript-development-3jhg>

使用 Windows 进行 web 开发的一个主要问题是缺少一个类似 unix 的 shell 环境。尽管大部分 JavaScript [NPM](https://npmjs.com) 工具也适用于 PowerShell 或 CMD，但我在使用除了`C:\`之外的驱动器上的一些包时遇到了一些奇怪的`Command not found`问题。

一个简单的解决方法是使用 git 附带的 bash shell。它比安装一个完整的 Linux/msys2/cygwin 发行版更轻量级，并且它足以满足大多数 JavaScript 开发目的。

我喜欢将 git bash 与 [hyper.is](https://hyper.is) 结合使用，以获得良好的终端体验。只需将 shell 设置配置为
`'C:\\Program Files\\Git\\bin\\bash.exe'`。我用了一个较暗版本的 rebeccapurple 作为背景色(#221133)💁。

[![Hyper terminal featuring git bash on Windows](img/6befe067f7b57599914b3bc8769545a1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aOYesmui--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s2djuchb7joso51xoqkc.png)

如果您使用的是 vscode，您可以通过 settings.json 相应地调整您的集成 shell:

```
{
    "terminal.integrated.shell.windows": "C:\\Program Files\\Git\\bin\\bash.exe",
    ...
} 
```