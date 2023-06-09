# 为 windows 开发人员设置 macbook

> 原文：<https://dev.to/darraghor/setting-up-a-macbook-for-a-windows-developer-mae>

我最近在工作中得到一台 macbook，这些天我只做网络开发，所以没有 Visual Studio。我不得不做了一些阅读来建立东西，我不想再做了，所以这里有一个帖子给我！

首先是从[https://www.iterm2.com/](https://www.iterm2.com/)下载 iTerm

打开一个新的 iTerm 并运行以下命令来安装 Homebrew(这就像巧克力)

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```

Enter fullscreen mode Exit fullscreen mode

下一步安装我的 zsh

```
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" 
```

Enter fullscreen mode Exit fullscreen mode

按照这个伟大的教程来设置 powershopp 字体和 zsh:[https://github.com/thacherT1D/fishToZsh](https://github.com/thacherT1D/fishToZsh)

用自制软件设置 git

```
brew update && brew upgrade

brew install git

brew link --force git 
```

Enter fullscreen mode Exit fullscreen mode

从[https://www.scootersoftware.com/](https://www.scootersoftware.com/)打开超越比较并转到文件>T3】安装命令行工具确认“bcompare”可以从终端启动。

运行以下代码行来设置一些 git diff 和 merge 魔术，并为代码审查添加一个别名(将这里的 develop 更改为您用作主分支的任何内容)

然后在项目窗口中输入:

```
git config --global alias.code-review = "!git difftool --dir-diff origin/develop...$1"
git config --global diff.tool bc3
git config --global difftool.bc3.trustExitCode true
git config --global merge.tool bc3
git config --global mergetool.bc3.trustExitCode true 
```

Enter fullscreen mode Exit fullscreen mode

对一些有差异的分支运行代码审查，并通过点击合并窗口中的规则按钮打开规则。在这里设置无与伦比的跟踪符号链接！这将使所有的路径像它们应该的那样排列起来。

要使用 Beyond Compare 启动三向合并，请使用命令“git mergetool file.ext”。要使用 Beyond Compare 启动比较，请使用命令“git difftool —dir-diff”。

如果你在 mac 上使用 jetbrains 的 Rider，但是你团队的其他人使用 visual studio，你可以通过编辑。git/info/exclude 文件。下面将隐藏 idea 文件夹。

```
.idea 
```

Enter fullscreen mode Exit fullscreen mode

在 iterm 的首选项中将 iterm 设置为使用您的项目目录作为启动位置

如果你使用 jira，我强烈推荐 zsh 插件“jira”。要记住您的 jira 实例，请将以下内容添加到~/中。zshrc

```
export JIRA_URL=https://jira.myorganisation.com
export JIRA_NAME=doriordan
export JIRA_PREFIX=REC-
export JIRA_RAPID_BOARD=true 
```

Enter fullscreen mode Exit fullscreen mode

然后就可以用

```
jira # performs the default action
jira new # opens a new issue
jira dashboard # opens your JIRA dashboard
jira reported [username] # queries for issues reported by a user
jira assigned [username] # queries for issues assigned to a user
jira branch # opens an existing issue matching the current branch name
jira ABC-123 # opens an existing issue
jira ABC-123 m # opens an existing issue for adding a comment 
```

Enter fullscreen mode Exit fullscreen mode

太好了，都搞定了。这可能是一个伟大的脚本项目:D

接下来安装以下应用[https://www.cockos.com/licecap/](https://www.cockos.com/licecap/)-gif 录制的 Licecap【https://www.spectacleapp.com/】T2-OS x 的窗口管理[https://www.alfredapp.com/](https://www.alfredapp.com/)-增强的聚光灯/助手

你可以使用[http://www.rubicode.com/Software/RCDefaultApp/](http://www.rubicode.com/Software/RCDefaultApp/)来改变 mac 上的默认应用程序。例如，可以从 mail 更改邮件客户端，而不必在 Mail 中设置电子邮件帐户。

照片由 Fabian Grohs 在 Unsplash 上拍摄