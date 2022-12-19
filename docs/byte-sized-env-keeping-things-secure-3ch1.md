# 字节大小:。环境保持事物安全

> 原文：<https://dev.to/jaybeekeeper/byte-sized-env-keeping-things-secure-3ch1>

我最近花了一点时间研究如何用 JavaScript 构建一个 twitter 机器人；这实际上并不是超级困难- [但是绝对值得去探索关于](https://botwiki.org/learn/#essays)的伦理、意图、潜力和分歧-但是我遇到了一些我不熟悉的东西-。环境文件。

我把我的机器人放在 glitch.me 上——我重新混合的项目已经包含了一个. env 文件。但是我并不完全熟悉这个想法。

[![My Glitch Directory](../Images/ce9be1744b4873e85e8c08b7486ac11c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1ivwCz2r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sgrcpf2nrxy96m74oxnw.png)

Glitch 作为一个社区和一个项目是非常棒的，我可以叉一个存储库并得到一个空白。env 文件——为我提供模板，而不泄露前存储库所有者的内容和秘密。

[![](../Images/cd3257c070f7c0f0c47c6bc9427901f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9Je7SPdP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k0tljuxygfcuhcnmku5w.png)

但是...什么是. env 文件？为什么我的推特机器人会有故障？

所以。env 文件是如何保证 APIkeys 安全的问题的解决方案。我需要和 TwitterAPI 对话来制作我的 Twitter 机器人。但是对于许多 the API 来说，我需要一个安全的密钥来和 API 对话。那个密钥需要*保持*的安全——我不能把它发布到 Github 上的公共知识库让全世界看到。任何人都可以抓住它并使用它。

快速解决方案是 dotenv npm 包——Glitch 允许我在他们的网站上安装这个包，但是假设我在 JS 本地工作。快速安装:

```
npm install dotenv --save 
```

Enter fullscreen mode Exit fullscreen mode

现在我已经有了这个包，我要求它在我的程序中尽早出现:

```
require('dotenv').config() 
```

Enter fullscreen mode Exit fullscreen mode

现在我可以有我的 APIKey，设置在一个。环境文件-

```
echo "API_KEY=secure_API-key_here" >> .env 
```

Enter fullscreen mode Exit fullscreen mode

如果打开该文件，我可以看到我的安全 API 密钥，它不必位于我的存储库中的其他文件中。我在做生意！dotenv 将寻找一个. env 文件，当我运行我的。对于我的应用程序，dotenv 将读取。env 文件，并将其内容作为环境变量提供给我的进程。我可以用

`process.env.API_KEY`

访问那个变量，如果我添加我的。环境文件到我的。gitignore 文件，我就不用把自己的秘密提交给全世界看了。保证我的密钥安全，并允许我的代码运行。

同样，我第一次在 Glitch.me 的上下文中遇到这个问题。env 文件安全——即使其他用户查看了您的代码，他们也无法访问您的安全密钥。但是你的程序仍然有！