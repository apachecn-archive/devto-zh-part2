# git 中的自动化语义版本控制

> 原文：<https://dev.to/franiglesias/automate-semantic-versioning-in-git-ah9>

我刚刚写了一个小的命令行工具来自动标记 git 中的语义版本。

[你可以在 github 资源库](https://github.com/franiglesias/versiontag)中看到并下载，在那里你会找到完整的文档。

**versiontag** 在你的项目中寻找最后一个版本标签并更新它。

当您准备好进行标记时，您只需使用您想要更新的版本级别调用`versiontag`。仅此而已。它会向您显示当前版本，新版本，并要求您确认添加并将其推送到遥控器。

这是一个来自 **versiontag** 的版本控制的例子，其中你可以看到所有的步骤和细节:

```
➜ versiontag git:(master) ./versiontag patch 'Add License'
Current version : v1.0.1
New tag :         v1.0.2
Do you agree? ([y]es or [N]o): y

Executing git tag -a v1.0.2 -m Add License (patch version) ...

Tag v1.0.2 was created in local repository.

Push it:

    git push origin v1.0.2

Delete tag:

    git tag -d v1.0.2

Do you want to push this tag right now? ([y]es or [N]o): y

Executing git push origin v1.0.2 ...

Counting objects: 1, done.
Writing objects: 100% (1/1), 179 bytes | 179.00 KiB/s, done.
Total 1 (delta 0), reused 0 (delta 0)
To https://github.com/franiglesias/versiontag
 * [new tag] v1.0.2 -> v1.0.2 
```

想法和改进将受到欢迎(特别是从远程获取最新版本标签，并区分与版本无关的标签)。

HTH :-)