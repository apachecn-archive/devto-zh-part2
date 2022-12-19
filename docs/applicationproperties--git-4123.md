# application.properties & Git

> 原文：<https://dev.to/tomtucka/applicationproperties--git-4123>

大家好，

今天在工作中遇到了一个问题，我想要一些意见/帮助来处理它。

所以我有一个 application.properties 文件，它基本上包含

`spring.profiles.active=***`

星号表示您基本上定义了您想要使用的配置文件，即开发、uat、生产前、生产。

因此，当开发人员在本地机器上工作时，他们使用`dev`配置文件，但有时会忘记在提交时将它改回来，这意味着事情指向错误的位置或使用错误的登录等等...我原本以为我可以将 application.properties 添加到我的`.gitignore`文件中，远程版本将在 github 上保持正确，然后 devs 将在本地创建一个 application.properties 并指定使用哪个概要文件。结果这不起作用，git 不会忽略这个文件而不删除远程文件。

有人有什么建议吗？任何帮助都将不胜感激