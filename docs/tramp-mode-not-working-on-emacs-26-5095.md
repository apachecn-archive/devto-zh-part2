# 不定期模式在 emacs 26 上不起作用

> 原文：<https://dev.to/highperformancecoder/tramp-mode-not-working-on-emacs-26-5095>

在最近对 emacs 的一次升级中，我发现通过 tramp 远程编辑文件已经不起作用了。这让我很困扰，因为我经常使用它。在 Googling 了很多(much)之后，我发现了一个使用 sshfs 的变通方法，它允许挂载一个远程 ssh 连接作为 fuse 挂载点。

但是 tramp 停止工作的真正原因是现在 tramp 字符串中需要一个额外的“方法”参数，例如

 `^X^F /ssh:remote:file` 

如果没有太多地改变默认值，可以将 ssh 缩写为'-'。

一个很好的附加特性是 su 和 sudo 方法，例如

 `^X^F /sudo:/etc/passwd` 

参见[emacs 26.1 新功能](https://www.masteringemacs.org/article/whats-new-in-emacs-26-1)(搜索“tramp”)。