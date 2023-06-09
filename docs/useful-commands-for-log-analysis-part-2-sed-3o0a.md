# 用于日志分析的有用命令:第 2 部分— Sed

> 原文：<https://dev.to/chapindb/useful-commands-for-log-analysis-part-2-sed-3o0a>

### 用于日志分析的有用命令:第二部分— Sed

继续之前的帖子[讨论 bash 技巧&技巧](http://pythonicforensics.com/useful-cmds-1/)这里有一些在文本中有用的命令，特别是日志和文件分析。

<figure>[![](img/411c0a17f170e8aa7161dab37fe3345d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WddiVkgp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AktI0q4oGqa3xj4DB) 

<figcaption>照片由[布鲁诺·马丁斯](https://unsplash.com/@brunus?utm_source=medium&utm_medium=referral)上传 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

### sed 使用简介

在本例中，我们将使用 sed 来帮助规范化我们正在处理的数据。这个工具是一个流编辑器，在操作数据时非常有用。在下面的场景中，我们将使用它来查找和替换文本字符串。

在下面的例子中，我们有一个 squid 代理访问日志，并将从日志文件中提取一个唯一的域列表。使用与上次类似的过程，我们将迭代地构建我们的命令来进一步细化我们的结果。首先，我们来看看日志的格式:

```
$ head -n 3 access.log 
10.53.56.81 - - [26/Nov/2017:14:20:26 +0000] "CONNECT aus5.mozilla.org:443 HTTP/1.1" 200 0 "-" "Mozilla/5.0 (Windows NT 10.0; WOW64; rv:47.0) Gecko/20100101 Firefox/47.0" TAG\_NONE:HIER\_DIRECT 
10.53.56.81 - - [26/Nov/2017:14:20:26 +0000] "GET https://aus5.mozilla.org/update/6/Firefox/47.0.2/20161031133903/WINNT\_x86-msvc-x64/en-US/release/Windows\_NT%2010.0.0.0%20(x64)/SSE3/default/default/update.xml HTTP/1.1" 200 946 "-" "Mozilla/5.0 (Windows NT 10.0; WOW64; rv:47.0) Gecko/20100101 Firefox/47.0" TCP\_MISS:HIER\_DIRECT 
10.53.56.81 - - [26/Nov/2017:14:20:27 +0000] "GET http://download.mozilla.org/?product=firefox-56.0-complete-bz2&os=win&lang=en-US HTTP/1.1" 302 585 "-" "Mozilla/5.0 (Windows NT 10.0; WOW64; rv:47.0) Gecko/20100101 Firefox/47.0" TCP\_MISS:HIER\_DIRECT 
```

Enter fullscreen mode Exit fullscreen mode

如上所示，我们在第 7 列中有 URL。使用 awk，正如我们在[之前的文章](http://pythonicforensics.com/useful-cmds-1/)中所做的，我们可以通过选择第 7 个空格分隔的列
来隔离这个字段，如下所示

```
$ head -n 3 access.log | awk '{ print $7 }' 
aus5.mozilla.org:443 https://aus5.mozilla.org/update/6/Firefox/47.0.2/20161031133903/WINNT\_x86-msvc-x64/en-US/release/Windows\_NT%2010.0.0.0%20(x64)/SSE3/default/default/update.xml 
[http://download.mozilla.org/?product=firefox-56.0-complete-bz2&os=win&lang=en-US](http://download.mozilla.org/?product=firefox-56.0-complete-bz2&os=win&lang=en-US) 
```

Enter fullscreen mode Exit fullscreen mode

前三个条目都是同一个域，尽管我们需要清理我们的 URL 来提取域。首先，让我们删除查询字符串数据。查询字符串包含在网站页面之间传递的参数，并遵循？在 URL 中找到的字符。为了消除这一点，让我们再次使用 awk，将分隔符设置为？并显示字符前的字段:

```
$ head -n 3 access.log | awk '{ print $7 }' | awk -F '?' '{ print $1 }' 
aus5.mozilla.org:443 https://aus5.mozilla.org/update/6/Firefox/47.0.2/20161031133903/WINNT\_x86-msvc-x64/en-US/release/Windows\_NT%2010.0.0.0%20(x64)/SSE3/default/default/update.xml 
[http://download.mozilla.org/](http://download.mozilla.org/) 
```

Enter fullscreen mode Exit fullscreen mode

既然我们已经处理了查询字符串，让我们删除协议语句。这包括 http://和 https://，但也可以是其他协议，如 ftp://。由于在该日志中我们只看到 HTTP 和 HTTPS 协议，我们将手动指定要删除的协议；虽然有其他方法可以做到这一点，但是让我们使用 sed:

```
$ head -n 3 access.log | awk '{ print $7 }' | awk -F '?' '{ print $1 }' | sed 's|http\(s\)\*://||' 
aus5.mozilla.org:443 aus5.mozilla.org/update/6/Firefox/47.0.2/20161031133903/WINNT\_x86-msvc-x64/en-US/release/Windows\_NT%2010.0.0.0%20(x64)/SSE3/default/default/update.xml 
download.mozilla.org/ 
```

Enter fullscreen mode Exit fullscreen mode

在上面，我们已经指定了一个 sed 替换表达式。sed 命令中的首字母 s 执行替代函数。该函数的语法如下所述(手册页中也有详细介绍):

*   | -该字符是替代函数部分之间的分隔符。它通常是一个/字符，但是因为我们在模式中包含了/字符，所以我们必须使用另一个字符。通常使用|作为替代。
*   第一部分是 sed 将寻找的模式。在我们的例子中，我们指定我们想要查找匹配正则表达式 http(s)*://的任何内容，这将同时匹配 http://和 https://协议说明符。
*   第二部分在这里是空的，因为我们使用 sed 来删除字符串。在其他情况下，我们可以在最后两个管道字符之间放置一个替换字符串。

我们的 URL 仍然有页面 URL 路径和端口号，我们可以使用 awk 删除它们。我们将使用两个 awk 语句，用不同的分隔符来删除这些值:

```
$ head -n 3 access.log | awk '{ print $7 }' | awk -F '?' '{ print $1 }' | sed 's|http\(s\)\*://||' | awk -F '/' '{ print $1 }' | awk -F ':' '{ print $1}' 
aus5.mozilla.org 
aus5.mozilla.org 
download.mozilla.org 
```

Enter fullscreen mode Exit fullscreen mode

太好了！我们到了子域，我们可以使用我们在之前的文章中创建的 usort 别名[来生成统计数据。为了进一步演示这一点，让我们用 cat 替换 head -n 3 语句来查看整个文件。](http://pythonicforensics.com/useful-cmds-1/) 

```
$ cat access.log | awk '{ print $7 }' | awk -F '?' '{ print $1 }' | sed 's|http\(s\)\*://||' | awk -F '/' '{ print $1 }' | awk -F ':' '{ print $1}' | usort 
1 36cc206a.akstat.io 
1 a.scorecardresearch.com 
1 a.visualrevenue.com 
1 a1.vdna-assets.com 
1 ads.lfstmedia.com 
[...] 
146 cdn.images.express.co.uk 
149 px.moatads.com 
186 pagead2.googlesyndication.com 
205 dt.adsafeprotected.com 
380 [www.google.com](http://www.google.com) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了这个列表，我们可以用多种方式进一步处理这些数据:

*   删除子域，以更好地统计域的数量
*   将域输入 API 以收集情报(whois、ASN、GeoIP、信誉等)。)
*   搜索特定于一个或多个领域的活动(即 google.com 搜索了什么)
*   如果我们添加端口号和/或协议说明符，我们可以在访问日志中查看这些数据点的频率

*原载于 2018 年 10 月 6 日*[*【pythonicforensics.com】*](https://pythonicforensics.com/useful-cmds-2/)*。*

* * *