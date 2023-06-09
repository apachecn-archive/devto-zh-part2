# 用谷歌在 Windows 10 上开始菜单搜索！

> 原文：<https://dev.to/spikespaz/start-menu-search-on-windows-10-with-google-46ie>

# T2】

注意:这是一个相当老的帖子。对于更新的信息，你应该可以简单地谷歌软件的名字，*搜索导向器*。其他几位作者比我更好地报道了这个项目，去看看他们的文章吧！

我刚刚发布了我的项目的新版本， **[搜索偏转器](https://github.com/spikespaz/search-deflector)** ，我认为这是功能性的，并准备供公众使用。你应该继续阅读一些关于它的信息，或者如果你能从标题中猜出它是做什么的，去[下载](https://github.com/spikespaz/search-deflector/releases)它！

如果这对您有帮助，请不要忘记启动存储库！

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)/[搜索-偏转器](https://github.com/spikespaz/search-deflector)

### 一个小程序，将搜索从 Cortana 转发到您首选的浏览器和搜索引擎。

<article class="markdown-body entry-content container-lg" itemprop="text">

# [![Search Deflector](img/aa5b5e459837c7ff139a760b278cb0c5.png)T2】](https://raw.githubusercontent.com/spikespaz/search-deflector/release/assets/title.svg)

[![Latest Version](img/59a55815153acc9b9febe778ea674473.png)](https://github.com/spikespaz/search-deflector/releases/latest)[![Project License](img/cda54c1b7106d0f1ff6f75181f1d39a2.png)](https://github.com/spikespaz/search-deflector/blob/master/LICENSE)[![Latest Release Downloads](img/63c6130536fbd6e9548541618cb96771.png)](http://tiny.cc/get-search-deflector)[![Project Stars](img/d792b89c8ae8ee3bb1854d1a3afce68b.png)T11】](https://github.com/spikespaz/search-deflector/stargazers)

### **Search Deflector** 是一个小型系统实用程序，可以将从开始菜单或 Cortana 进行的搜索重定向到您喜欢的任何浏览器和搜索引擎，消除与微软 Edge 和 Bing 的联系。

这个软件可以让你执行更快的网络搜索，只需点击 Windows 键，然后输入你的搜索。按 enter 键，或单击右侧面板中的一个结果。这比打开浏览器(如果还没有的话)，打开一个新标签，然后点击搜索栏要快。

简单设置后，你可以使用*任何*浏览器(只要它注册为协议处理器)和*任何*搜索引擎。如果你在使用这两个选项时遇到问题，请发邮件到 [support@birkett.dev](https://raw.githubusercontent.com/spikespaz/search-deflector/release/mailto:support@birkett.dev) 或创建一个[问题](https://github.com/spikespaz/search-deflector/issues)，我会看看我能做些什么来让你的自定义设置正常工作。

[**如果你有**](https://github.com/spikespaz/search-deflector/wiki) 的话，别忘了查看维基……

</article>

[View on GitHub](https://github.com/spikespaz/search-deflector)

# 关于

为了节省时间，我将只引用我在 GitHub 库中的描述。

> 搜索偏转器
> 
> 这是一个小程序，它会将使用 Cortana 和开始菜单进行的搜索重定向到您首选的浏览器和搜索引擎。虽然这个软件非常类似于 Edge Deflector 和 Search With My Browser，但它允许用户使用他们想要的任何搜索引擎，并选择任何安装的浏览器——而不仅仅是系统默认的。

基本上，当你按下键盘上的 Windows/Super/Logo 键开始输入时，默认行为是在微软 Edge 中用 Bing 打开你的搜索词。真的有人用 Edge 吗？

有了这个，在第一次设置后，你从 Windows Search UI 告诉 Cortana 打开的任何网站(或者如果 Cortana 被禁用，则键入，因为它应该被禁用)都将被重定向到你想要的任何浏览器和任何搜索引擎。耶！

我喜欢这个，因为它比打开浏览器或创建一个新标签，点击搜索栏，然后输入要快得多。只要按 Windows 键，输入“猫图片”，按回车键，你就会看到一个漂亮的充满猫图片的页面。

### 为什么做这个？

我使用 [EdgeDeflector](https://github.com/da2x/EdgeDeflector) 已经有一段时间了，但是它有一些问题。这是错误的，有时它不能正确地转换 URIs。它也不允许你改变搜索引擎，它只使用你的系统默认浏览器。

SearchWithMyBrowser 也有同样的问题。

我希望能够使用不同的浏览器进行搜索，而不是系统默认的浏览器。我也希望能够改变搜索引擎，当然不是除了谷歌之外的任何东西。我也非常喜欢重新发明轮子。

### 那么我该如何使用呢？

**进入 GitHub [发布](https://github.com/spikespaz/search-deflector)页面，下载安装程序并运行。**

当您到达命令提示符打开的部分时(我懒得为它创建 GUI)，按照屏幕上的提示操作。它应该列出所有可以在你的注册表中找到的浏览器，并允许你通过输入旁边的数字来选择一个。

我一直无法正确显示所有浏览器的路径，所以如果你的路径看起来不正确，请创建一个新的[问题](https://github.com/spikespaz/search-deflector/issues)。

我没有给可执行文件签名，所以你会得到各种 Windows 的“不可信”警告。我保证，这不是病毒。你可以自己看代码。

如果您想查看详细的安装指南，请参阅存储库主分支中的 [README.md](https://github.com/spikespaz/search-deflector/blob/master/README.md) 文件。

### 发现一个 bug /不工作！

请提交一个尽可能详细的 GitHub [问题](https://github.com/spikespaz/search-deflector/issues)。如果程序崩溃了，你应该会看到一个消息框，里面有一个<button>帮助</button>按钮，可以将你重定向到问题页面，其中填写了大部分重要信息。只需添加一个标题，也许包括你搜索的文本。

如果你不认为一个新的问题会有很大帮助，或者如果你需要帮助设置它，你可以发电子邮件到 support@spikespaz.com 给我。我不咬人。

# 链接

| 名字 | 环 |
| --- | --- |
| 主页 | [https://spikespaz.com/search-deflector](https://spikespaz.com/search-deflector) |
| MS 商店 | [https://www.microsoft.com/store/productId/9P8ZJJ80RZ2K](https://www.microsoft.com/store/productId/9P8ZJJ80RZ2K) |
| 捐赠 | [https://spikespaz.com/donate](https://spikespaz.com/donate) |
| 维基网 | [https://github.com/spikespaz/search-deflector/wiki](https://github.com/spikespaz/search-deflector/wiki) |
| 问题 | [https://github.com/spikespaz/search-deflector/issues](https://github.com/spikespaz/search-deflector/issues) |
| 许可证 | [https://github . com/spikespaz/search-deflector/blob/master/LICENSE](https://github.com/spikespaz/search-deflector/blob/master/LICENSE) |
| 电子邮件 | [support@spikespaz.com](mailto:support@spikespaz.com) |

### 捐款

我是这个项目的唯一作者，如果你欣赏我的工作或我的任何其他项目，你可以捐赠少量来帮助我。

这个软件现在是而且将永远是免费的，不会减少功能。我在业余时间制作了这个软件，作为一种爱好，我将非常感谢你所做的任何金额的捐款。**如果你有 GitHub 账号，请考虑主演这个资源库！**

如果你有多余的零钱并且想要回馈，你可以在微软商店购买软件或者通过 PayPal 捐赠。

# [![Get it from Microsoft!](img/f04fbc5e75c037a5a2e0886711b4cb26.png)T2】](https://www.microsoft.com/store/productId/9P8ZJJ80RZ2K)

# [![Donate!](img/f8c80cd06047522f52b383d5398af014.png)T2】](https://spikespaz.com/donate)