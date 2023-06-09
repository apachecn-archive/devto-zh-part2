# 从 GitHub 迁移到 Bitbucket

> 原文：<https://dev.to/hector6872/moving-from-github-to-bitbucket-dhc>

## W̶h̶y̶ ̶a̶n̶d̶怎么了

> **免责声明:**这篇帖子与[微软最近收购 GitHub](https://news.microsoft.com/2018/06/04/microsoft-to-acquire-github-for-7-5-billion/) 没有任何关系(我对此并没有很强烈的看法；虽然我希望那根本不会影响到我们)。

[![](img/de87c94f9a9fb5b4f74c4b8d0dddf052.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3nM6iKC0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/hrules6872/Writings/master/4-from-github-to-bitbucket/art/1.jpeg)

下个月，我的 GitHub 年度订阅将到期，我不想为了减少我目前支付的大量在线服务而续订。
**TL；dr:我会继续使用 GitHub，但不会用于私有库。**

有许多替代方案，但我决定将我的私有存储库迁移到 [Bitbucket](https://bitbucket.org/hrules6872/) (主要是因为我习惯在[工作](https://lolamarket.com/)时使用它)。最大的障碍是他们没有提供任何工具来一次导入所有的回购(我们必须一个接一个地做家务…)所以**我创建了一个** [脚本](https://gist.github.com/hrules6872/af8655427e6ec7741a82516713d99ebf) 来填补这个空白。**不客气，bit bucket**T0】

#### 移动检查表

*   [Python 2.7](http://lmgtfy.com/?q=install+python+2.7)
*   [fromgithubtobucket . py](https://gist.github.com/hrules6872/af8655427e6ec7741a82516713d99ebf)脚本(不要忘记使用`chmod +x`使其可执行)
*   [GitHub 个人接入令牌](https://github.com/settings/tokens)(需要范围:*回购*
*   [Bitbucket app 密码](https://bitbucket.org/account) ➡️ Bitbucket 设置➡️ App 密码(需要权限:*仓库写*)
*   [Bitbucket OAuth 消费者](https://bitbucket.org/account) ➡️ Bitbucket 设置➡️ OAuth ➡️ OAuth 消费者(需要权限:*存储库写& admin*

> 如您所见，我们不会暴露任何帐户密码，因此我们可以在事后删除这些临时凭证。

#### 动天

*   [填写凭证](https://gist.github.com/hrules6872/af8655427e6ec7741a82516713d99ebf#file-fromgithubtobitbucket-py-L4)
*   [设置我们要导入哪种回购](https://gist.github.com/hrules6872/af8655427e6ec7741a82516713d99ebf#file-fromgithubtobitbucket-py-L15)
*   * *运行脚本* *并遵循说明
*   [跳舞](https://giphy.com/explore/victory-dance)！

本文最初发表于[媒体](https://medium.com/@hector6872/moving-from-github-to-bitbucket-30c12dd8aea5)

* * *

[1]尽管[有些人](https://twitter.com/Syknapse)认为它是最丑的一个`:wink:`
也是，我很久以前试过 [Gitlab](https://gitlab.com/) 而且我觉得他们的网站运行缓慢。也许他们现在已经解决了这个问题，IDK`:shrug:`
【3】它已经在 Linux/MacOS 下测试过了。Windows 用户呢？那你能告诉我为什么你还在用 Windows 吗？`:trollface:`

* * *

[外部链接👀](https://gist.github.com/hrules6872/fb65d6ca42c719221dfa8989e6cfef98)