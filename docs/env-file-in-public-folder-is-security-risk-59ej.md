# 。公共文件夹中的 env 文件存在安全风险

> 原文：<https://dev.to/ewnx01/env-file-in-public-folder-is-security-risk-59ej>

不幸的是，我必须在工作中使用 Typo3，我知道愚蠢的想法，他们将整个应用程序存储在 web 服务器的文档根目录中，所以默认情况下一切都是公开的。

有一个[扩展名](https://github.com/helhum/dotenv-connector)使用一个. env 文件进行配置，但是这个扩展名也希望这个文件在公共文件夹的根目录下。**那可是非常高的安全风险**。
如果你忘了在。htaccess 或服务器配置每个人都可以看到你的凭证和你存储在那里的任何东西。

不久前，我的公司就发生了这样的事情。纯属偶然，我发现了这个安全漏洞，并迅速修复了它。我只能希望它没有在线很长一段时间，因为在。env 文件，其中还提示在哪里可以找到其他脚本、密码/用户提示和其他凭证。但是这说明了为什么你不应该把你的。env 文件放在公共可访问文件夹中，并且实现默认情况下希望该文件放在公共文件夹中的扩展(或库)是非常危险的。

当然，你可以说没有禁止访问这个文件是管理上的错误。但是这很容易发生，您的应用程序不应该依赖于此，尤其是对于这样一个有风险的文件。如果你这样做，那是一个非常糟糕的设计缺陷，你应该尽快重构它。

我知道 Typo3 开发人员没有遵循现代开发或设计规则，并且使用了许多旧的编码风格，但是我将尝试解决这个问题。