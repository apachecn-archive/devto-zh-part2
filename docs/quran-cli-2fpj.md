# 古兰经 Cli

> 原文：<https://dev.to/sarfraznawaz2005/quran-cli-2fpj>

用英语翻译从命令行朗读/背诵古兰经。

## 2005 年 / [古兰经-古兰经](https://github.com/sarfraznawaz2005/quran-cli)

### 📕用英语翻译从命令行朗读/背诵古兰经。

<article class="markdown-body entry-content container-lg" itemprop="text">

# <g-emoji class="g-emoji" alias="closed_book" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/1f4d5.png">📕</g-emoji>古兰经-cli

用英语翻译从命令行朗读/背诵古兰经。

# 要求

*   [节点 j](http://nodejs.org)

# 装置

*   克隆此存储库。`git clone https://github.com/sarfraznawaz2005/quran-cli.git`
*   类型`npm install`
*   类型`npm link`

# 使用

**列出所有章节**

`quran chapters`或用别名`quran c`

[![Screenshot](../Images/ae1eedba5c4b99c6fb3d4c4382dd622f.png)T2】](https://raw.githubusercontent.com/sarfraznawaz2005/quran-cli/master/chapters.png)

**阅读具体章节**

命令格式为:`read|r <chapter> [verse] [arabic]`，其中`verse`和`arabic`参数可选。

阅读第一章:

`quran read 1`或用别名`quran r 1`

[![Screenshot](../Images/4d8423aa8cc0a06ff5fdad890bc814c2.png)T2】](https://raw.githubusercontent.com/sarfraznawaz2005/quran-cli/master/screen1.png)

***仅限英语*** :以阅读第一章为例，键入:`quran read 1 0 0`。这里第一个`0`表示没有/所有诗句，第二个`0`表示没有阿拉伯文本。

[![Screenshot](../Images/0ef0a26432a518d42f0b5cca97e983f7.png)T2】](https://raw.githubusercontent.com/sarfraznawaz2005/quran-cli/master/screen6.png)

**阅读章节中的特定诗句**

阅读第二章的第二节:

`quran read 2 2`或用别名`quran r 2 2`

[![Chapters](../Images/850f8a3afc8b10a0979af7a8f01f9e94.png)T2】](https://raw.githubusercontent.com/sarfraznawaz2005/quran-cli/master/screen2.png)

***仅英文*** :以阅读第 3 章第 25 节为例，键入:`quran read 3 25 0`。这里的`0`表示没有阿拉伯文本。

[![Screenshot](../Images/eef0b9889a0b596e2744280b51fc9313.png)T2】](https://raw.githubusercontent.com/sarfraznawaz2005/quran-cli/master/screen5.png)

**读取**的指定范围…

</article>

[View on GitHub](https://github.com/sarfraznawaz2005/quran-cli)