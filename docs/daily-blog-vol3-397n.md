# 每日博客第 3 卷

> 原文：<https://dev.to/hirodeath/daily-blog-vol3-397n>

## 我最近的兴趣

如今，我给 [NISA](http://www.jsda.or.jp/en/activities/research-studies/html/NISA.html) 算了一笔账。你可能已经知道[是](https://en.wikipedia.org/wiki/Individual_Savings_Account)了。自 1999 年以来，英国政府开始为投资者提供便利。而日本，在日语中的意思是`NIHON`，所以首字母是`N`。N + ISA = NISA。这就是为什么我们称它为`NISA`。最近，我对不像赌博的投资感兴趣。作为一个年轻(但不年轻，也不老)的日本人，投资听起来很可疑。我通过文章或者网络媒体或者 youtube 听说过关于 [ICO](https://en.wikipedia.org/wiki/Initial_coin_offering) 的诈骗案例。去年，加密货币非常受欢迎，并在日本传播开来。我猜很多人都对它感兴趣。与此同时，ICO 欺诈案件不断发生。因此，我对加密货币和区块链技术非常感兴趣，我从未拥有过它。
所以，我一直在阅读关于它的文章和区块链图书馆的文件，如 [web3.js](https://github.com/ethereum/web3.js/) 。然后，突然，我开始思考我的人生。我喜欢开发，制作应用程序，不断追赶新技术，诸如此类的事情。但是我很容易厌倦。就我个人而言，我被音乐学校提升，然后以副学士学位毕业。所以，我喜欢作曲、演奏吉他、贝司、鼓和键盘等乐器。我总共有 8 年的音乐生涯，其中包括我的爱好，尤其是当我还是高中生的时候。这让我很自豪，并且永远不会忘记，但是我突然放弃了一切，因为我进入了网络技术。总之，当我遇到我沉迷的东西时，我可能会突然有所发展。大概了解一下加密货币和投资让我成长到我未来的那些。所以，我一天天地慢慢积累这些东西。

## Javascript 的其他东西

我最近将 create-react-app 从 1.x.x 升级到了 2.0.4，有时候(不经常，只是有时候)，我会读取它的 webpack 配置和 CRA 内部的代码。这对学习如何使用 webpack 和配置 webpack 很有帮助。代码阅读对开发人员来说非常重要，特别是如果你必须管理 SPA 环境应用程序或复杂的应用程序，CLI 的内部应该会对你有所帮助。
我个人在使用 webpack 的时候，写 webpack 构建或者启动服务器到`package.json`，但是 create-react-app 的启动脚本是`node scripts/start.js`。
这意味着 create-create-app 通过调用节点 api 从`scripts/start.js`文件中调用启动脚本。事情是这样的，`scripts.start.js`里面写的是关于调用 webpack-dev-server，以及一些顺序逻辑。我还没有深入阅读，所以就这样吧。

所以，今天的我的博客完成了。明天见。