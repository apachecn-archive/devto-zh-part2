# 厌倦了。DS_Store 获得版本控制？

> 原文：<https://dev.to/johnphamous/tired-of-dsstore-getting-version-controlled-2e0k>

每个 Mac 用户都遇到过这种情况。您编写一些代码，将您的更改推送到一个存储库中，这就是它发生的时间。有。DS_Store 文件跨您的存储库。

### 为什么会创建这个文件？

> 在苹果 macOS 操作系统中，。DS_Store 是一个存储其包含文件夹的自定义属性的文件，例如图标的位置或背景图像的选择。

*tldr；操作系统用它来存储文件夹*的元数据

### 如何解决这个问题？

要解决这个问题，您可以重写您的 Git 历史，但是您将不得不在每次。出现 DS_Store 文件。这不是一个很好的解决方案。

你可以给你的新规则。gitignore 文件。这就需要你记得补充了。每次都是 DS_Store。这是一个更好的解决方案，但仍不理想。

### 介绍全局。gitignore

正如。gitignore file 告诉 Git 应该忽略哪些文件。gitignore_global 将规则应用于机器上的所有存储库。

的。gitignore_global 文件可以通过输入以下内容来创建:

**git config—global core . excludes file ~/。gitignore_globals**

创建文件后，可以像在. gitignore 文件中一样添加规则。

### 下一步

那现在怎么办？您可以开始添加不想提交的文件。gitignore_global。您可以在这里找到基于您正在开发的[的示例配置文件列表。](https://github.com/github/gitignore)

此外，如果您想停止创建。DS_Store，您可以在终端中输入以下命令:

**defaults write com . apple . desktop services DSDontWriteNetworkStores true**

重启后，你的操作系统将停止创建那些讨厌的。DS _ 存储文件。

要撤消上述命令，请在终端中输入以下命令:

**defaults write com . apple . desktop services DSDontWriteNetworkStores false**

> 这是一篇来自 GitHub 校园专家博客的交叉文章。