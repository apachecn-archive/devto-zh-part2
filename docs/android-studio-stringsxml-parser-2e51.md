# Android Studio strings.xml 解析器

> 原文：<https://dev.to/developerdmp/android-studio-stringsxml-parser-2e51>

大家好！今天，我有一个关于我最近创建和使用的一个相对基本但有用的工具的小广告。该程序的想法是方便生成 strings.xml 文件，这样您的应用程序就可以支持多种语言。

目前，如果你希望你的应用程序被专业翻译成任何语言，谷歌提供每个单词 0.07 美元的费用。这可能看起来不是很多，但是如果你的资源文件中有很多字符串，并且想要支持多种语言，那么翻译你的应用程序的价格可以很容易地增长到数百。如果你愿意付钱，那就想尽一切办法。否则，如果你不同意为 Google Translate 可以做的事情付费(并希望通过这一过程加快你的效率)，请查看我最新的 GitHub 贡献:[https://GitHub . com/developer-DMP/Android-Studio-Strings-Parser](https://github.com/developer-dmp/Android-Studio-Strings-Parser)。

GitHub 链接中有一些关于用法的信息，但这里有一个关于它如何工作的简要概述:

将项目下载到您的本地机器
在 input/strings.xml 处输入您的 strings.xml 文件
运行程序来解析文件
从 output/strings_content.txt 中获取内容并粘贴到 Google Translate 中
*将文本翻译成您想要的任何语言
获取翻译后的文本并粘贴到 Input/strings _ content _ converted . txt 中
再次运行程序来重建 strings.xml

嘣。现在，您在 output/中有了一个新格式化的 strings.xml 文件，您可以将它复制并粘贴到 Android Studio 中的 strings.xml 文件中，以支持您希望支持的任何语言环境。

这个过程不是完全自动化的，但那是因为谷歌翻译 API 不是免费的

我写的程序(如果你想知道的话，用 Intellij 写的)极大地提高了为你想要支持的不同地区/语言创建大量 strings.xml 文件的效率。我刚刚在即将发布的应用程序中使用了这个程序，并能够在 20 分钟内浏览 35 种语言。我的 strings.xml 文件也超过了 100 行，所以我能够执行数千次翻译和无数次 strings.xml 文件的重建，而不用绞尽脑汁。

请让我知道这个程序是否对阅读这篇文章的人有用，你能想到的任何潜在的改进，以及是否有比我好 100 倍的服务，我只是浪费时间重新发明轮子！无红利