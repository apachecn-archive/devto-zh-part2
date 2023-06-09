# TIL:从 CLI 查询 Dash.app

> 原文：<https://dev.to/jonasbn/til-query-dashapp-from-the-cli-3eb5>

**[破折号](https://kapeli.com/dash)**MAC OS 和 iOS 的文档浏览器是一个俏皮的工具。

它可以集成许多工具，如编辑器等。但是您也可以与 CLI 集成。

这个例子是针对 **Bash** 的，我在我的`.bash_profile`
中添加了以下函数

```
function dash {
    open "dash://$1"
} 
```

Enter fullscreen mode Exit fullscreen mode

这让我来做:

```
$ dash «query» 
```

Enter fullscreen mode Exit fullscreen mode

这将打开 **Dash** 应用程序并列出结果。

```
$ dash chmod 
```

Enter fullscreen mode Exit fullscreen mode

列出提及`chmod`的所有页面

[![Ruby result for chmod](img/9ae6ca55c0a8fdda18f80fd9768a4791.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b1AMh2OH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonasbn.github.io/til/dashapp/dash-chmod.png)

您甚至可以使用可配置的搜索配置文件来限制您的结果集，就像这样:

```
$ dash ruby:chmod 
```

Enter fullscreen mode Exit fullscreen mode

列出了所有提到`chmod`的**红宝石**页面，以及**红宝石**搜索简介的一部分页面，但是你可以看到图片。

[![Ruby result for chmod](img/1556fa239b3e2731e6b0be086f45236f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Dw7yQbzD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonasbn.github.io/til/dashapp/dash-ruby-chmod.png)

## 参考文献

*   [Dash 网站](https://kapeli.com/dash)

[这种胡麻](http://jonasbn.github.io/til/dashapp/query_dash_from_the_cli.html)是今晚从[我的胡麻收藏](http://jonasbn.github.io/til/)中精心策划、调味和精选的。

如果您有问题或改进，请告诉我