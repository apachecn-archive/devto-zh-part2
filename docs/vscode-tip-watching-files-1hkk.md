# VSCode 提示:监视文件

> 原文：<https://dev.to/seankilleen/vscode-tip-watching-files-1hkk>

这个文件放在 [VS 代码下能做到吗？！](https://vscodecandothat.com/)

有时我需要观察日志文件或其他文件的变化，无论是在我的本地机器上还是远程服务器上。在 windows 上，这可能有点痛苦，您经常不得不将它从您的 IDE 中转储出来。

于是有一天突发奇想，我说“不知道 VS 代码能不能做到这一点”。我搜索扩展，你瞧，VS 代码*能做到这一点！*

## 输入 VS 代码和日志查看器扩展

*   开放 VS 代码
*   点击`Extensions`按钮
*   在搜索框中键入`Log Viewer`
*   找到[日志查看器扩展](https://marketplace.visualstudio.com/items?itemName=berublan.vscode-log-viewer)并安装。

## 设置分机

*   在 VS 代码中，打开命令栏(对我来说，`CTRL + Shift + P`会这样做)
*   键入`Workspace Settings`或类似内容，然后选择`Preferences: Open Workspace Settings`
*   在设置中，为`logViewer.watch`添加一个部分，为您想要观看的文件定义一些标题和模式。下面是一个在不同服务器上观看两个独立文件的例子。我添加了以下内容并保存了我的首选项:

```
{  "logViewer.watch":  [  {  "title":  "Server 1 IIS",  "pattern":  "\\\\servername\\C$\\inetpub\\logs\\LogFiles\\W3SVC2\\u_ex180718.log"  },  {  "title":  "Server 2 IIS",  "pattern":  "\\\\servername2\\C$\\inetpub\\logs\\LogFiles\\W3SVC2\\u_ex180718.log"  },  ]  } 
```

## 有什么结果？

日志查看器打开一个屏幕，我们可以看到所有适用的观察器，并在它们发生变化时查看它们的更新。

[![Results](img/50190e5de376aa7f4e8656a65dd8a173.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HW8n4KmG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://SeanKilleen.cimg/post-images/2018-08-21_vscode-logviewer-watches.png)

我喜欢定义模式，而不仅仅是文件路径，这样我就可以一次查看多个文件。

## 你怎么看？

希望你喜欢这个提示！在评论中留下你最喜欢的技巧，看看 [VS 代码能做到这一点？！](https://vscodecandothat.com/)获得许多其他有用的提示！

[VSCode 提示:观看文件](https://SeanKilleen.com/2018/08/vscode-tip-watch-files/)最初由 Sean Killeen 于 2018 年 8 月 21 日在 SeanKilleen.com[发表。](https://SeanKilleen.com)