# 每周命令:与 tig 一起回顾 Git 历史

> 原文：<https://dev.to/roperzh/weekly-command-going-over-git-history-with-tig-4ajp>

有许多 UI 实用程序允许您探索 Git 存储库，但是对我来说，没有什么比`tig`更好的了。

[![A GIF showing examples of the usage of the tig command](img/9f11881aa7585a09cc84d3da07f923f4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9dU7J1PS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/4419992/38970312-139b8b28-436b-11e8-82d7-019f5397e7c8.gif)

## 基础知识

```
tig [view] [-options] [revs] 
```

Enter fullscreen mode Exit fullscreen mode

`tig`是 Git 的一个基于文本的用户界面，可以直接从您的终端使用它来浏览当前工作目录中的存储库。它可以直接调用，也可以通过管道 Git 输出来调用。

### 视图

该程序公开了不同的*视图*，每个视图显示来自存储库的不同数据。你可以通过可选的*视图*参数告诉`tig`使用一个视图，或者你可以在`tig`以默认的键绑定打开后切换视图:

| 视角 | 争吵 | 好吧，好吧 |
| --- | --- | --- |
| 主视图 | - | m |
| 差异视图 | - | d |
| 日志视图 | 原木 | l |
| 显示视图 | 显示 | - |
| 责备观点 | 责备** | b * |
| grep view | grep ** | g |
| 参考文献视图 | 参考文献 | r |
| 状态视图 | 状态 | s |
| 帮助 | -救命 | h |

 <small>*T3】*</small>

 **   您需要在 UI 中选择一个需要提供参数的文件

如果你熟悉 Git 命令，视图名称几乎是不言自明的，我强烈建议你下载`tig`并亲自尝试不同的视图，它们导航起来非常直观。

### 寻呼机模式

您还可以通过 stdin 将常规 Git 命令传输到`tig`，例如:

```
$ git show | tig 
```

Enter fullscreen mode Exit fullscreen mode

[![output of the git show command piped into tig](img/07d804393d0bbf89834a0a0e92d215b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9EWriwUS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/4419992/39009773-f496ca08-43e2-11e8-9c42-adc35b57b4b1.jpg)

## 资源

*   [tig 手动](https://jonas.github.io/tig/doc/manual.html)
*   [tig 库](https://github.com/jonas/tig)*