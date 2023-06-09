# 用 Git 推送 WordPress 主题更新

> 原文：<https://dev.to/jackharner/pushing-wordpress-theme-updates-with-git-khi>

你经常更新 WordPress 的主题吗？有点让人头疼，对吧？通常必须通过 FTP 手动完成，或者通过后端上传新的 zip 文件。

今天我将向你展示如何更新你的网站，只需:

```
$ git push live 
```

Enter fullscreen mode Exit fullscreen mode

听起来好得难以置信？不是的。看看这个:

## 先决条件

1) SSH 访问您的 web 服务器
2) Git 安装在您的本地机器和 web 服务器上

这种设置最适用于一次性的定制主题(如[HarnerDesigns.com](https://harnerdesigns.com)上的主题)，而不适用于制作发行的主题。

## 在本地创建您的回购

首先，为你的项目创建一个文件夹并输入:

```
$ mkdir git-test && cd git-test 
```

Enter fullscreen mode Exit fullscreen mode

然后初始化回购:

```
$ git init 
```

Enter fullscreen mode Exit fullscreen mode

或者，你可以克隆我的 [Blank2 - Blank 主题](https://github.com/harnerdesigns/blank2)回购来开始:

```
$ git clone https://github.com/harnerdesigns/blank2.git git-test 
```

Enter fullscreen mode Exit fullscreen mode

## 在远程服务器上创建一个裸回购

裸 repo 是包含 git 项目工作树的目录，只是没有存储所有文件。它本质上只是一个包含所有文件的目录。git 目录。

SSH 到您的服务器:

```
$ ssh <user>@<server>:<sshPort> 
```

Enter fullscreen mode Exit fullscreen mode

为裸回购创建一个文件夹并输入:

```
<user>@<server> $ mkdir git-test.git && cd git-test.git 
```

Enter fullscreen mode Exit fullscreen mode

记住您在服务器上的什么地方创建了这个目录，以后您会需要它(在您的主目录中创建也很好)。

初始化裸回购:

```
<user>@<server> $ git init --bare 
```

Enter fullscreen mode Exit fullscreen mode

## 勾起来

你崭新的光秃秃的 git repo 有一个目录`hooks`,里面有一堆。样本文件。我们想在回购接收到新数据后做些什么，所以创建一个名为`post-receive`(无扩展名)的新文件，并在其中添加以下内容:

```
#!/bin/bash
GIT_WORK_TREE=<pathToWordPressInstall>/wp-content/themes/git-test git checkout -f 
```

Enter fullscreen mode Exit fullscreen mode

这将做的是，任何时候你推这个 git repo，设置 repo 的工作树到你的 WordPress 安装中的主题文件夹，然后强制签出文件。

## 从“云”上退下来

现在注销您的远程服务器。是时候将新的 repo 添加为本地 repo 的遥控器了。

确保您在项目的文件夹中:

```
$ git remote add live ssh://<server>/home/<user>/git-test.git 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！现在你需要做的就是添加一些文件，像平常一样提交它们，然后运行:

```
$ git push live 
```

Enter fullscreen mode Exit fullscreen mode

能够快速更新 WordPress 网站可以更快地修复错误，减少花在争论文件上的时间。将这个想法与 Gulp 这样的 taskrunner 结合起来，你就可以快速更新了。

让我知道你是否使用这种方法或者你是否有其他方法来管理 WordPress 主题！

### 我写的更多

*   [在伪元素中使用字体 Awesome 5](https://harnerdesigns.com/blog/using-font-awesome-5-in-pseudo-elements/)
*   [自定义文章类型的档案没有显示在 WordPress 菜单中](https://harnerdesigns.com/blog/missing-custom-post-type-archives/)
*   我终于再次拥有了 JackHarner.com！