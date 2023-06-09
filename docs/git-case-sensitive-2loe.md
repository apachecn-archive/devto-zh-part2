# Git 区分大小写

> 原文：<https://dev.to/emmanuelgautier/git-case-sensitive-2loe>

今天，我想知道为什么在 CI 管道上，测试由于丢失文件而失败，但是那些文件在本地文件系统上，并被推送到 GIT 存储库。此问题来自重命名的文件从小写更新为大写。

Windows 和 OSX 文件系统不区分大小写。因此，如果你是这两个操作系统之一的用户，这里有两个你可以使用的解决方案。

一个简单的解决方法是使用如下的`git mv`命令:

```
git mv camelcase camelCase
git commit -m "fix camelCase name" 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用的另一个解决方案是将 GIT 配置为区分当前文件系统类型的大小写。

根据该文档，git-config

> 内部变量，支持各种变通方法，使 Git 能够在不区分大小写的文件系统上更好地工作，如 APFS、HFS+、FAT、NTFS 等。举个例子，如果一个目录列表在 Git 期望“makefile”的时候找到了“Makefile”，Git 会假设这真的是同一个文件，继续记为“Makefile”。

要启用它，将值`false`分配给`core.ignoreCase`配置

```
git config core.ignorecase false 
```

Enter fullscreen mode Exit fullscreen mode