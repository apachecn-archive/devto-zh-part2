# 用 AJAX 基础知识碰壁

> 原文：<https://dev.to/danalovesvidya/hit-a-wall-with-ajax-basics-j62>

我想学习 Javascript 游戏的 netcode，我的谷歌搜索让我接触到了 AJAX。我尝试了一个从. txt 文件中提取文本并在 HTML 段落中显示的例子，但是它对我来说根本不起作用。

沮丧地退出后(我想我可能有多动症)，我回来谷歌了一些，结果发现我的问题是 Chrome 不允许本地 AJAX 调用。建议安装 XAMPP 并重试。

我没有太多使用 Apache 或 XAMPP 的经验，但是我以前做过网页，所以我对基本的文件夹系统比较熟悉。我创建了一个 http/文件夹，并放置了我的 index.html、main.js 代码和 simple。txt 文件在里面。当我试图从服务器运行代码时，它给出了一个不同的错误。

另一次挫折打击了我，但我回来了。我回到谷歌，我找到了 CORS 和彗星的参考，我不是 100%理解，但我确实收集了有问题的文件。txt 文件)需要一个标题。但问题是。txt 文件实际上没有标题，因为它们只是文本，不能作为代码文件读取。此外，我能找到的几乎所有东西都提到了向 PHP 文件添加头，这对我来说并不相关，因为我使用的是 Javascript。我尝试将标题添加到我的。js 文件以备不时之需，但这没有用。

这里有人能给我一些建议，让这个简单的 AJAX 代码在我的计算机上运行，这样我就可以继续我的学习目标了吗？感谢您的宝贵时间！