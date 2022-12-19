# EJSONPARSE 或意外令牌“npm 安装”错误的 7 种解决方案

> 原文：<https://dev.to/redslug/7-solutions-to-ejsonparse-or-unexpected-token-npm-install-error-20nh>

您克隆了一个 repo，或者您可能正在用一个新的`package.json`文件创建您的项目。当你尝试`npm install`，`npm install package_name — save-dev, or` npm i npm `，JSON 不解析。

`npm ERR! code EJSONPARSE`
`npm ERR! JSON.parse Failed to parse json`
T2】

出现此错误的原因有很多。你认为你的`package.json`文件可能有问题，但你不确定是什么问题。也许你可以试试`yarn install`,因为 Yarn 是一个类似于 npm 的包管理器，众所周知它更快并且有更多的增强。但是如果问题出在您的 JSON 上，这可能没有帮助。以下是诊断该问题的一些可能的解决方案或方法:

1.)`npm cache clean -force`:清理缓存将解决与以前安装的软件包的潜在冲突。

2.)调试日志:这可能会为您指出错误发生的确切位置，以便您可以修复它。您可以在控制台输出中找到调试日志的位置。举个例子，

3.)JSON validator:使用类似 JSONLint 的工具来检查并确保您的 package.json 文件是有效的。JSON 验证器或 linter 应该能让您更深入地了解为什么文件没有被解析。

4.)检查逗号:在某些情况下，在键值对之间添加逗号可能会有所帮助。在我的例子中，删除尾部的逗号可以解决这个错误。

5.)缩小:缩小器可以删除多余的空白或不正确的格式并解决问题。

6.)包数:确保你的 package.json 中只有一个包，如果要使用多个包，最好把你的项目拆分到多个目录中。每个组件都需要自己的 package.jsonto，以避免版本问题或使 require-statement 解析起作用。

7.)`rm package-lock.json`:移除锁文件并再次运行`npm install`可能有助于解决版本问题。

希望这些解决方案中的一个能帮助你。请在评论中添加您的想法或任何其他解决方案！

感谢安吉·琼斯教我 listicle 博客风格，感谢李佳薇评论这篇博客文章并提供令人敬畏的反馈，感谢编写 Speak Code 激励我写这篇文章，感谢 Recurse Center 成为一个精彩的编程社区！