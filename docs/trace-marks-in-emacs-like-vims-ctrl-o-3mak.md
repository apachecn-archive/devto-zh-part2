# Emacs 中的跟踪标记，如 Vim 的 Ctrl-o

> 原文：<https://dev.to/acro5piano/trace-marks-in-emacs-like-vims-ctrl-o-3mak>

在 Vim 中，我们可以通过反复点击`C-o`来跟踪标记，这是当前 Vim 会话的编辑历史。在 Emacs 中，我们可以通过点击`C-x C-x`回到最近的标记，但是我想回到更远的标记。

我仔细阅读了 Emacs 手册，找到了解决方法。

[https://www . GNU . org/software/emacs/manual/html _ node/emacs/Mark-ring . html](https://www.gnu.org/software/emacs/manual/html_node/emacs/Mark-Ring.html)

我们可以通过在 Emacs 中反复点击`C-u C-SPC`来追踪标记。

# 工作原理

Emacs 具有名为`Mark-Ring`的标记存储器，可存储多达 16 个标记位置。与 Vim 的`C-o`不同，Emacs 的`C-u C-SPC`不会跳转到其他缓冲区。这意味着 Emacs 不会跳转到其他文件。我更喜欢这种行为，因为当我们使用 Emacs 时，我们会在其中打开很多文件。记住“用 Emacs 做任何事情”。

# 再者

通过 Emacs 的一行配置，我们可以像`C-u C-SPC C-SPC C-SPC`一样连续返回标记。

```
(setq set-mark-command-repeat-pop t) 
```

Enter fullscreen mode Exit fullscreen mode

如果你使用头盔，你会喜欢`helm-ring`。

[![helm](img/95edb467e173e86596ccb735f172fd63.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YTIp05fx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g98u6jkkbtis1b094oda.gif)

[https://github.com/emacs-helm/helm/blob/master/helm-ring.el](https://github.com/emacs-helm/helm/blob/master/helm-ring.el)

它可以通过舵面找到标记线。

# 参

*   [http://emacs.rubikitch.com/set-mark-command-repeat-pop/](http://emacs.rubikitch.com/set-mark-command-repeat-pop/)
*   [https://tuhdo.github.io/helm-intro.html](https://tuhdo.github.io/helm-intro.html)