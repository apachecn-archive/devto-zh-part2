# 正则表达式中的冒险

> 原文：<https://dev.to/lefebvre/adventures-in-regular-expressions-588k>

Xojo 的每个版本都包含一个详细的发行说明列表，它作为一个名为 ReleaseNotes.htm 的 HTML 文件包含在 Documentation 文件夹中。

为了让这些更容易访问，我需要一种方法将它们放入 wiki。如果我能把 HTML 内容复制并粘贴到 wiki 页面上，那将是最简单的，但是 MediaWiki 不能完全处理该文件中的所有 HTML，所以我需要一种方法来清理它。

看一下包含发行说明的单行代码:

```
<tr class="spacer"><td class="reportid"><a href="feedback://showreport?report_id=52522">52522</a></td><td class="category">Framework » All</td><td class="desc">Added AntiAliasMode property on Graphics class. This property controls the level of interpolation/quality when drawing scaled Pictures. Valid modes are from the Graphics.AntiAliasModes enumeration: LowQuality, DefaultQuality, and HighQuality. The default is DefaultQuality.</td></tr> 
```

这是 HTML 表格中的一行。问题在于这一部分:

```
<a href> 
```

MediaWiki 不使用它来显示链接。所以我需要一种方法来清理。我还需要删除一些“tbody”标签。

通常我会掰着手指头，开始一个 Xojo 项目，然后使用字符串查找/替换来修改文本。但是这既麻烦又乏味。

所以我想，为什么不试试正则表达式(RegEx)？我真的不太了解 RegEx，坦率地说，它们让我有点害怕。毕竟，谁能理解这样的东西呢:

```
\b(\d{1,2})([/-])(\d{1,2})\2(\d{4}|\d{2})\b 
```

上面的胡言乱语称为正则表达式模式，显然是用于日期验证的。

无论如何，我想我会给 RegEx 一个尝试。我的第一个挑战是找到一个模式，我可以开始，也许可以修改。在前往 RegEx 类的 Xojo 文档页面时，我发现这个模式可以从 RegEx 上的文本中删除 HTML 标签。替换页面的示例代码:

```
<[^<>]+> 
```

因为我确实需要删除 HTML 标签，所以我认为这是一个很好的起点。

要解释这种模式:

1.  “
2.  “[”括号开始一个字符类
3.  “<sup>”</sup>表示不是，所以“<sup><>”</sup>表示除了“<”和“>”以外的所有字符
4.  “]”结束字符类，而“+”表示一个或多个匹配项
5.  以“>”字符结尾

这意味着匹配所有以`<`开头的文本，除了`<`和`>`之外，后面还有一个或多个字符，以`>`结尾。

仅供参考:在所有的正则表达式测试中，我都使用了 RegExRX，这是一个优秀的处理正则表达式的工具，由 Xojo 的长期开发者日本京都电子工业株式会社·泰基纳伊用 Xojo 编写。

启动 RegExRX 后，我将上面的模式放入搜索模式字段，并将一小段 HTML 粘贴到源文本字段。我可以立即看到所有的 HTML 标签都匹配了。

