# Git 命令使一个 fork 保持最新

> 原文：<https://dev.to/philnash/git-commands-to-keep-a-fork-up-to-date-2327>

我最近在 Twitter 上看到了以下关于 git 的推文:

> ![Cory House 🏠 profile image](../Images/b6f41d5c274123b20530e2595bf37da7.png)科里的房子🏠@ housecor![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)我❤️饭桶。但老实说，这让我害怕了很多年。我认为我需要理解它所有的强大功能才能富有成效。我发现事实并非如此。你可以在 Git 中使用大约 6 个命令来提高效率:
> 
> branch
> check
> add
> commit
> pull
> push2018 年 8 月 14:46 PM - 19 日[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1031190760278970368)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1031190760278970368)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1031190760278970368)

这是真的，你可以用这些命令完成大部分工作。

如果你想为一个开源项目做贡献(甚至可能是[这个网站](https://dev.to/ben/devto-is-now-open-source-5n1))该怎么办？)在 GitHub、GitLab 或 BitBucket 上呢？你还需要几个命令来更新你的 fork，即`remote`、`fetch`和`merge`。让我们看看它们是如何使用的。

## 叉开

当您派生一个项目时，您会在自己的名称空间下创建一个副本。要使用该项目，您需要将存储库克隆到您自己的机器上。下面用我分叉的一个回购来举例: [Twilio 的 Node.js 包](https://github.com/philnash/twilio-node)。分叉后，我们克隆回购:

```
git clone git@github.com:philnash/twilio-node.git 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以看到`remote`的第一个用例。在`twilio-node`目录中运行`git remote`，我们会看到以下内容:

```
git remote
origin 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这不是太有用，它只是表明我们有一个远程回购称为“起源”。用`--verbose`标志再次运行它会显示更多的信息:

```
git remote --verbose
origin  git@github.com:philnash/twilio-node.git (fetch)
origin  git@github.com:philnash/twilio-node.git (push) 
```

Enter fullscreen mode Exit fullscreen mode

这样更好，这表明远程存储库是我们克隆的存储库，我们可以从它那里获取数据，也可以向它推送数据。

## 做出改变

当向回购协议贡献一些东西时，在分行这样做是最容易的。这样可以保持主分支的整洁，便于更新。科里在他的推文中列出的六个命令涵盖了设置您的更改；你用`branch`、`checkout`分支创建一个新分支，做你想要的改变，`commit`多次，最后`push`分支到原点，你的分叉。然后你可以创建你的拉请求，并希望它被接受。

过了一段时间后，你发现你想提出另一个拉请求，但原来的回购已经转移了。您需要更新您的 fork，以便使用最新的代码。您可以删除您的 fork 并再次经历派生和克隆 repo 的过程，但这是许多不必要的工作。相反，添加上游存储库作为 repo 的另一个远程存储库，并从命令行使用它。

## 添加上游

为此，我们再次使用`remote`命令，这次是为了`add`一个新的遥控器。找到原始 repo 的 URL，并将其添加为新的 remote。按照惯例，这个遥控器被称为“上游”,尽管你可以随便叫它什么。

```
git remote add upstream git@github.com:twilio/twilio-node.git 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们再次检查回购协议的远程，我们将看到起源和上游。可以用`-v`作为`--verbose`的快捷键。

```
git remote -v
origin git@github.com:philnash/twilio-node.git (fetch)
origin git@github.com:philnash/twilio-node.git (push)
upstream    git@github.com:twilio/twilio-node.git (fetch)
upstream    git@github.com:twilio/twilio-node.git (push) 
```

Enter fullscreen mode Exit fullscreen mode

## 抓取最新的

为了更新回购协议，我们现在可以从上游回购协议中获取最新信息。看起来是这样的:

```
git fetch upstream
remote: Counting objects: 6427, done.
remote: Compressing objects: 100% (549/549), done.
remote: Total 6427 (delta 5156), reused 5105 (delta 4939), pack-reused 934
Receiving objects: 100% (6427/6427), 2.54 MiB | 1.28 MiB/s, done.
Resolving deltas: 100% (5399/5399), completed with 391 local objects.
From github.com:twilio/twilio-node
   6a6733a8..73656c50 master -> upstream/master 
```

Enter fullscreen mode Exit fullscreen mode

从存储库中下载对象和引用，但不将其应用到我们正在处理的分支。我们希望将更新应用到主分支，所以要确保它是签出的。

```
git checkout master 
```

Enter fullscreen mode Exit fullscreen mode

## 将一切融合在一起

使主分支与遥控器保持一致`merge`遥控器的主分支进入我们自己的分支，就像这样:

```
git merge upstream/master 
```

Enter fullscreen mode Exit fullscreen mode

如果你把你的工作放在主分支之外，这将会顺利进行。最后`push`这些对 fork 的更新使它也是最新的。

```
git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

现在主分支上的所有内容都是最新的。如果您需要更新不同的分支，请用分支名称 master 替换您正在使用的分支。

## 快捷键

如果你一直在用`git pull`工作，那么你可能已经看到了一个潜在的快捷方式。`pull`是`fetch`和`merge`的组合，所以要在一个命令中执行这两个动作，您可以运行:

```
git pull upstream master 
```

Enter fullscreen mode Exit fullscreen mode

## 叉开心

这就是你在为开源做贡献时保持 fork 最新所需要知道的一切。添加上游作为新的`remote`回购、`fetch`上游回购和`merge`您想要更新的分支。

关于这里各种命令的更多细节，请看一下 Pro Git 书籍中的[“使用遥控器”。](https://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes)

记住，如果你被 git 卡住了，看看[哦，妈的，git！](http://ohshitgit.com/)感谢你所有的自救方法。

分叉快乐！