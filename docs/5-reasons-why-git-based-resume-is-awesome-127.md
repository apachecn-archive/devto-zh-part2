# 基于 Git 的简历很棒的 5 个理由

> 原文：<https://dev.to/acro5piano/5-reasons-why-git-based-resume-is-awesome-127>

我在 GitHub 上创建并更新了我的简历。这不是一个正常的方法，但对我来说很有效。

我使用 gitHub 来保存和共享我的 Git 存储库，所以在本文中我假设 GitHub 是默认的 Git 存储库服务。如果您熟悉其他版本管理系统或 git repo 服务，请阅读“Git / GitHub”中的任何其他单词，如“Git / Gitlab”、“Mercurial / Bitbucket”等。

基于推特上的讨论。

> ![The Practical Dev profile image](img/1a10e330d1a8423f8b4b861b114cf62c.png)实用 Dev[@ thepracticaldev](https://dev.to/thepracticaldev)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)1 .便携性
> 2。拉动请求
> 3。更易于设计
> 4。管理成本少
> 5。堆栈空闲
> {作者:[@ ydp 140](https://twitter.com/YDP140)}
> [dev.to/acro5piano/5-r…](https://t.co/Z6KqbSYbeG)2018 年 4 月 28 日上午 11:40![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=990193919882383361)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=990193919882383361)

[/edited]

# 我为什么把简历上传到 GitHub

我有一个 LinkedIn 的账户。LinkedIn 是一个独特的公开个人简历和在线交流的服务。我喜欢这个想法，所以我总是公开我的简历。

当我申请一家公司时，它要求制作一份 A4 的简历，通常是用微软 word 或其他软件制作的。我当时用的是 Linux 笔记本电脑(搭载 Arch Linux)，所以没有微软 office。我可以使用谷歌文档，但它不允许我使用各种快捷方式，并且在离线时很难编辑。因此，我决定用 HTML、CSS 和 Webpack 创建我的简历。

看起来用这一堆东西制作我的简历比用 Windows 电脑或者在我的 Linux 笔记本上安装 MS Office 更容易、更有趣、更便宜。

当我创建一些东西时，我总是运行`git init`并用 Git 控制版本并上传到 GitHub。这是为了备份，因为我有时候为了一些意外会完全卸载 Linux OS。所以我把基于 HTML 的简历上传到 GitHub。
我不在乎我的个人信息是否公开。

我发现用 Git 管理简历给我带来了很多好处，我就在这里介绍一下。

# 1。轻便

我可以在任何地方展示和编辑我的简历。从我的 MacBook，Linux 笔记本电脑，甚至某人的 PC。我的 GitHub 简历网址很短，可以直接在网页浏览器的网址窗口中输入。

[https://github.com/acro5piano/resume](https://github.com/acro5piano/resume)

事实上，我已经用别人的电脑输入了网址。我更喜欢通过 Facebook Messenger 或其他方式将 PDF 发送给某人。

# 2。接收拉取请求

我一开始没想到这个功能，但是刷简历真的很容易。

[![the PR](img/d5d291460b80b470d698b2fbbfeecf94.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--K34aKmsI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/la9ywsv438vchc1y1dlv.png)

我已经请我的朋友，伟大的波兰开发者，在申请一家公司之前检查我的简历，我把他发送到 URL。他看到了我的存储库，纠正了我的错误并创建了拉取请求。他重构了我的简历！我检查了差异并合并了 PR。非常棒的体验。

# 3。更易于设计

我可以用我的 CSS 知识定制我的简历布局。这看起来比用 MS Word 定制更容易。我甚至不能像垂直布局一样实现 flex-box。我可以用草图来定制设计，但草图不是用来写作的，尽管它是创造设计原型的伟大工具。

# 4。减少管理成本

有了 Git，我可以确保这份简历绝对是新的。当我用 MS Word 创建时，我有多个版本，比如

*   简历-kaz-gosho.docx
*   简历-KAZ-gosho _ _ 更新. docx
*   简历-kaz-gosho__2018-04-28.docx
*   简历-kaz-gosho (1)。docx

我不相信哪个是最新的。

我们甚至可以用`git log -p`显示版本之间的差异。

# 5。堆栈空闲

我可以使用任何文本编辑器来编辑我的简历。这对开发人员来说是一个很好的观点，因为开发人员往往喜欢特定的文本编辑器。例如，我不擅长 MS Word 的界面，因为它不保证布局(并且缺少“模式”概念...不像 Vim)。
我可以用 MS Word 导出`.docx`到`.pdf`，但是至少需要点击三次，开发者不太舒服。如果我们使用 HTML 和 webpack-dev-server，我们可以用一行代码将 HTML 转换成 PDF，比如:

```
chromium --headless --disable-gpu --print-to-pdf --window-size=1200,1900 http://localhost:3000/ 
```

Enter fullscreen mode Exit fullscreen mode

或者使用`wkhtmltopdf`等转换工具。

当然，我们可以将该命令添加到 NPM 脚本中:

```
"build": "chromium --headless --disable-gpu --print-to-pdf --window-size=1200,1900 http://localhost:3000/" 
```

Enter fullscreen mode Exit fullscreen mode

然后运行

```
yarn build 
```

Enter fullscreen mode Exit fullscreen mode

这真的很短。无头 Chrome 真的很牛逼。此外，如果我们正确设置了配置项，我们就不必运行该命令。

# 待办事宜

我想进一步与基于 HTML 的简历。目前我的主要目标是:

*   CI。希望将`git push`挂接到`master`分支，生成 PDF 文件并截图，然后保存到某个地方(可能是 S3)。
*   I18n。需要两种语言`en`和`ja`。
*   网页。矛盾的是，我没有我简历的网页。可能我用 React.js 做渲染。

# 结论

我会继续在 GitHub 上保存我的简历。如果有兴趣，请尝试一下！

（...风险自担，因为 CV 有一些敏感数据)