[![](img/a906516be54f0da00b45200a258b03fa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y2xprn9y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.xojo.com/wp-content/uploads/2018/10/2018-10-12_09-54-33.png)

但是我不需要匹配所有的 HTML 标签，我只需要`<a>`和`<tbody>`。对上面的模式稍加修改就可以告诉它只查找以`<a`或`</a`开头的文本。查看正则表达式页面上的正则表达式引用，我看到“？”字符表示 0 或 1 个匹配。所以我可以把模式改成这样:

```
</?a[^<>]*> 
```

这意味着查找以`<a`或`</a`开头的 HTML 标签。在这个模式中，我可以看到只有`<a>`和`</a>`标签被突出显示:

[![](img/d13ed9951d4c781a83d7f3afda0f2fa2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J3ablD4H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.xojo.com/wp-content/uploads/2018/10/2018-10-12_13-24-33.png)

为了真正删除标签，我使用了一个替换，并用一个空字符串替换匹配的标签。切换到 RegExRX 中的 Replace 选项卡，如下所示:

[![](img/cb1fd6593f182648c05740fca6f13377.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--u7_2SgmS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.xojo.com/wp-content/uploads/2018/10/2018-10-12_13-28-45.png)

为了对`<tbody>`做同样的事情，我将模式改为:

```
</?tbody[^<>]*> 
```

现在是时候在 Xojo 项目中使用它了。您使用 RegEx 类来处理正则表达式。该函数获取文本(从发行说明文件中加载)并应用搜索模式和空替换模式来删除标签并将结果粘贴到剪贴板中，这样我就可以将它粘贴到 wiki 中:

```
Private Function CleanHTML(html As String) as String
  Dim re As New RegEx
  re.SearchPattern = "</?a[^<>]*>" // Find <a href></a> tags for removal
  re.ReplacementPattern = ""
  re.Options.ReplaceAllMatches = True
  Dim plainHTML As String = re.Replace(html)

  // Remove any <tbody> tags as MediaWiki doesn't process them
  re.SearchPattern = "</?tbody[^<>]*>"
  re.ReplacementPattern = ""
  re.Options.ReplaceAllMatches = True
  plainHTML = re.Replace(plainHTML)

  Dim c As New Clipboard
  c.Text = plainHTML

  Return plainHTML
End Function 
```

这非常有效，但在尝试之后，我决定保留反馈案例的链接，以便您可以点击案例编号，在反馈应用程序中打开案例，并阅读其所有历史记录。这意味着我不需要删除`<a>`标签，而是需要修改它们:

```
<a href="feedback://showreport?report_id=52522">52522</a> 
```

对此:

```
[http://feedback.xojo.com/case/52522 52522] 
```

为此，我现在需要使用一个子组来保存案例 ID，这样我就可以将它用作替换字符串的一部分。要创建子组，需要使用括号将 RegEx 模式的各个部分分组。本质上，我希望为`<a>`开始标记、值(案例 ID)和`</a>`结束标记提供清晰的模式部分。

我首先将`<a>`标签搜索简化为只匹配开始标签:

```
<a[^<>]*> 
```

然后，我添加了一个部分来匹配案例 ID，这只是一系列 1 或更多的数字。RegEx 有一个只匹配数字的命令，它是“\d”，我们可以在上面加上“+”来根据需要重复一次或多次。然后，我用括号将它括起来，得到一个结果如下的组:

```
<a[^<>]*>(\d+) 
```

最后，我添加了匹配结束标签`</a>`的部分，以获得最终的模式:

```
<a[^<>]*>(\d+)</a> 
```

最后，您可以看到`<a>`、`</a>`标记和案例 ID 值都匹配:

[![](img/825034ce8aa69c428d76090fa3fe3156.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xgCq_vny--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.xojo.com/wp-content/uploads/2018/10/2018-10-12_13-32-42.png)

这种变化的模式意味着我现在有了一个可以用来替换的组。切换到 RegExRX 中的 Replace 选项卡，您会注意到整个匹配的文本都被删除了，因为替换模式是空白的。在替换模式中键入“$1”(这包含具有案例 ID 值的组)会在替换的文本中显示案例 ID:

[![](img/408c7b5c2a7d3803c059950bfd49a962.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zd7xQwn6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.xojo.com/wp-content/uploads/2018/10/2018-10-12_13-33-30.png)

现在我把我想要的其余文本放入替换模式中:

```
[feedback://showreport?report_id=$1 $1] 
```

被替换的文本现在看起来像我想要的:

[![](img/ea2660da3ed1b0332654b57d56eeb502.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uswRBA2S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.xojo.com/wp-content/uploads/2018/10/2018-10-12_13-34-06.png)

下面是更新后的 Xojo 函数:

```
Private Function CleanHTML(html As String) as String
  Dim re As New RegEx
  re.SearchPattern = "<a[^<>]*>(\d+)</a>" // Find <a href> tags and save case # as a group
  re.ReplacementPattern = "[http://feedback.xojo.com/case/$1 $1]" // Swap in wiki link format with correct URL
  re.Options.ReplaceAllMatches = True
  Dim plainHTML As String = re.Replace(html)

  // Remove any <tbody> tags as MediaWiki doesn't process them
  re.SearchPattern = "</?tbody[^<>]*>"
  re.ReplacementPattern = ""
  re.Options.ReplaceAllMatches = True
  plainHTML = re.Replace(plainHTML)

  Dim c As New Clipboard
  c.Text = plainHTML

  Return plainHTML
End Function 
```

我希望 RegEx 中的这个小冒险已经帮助您体会到它们对于字符串搜索和替换有多棒。我仍然不是专家，但我发现这比使用 InStr 和 friends 进行杂乱的字符串搜索和解析要好得多。