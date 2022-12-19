# 6 个月文化编程后的思考

> 原文：<https://dev.to/jf/thoughts-after-6-months-of-literate-programming-3o0p>

2015 年 11 月，我在[写了我的第一个](https://github.com/jpf/token-cleaner/commit/29f589b054c12f779d18cf8907826ef20231eab4)[识字程序](https://en.wikipedia.org/wiki/Literate_programming)。从那以后，我几乎每天都在
写识字程序。这是一次
的经历，给了我很长一段时间未曾有过的启迪。不仅写有文化的程序很有趣，我觉得自己获得了某种新的“超能力”。

以下是我对目前为止有文化的编程经验的想法。

## 我迄今为止写过的有文化的程序

| 项目名 | 代码行 | 文学文献中的词语 | 字数与行数的比率 |
| --- | --- | --- | --- |
| [令牌清除程序](https://github.com/jpf/token-cleaner#token-cleaner) | Forty-six | Four hundred and ninety-six | 10.782609 |
| [Okta 登录小工具](http://developer.okta.com/docs/guides/okta_sign-in_widget.html) | One hundred and twenty | Three thousand eight hundred and sixty-two | 32.183333 |
| 【scim 测试版教学版 | Three hundred and sixty | Seven thousand nine hundred and sixty-six | 22.127778 |
| [打电话给猫](https://github.com/jpf/dial-a-cat#1-855-meow-jam-sending-cat-pictures-over-the-phone-via-space-age-technology) | Three hundred and seventy | Three thousand six hundred and twenty | 9.7837838 |
| [教导 OIC 测试版](https://github.com/jpf/okta-oidc-beta#introduction) | Seven hundred and fifty-two | Seven thousand seven hundred and thirty-one | 10.280585 |

解释:

*   **代码行数**是识字程序生成的“代码”行数。“代码”用引号括起来，因为代码是一个 **Org Babel** “源”块中的任何东西，它可以是 Python、Shell、HTML 等等。这个数字来自于对一个 **Org Babel** 文件:`awk '/BEGIN_SRC/,/END_SRC/' $filename`运行这个命令
*   **每行代码在文档中的字数**是一个文件中的字数，注意 in 也包括源代码。这个数字来自于对一个 **Org Babel** 文件:`wc -w`运行这个命令
*   **字行比**是:文字文档中的*字*除以代码的*行*

## 我喜欢有文化的编程

### 文化编程的表现力

我现在感到被内联代码注释束缚住了。有文化的编程最明显的好处之一是能够将我的代码编织成一大段一大段的散文，而不是将一小段一小段的散文交织成代码。

现在我发现自己花时间在代码中添加详细的引用和解释。其中一个例子是包括一个
`curl`命令，到 RFC 的链接，以及一个关于我的
[Okta SCIM Beta](https://github.com/joelfranusic-okta/okta-scim-beta) 文档的“[资源分页](https://github.com/joelfranusic-okta/okta-scim-beta#resource-paging)”部分中“1 索引”与
“0 索引”差异的注释。另一个例子是能够逐行覆盖
函数，以及在我的
[Okta OIDC 测试版](https://github.com/jpf/okta-oidc-beta)文档的“[验证来自 Okta](https://github.com/jpf/okta-oidc-beta#validating-an-oidc-id_token-from-okta) 的 OIDC id_token”一节中包含单元测试。

### 从表格中生成数据的能力

看一下
[Okta SCIM Beta](https://github.com/joelfranusic-okta/okta-scim-beta)文档的“[依赖关系](https://github.com/joelfranusic-okta/okta-scim-beta#dependencies)部分列出的表格。然后看一下同一个资源库中的
[requirements.txt](https://github.com/joelfranusic-okta/okta-scim-beta/blob/master/requirements.txt) 文件。依赖关系
表和`requirements.txt`文件都是从**组织 Babel** 中的
[单表](https://github.com/joelfranusic-okta/okta-scim-beta/commit/80fb95bbb315caea9ef0734f98f296923ffa03b8#diff-aacb4ce123f9200898dff4a35e6a8461R1229)生成的！

这一切都是通过编写内嵌代码到
[生成 requirements.txt 文件](https://github.com/joelfranusic-okta/okta-scim-beta/commit/80fb95bbb315caea9ef0734f98f296923ffa03b8#diff-aacb4ce123f9200898dff4a35e6a8461L1099)和代码生成[表
为 GitHub 页面](https://github.com/joelfranusic-okta/okta-scim-beta/commit/80fb95bbb315caea9ef0734f98f296923ffa03b8#diff-aacb4ce123f9200898dff4a35e6a8461R1318)降价的能力实现的。

### 能够在多个文件中包含版权声明

在 [Okta SCIM Beta](https://github.com/joelfranusic-okta/okta-scim-beta) 文档中，我可以在[一个地方](https://github.com/joelfranusic-okta/okta-scim-beta/commit/80fb95bbb315caea9ef0734f98f296923ffa03b8#diff-aacb4ce123f9200898dff4a35e6a8461L1131)定义一个版权
通知，并将该版权通知写入到
[LICENSE.txt](https://github.com/joelfranusic-okta/okta-scim-beta/commit/80fb95bbb315caea9ef0734f98f296923ffa03b8#diff-aacb4ce123f9200898dff4a35e6a8461L1133) 文件中，并将相同的通知包含到
[scim-server.py](https://github.com/joelfranusic-okta/okta-scim-beta/commit/80fb95bbb315caea9ef0734f98f296923ffa03b8#diff-aacb4ce123f9200898dff4a35e6a8461L1155) 文件中。

### 将 shell 命令和输出都放在一个地方

当我在写 [Okta OIDC 测试版](https://github.com/jpf/okta-oidc-beta)文档时，我花了大量的
时间寻找生成
x509 证书所需的神秘`openssl`咒语，这是我进行单元测试所需要的。因为我正在
编写一个有文化的文档，所以我能够包含我使用的所有
命令，以及每个命令
的详细解释。

能够执行这些命令并查看它们的输出，而不需要
切换到终端，这使得
交互地探索我需要生成
证书的命令，并在下一次
我需要生成类似的证书或密钥时跟踪这些命令变得非常容易。

### 在同一文档中跟踪时间和未来工作

由于 **Org Babel** 是 [Org Mode](http://orgmode.org/) 的一部分，所以利用
Org 的时间跟踪和项目规划特性是很简单的。

我通常在我通常的组织设置中记录我的时间，但是当
适当的时候，能够在我正在处理的
相同的文字文档中记录我的时间是很好的。

你可以在[的](https://github.com/jpf/dial-a-cat/commit/bdb90f1d9b7ba1e7fd97152169330d026b45e812#diff-aacb4ce123f9200898dff4a35e6a8461L901)文档中看到时间跟踪和项目计划的例子。(寻找带有`CLOCK`、`DONE`、
或`TODO`的线条。

### 轻松保持代码和文字的一致性

能够保持代码和文章同步是我第一次遇到的问题，当时我正在为 Twilio 写博客，搜索这个“圣杯”是我第一次对
文化编程感兴趣的原因。我想要的是一个能够从同一个文件中生成
Markdown 和代码的工具，这正是 **Org
Babel** 所能做到的。

在我将 Twilio 博客上我最喜欢的
[博文转换成一个有文化的文档后，](https://www.twilio.com/blog/2013/10/1-855-meow-jam-sending-cat-pictures-over-the-phone-via-space-age-technology.html) [在这个过程中发现了不一致](https://github.com/jpf/dial-a-cat/commit/5096cf9a58e87ac677e5303653c57861928444a4#diff-3f41e546893dc64b71aaacad12cad815L143)，我在 **Org Babel** 上被卖了。

现在，因为我*知道*我的代码和文字将会一直
一致，我发现自己对代码做了快速的修改，否则我
不会这样做。

## 描述代码的最好方式是什么？

现在我有了一个工具，可以很容易地写一篇关于我的代码的文章，我面临着以前从未考虑过的新问题。

我想满意地回答这个问题“我怎样才能最好地描述这段代码？”是一生的努力。

以下是我目前面临的具体问题:

### 有文化的文档更难重构

一旦一段代码嵌入到散文中，修改这段代码就变得非常困难。

正因为如此，我发现写一个有文化的
程序的最好方法是先从代码开始，等着在大量的重构之后把代码
变成有文化的文档。

### 随着项目的增长，文档的结构需要改变

描述一个短程序可以像“从上到下”描述
程序一样简单，正如我在我的
[令牌清理器](https://github.com/jpf/token-cleaner)项目中所做的那样。

然而，随着项目的增长，我发现我用来描述小项目的结构在变成中等规模的项目时会发生变化，而在变成大项目时又会发生变化。

### 我应该描述每个程序的每一行吗？

我一直在努力解决的另一件事是在逐行描述程序和完全跳过大部分代码之间找到正确的平衡。我觉得理想的
方法更倾向于“逐行”的方法，但是我认为我在这方面需要更多的实践经验。

# 关闭

识字编程是一门非常值得学习的学科，
我还有很多东西要学，我期待在未来的几十年里
在编写识字程序方面做得更好。

我很想听听您对这份文件的意见和反馈。请通过推特[联系我](https://twitter.com/jf)！

*   乔尔·弗兰西斯，2016 年 5 月