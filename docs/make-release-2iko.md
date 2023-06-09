# 发布:节省时间，提高可靠性

> 原文：<https://dev.to/ebreton/make-release-2iko>

[![make release: save time, gain in reliability](img/7f386e505582d54e1eb138b2ed026bfb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--krC99TWb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2sz1pxsnuzu89ypwspn8.png)

***制造钻石固体的最佳方法是自动化***

# 一个故事，凡与现实有雷同之处纯属巧合...

> [虚构用户]:...我有一页写着五百个错误。太多了！
> 
> 【你，不提 500 是错误码，不是错误数】:Hmmm...不应该是这样的....您运行的是哪个版本？

啊。

具体是哪个版本？

偏题...页脚的小数字？隐藏评论？API 的版本化 URL？等等...

> [用户]:上面写着 0.3.1
> 
> [你]:谢谢。让我检查一下变更日志

啊。

啊。

变更日志到底在哪里？

我们有一个开始吗？它是最新的吗？它是为营销人员写的吗？好吧，如果你有一个变更日志，无论如何也不会有什么帮助...

# 目标

为了促进上面这种对话，我想向您介绍一些处理和工具。

最终目标应该是

```
$ make release
... 
```

Enter fullscreen mode Exit fullscreen mode

你会有

1.  github rc-X.X.X 上的新分支
2.  github 上基于这个分支的新版本
3.  包含有意义信息的更新的变更日志...
4.  ...这实际上来自于这个版本和前一个版本之间的差异
5.  检查代码当前/下一版本的方法
6.  自述文件上的更新徽章(因为徽章很酷)

<figure>[![](img/a97279b6f24bc18eee4bc75fdfca5a6e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dMUXB5PO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://manu.breton.ch/blog/conteimg/2018/05/objective-numbered.png) 

<figcaption>一个命令统治他们所有人</figcaption>

</figure>

# 先决条件

*   制造
*   你在 github 上的应用
*   码头工人

Docker 将使您不必安装其他依赖项，如 ruby(用于生成变更日志)或 python(用于管理版本)

# 设置

## 文件

这个库( [**)发布者**](https://github.com/ebreton/release-maker) )被设计成一个插件:你应该复制粘贴以下文件到你的应用程序的库中

| 文件 | 描述 |
| --- | --- |
| ├──生成文件 | 所有命令的入口点 |
| ├──·宾 |  |
| │ ├── Dockerfile | 用可用的*请求*库从 python:3 定义了一个(非常)简单的 docker 映像。使用`make build`在本地构建图像 |
| update _ release . py | 管理脚本，运行健全性检查，更新版本号，发布版本到 github |
| 样本环境 | 定义环境变量的值 |
| versions . py | 版本号写入/读取/导入的位置 |

## 环境变量

只需将 *.env.sample* 文件复制粘贴(或重命名)到*。env* 并填写您的详细信息:

```
$ cp .env.sample .env
$ vi .env
# GITHUB_* are used for github APIs, to automatically create release & Changelog
GITHUB_OWNER?=repo owner
GITHUB_REPO?=repo name
GITHUB_USER?=your github user
GITHUB_KEY?=your key
CHANGELOG_GITHUB_TOKEN?=${GITHUB_KEY} 
```

Enter fullscreen mode Exit fullscreen mode

## 码头工人

一旦您“导入”了存储库内容并定义了您的 var，您就需要构建/提取将要使用的 docker 映像。否则会得到这种错误:

```
$ make vars
Unable to find image 'python-requests:latest' locally 
```

Enter fullscreen mode Exit fullscreen mode

python 映像是用`make build`在本地构建的。这是一个简单明了的 *Dockerfile* ，它依赖于 python 3 的官方映像，并将安装库 *requests* 。

```
# bin/Dockerfile
FROM python:3

WORKDIR /usr/src/app
RUN pip install --no-cache-dir requests

ENTRYPOINT ["python"]
CMD ["--help"] 
```

Enter fullscreen mode Exit fullscreen mode

因为我们正在设置 docker，所以您实际上可以提取将生成 Changelog 的第二个映像。`make pull`也会为你跑`make build`

```
$ make pull
... 
```

Enter fullscreen mode Exit fullscreen mode

该命令现在应该可以正常工作了(使用您的环境变量的值)

```
$ make vars
  Version: 0.1.1-rc

  GITHUB_OWNER=ebreton
  GITHUB_REPO=release-maker
  GITHUB_USER=ebreton
  GITHUB_TOKEN=xxx
  CHANGELOG_GITHUB_TOKEN=xxx 
```

Enter fullscreen mode Exit fullscreen mode

## 版本

Changelog 的生成依赖于(至少)一个 git 标签的存在。如果没有，您应该运行

```
git tag 0.1.0 # or whatever initial version number you prefer
git push --tags 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用
检查当前配置

```
$ make version
CHANGELOG GENERATOR:
Version: 1.14.3

APPLICATION:
Version: 0.1.1-rc

Updating release numbers...
INFO - compute - will set _version=0.1.1-rc
INFO - compute - will set _build=5026db3b8b001c5d8991c90d0528161abec7bbeb
INFO - compute - will set _release=0.1.0-12-g5026db3 
```

Enter fullscreen mode Exit fullscreen mode

# 用法

实际上，你只会在每次你想发布的时候使用`make release`(惊讶吗？)

仅此而已。

当然，还有一些其他的命令，一些我们见过的用于设置，一些用于诊断或检查情况。以下是清单:

| 命令 | 描述 | 评论 |
| --- | --- | --- |
| `make version` | 显示应用程序的当前版本(不是以前部署的版本)，以及 github-changelog-generator 的版本 | - |
| `make vars` | 显示环境变量的值 | 需要*。环境*文件 |
| `make build` | 从 python:3 构建一个 docker 映像，请求可用 | - |
| `make pull` | 获取 github-changelog-generator 的最后一个 docker 映像 | - |
| `make ps` | 列出 docker 容器(信息比 docker ps 少) | - |
| `make changelog` | 根据标签和 PRs 构建/更新新版本的 Changelog | 有效的环境变量必须在*中定义。env* (或环境) |
| `make release` | 确认版本号后，执行所有发布流程。 | 必须在分支**主**中执行 |
| `make push-qa` | 更新标签 **qa-release** 并重建变更日志 | - |
| `make push-prod` | 更新标签 q **a-release** 并重建 Changelog | 要求确认分支和版本 |

我们还没有讨论最后两行...他们背后有某种奖励，这将在最后一节看到。

# 窗帘后面

我猜你已经预见到了。

## 1。github rc-X.X.X 上的新分支

这是一个应急分支，以防需要快速修复

## 2。github 上基于这个分支的新版本

这允许更新徽章或从 github API 获取版本号

## 3。包含有意义信息的更新的变更日志...

这是您在诊断任何问题时将寻找的强制上下文

## 4。这实际上来自于这个版本和前一个版本之间的差异

感谢[费拉里马科](https://github.com/ferrarimarco/docker-github-changelog-generator)！

## 5。检查代码当前/下一版本的方法

多亏了 *versions.py* ，你可以在你代码的任何地方导入和使用它

## 6。自述文件上的更新徽章(因为徽章很酷)

确实是

# 奖金

> 使用 git 标签进行持续集成和持续部署

如果你碰巧

1.  要在 docker 容器中运行应用程序，
2.  并在您的注册表(或 docker hub)中设置了自动构建，
3.  使用管弦乐队

使用标签进行持续集成和部署非常方便

在我的例子中，orchestrator 依赖于`qa-release`标签来升级集成环境，依赖于`prod-release`标签来升级产品。

这一切都是自动的，除了扳机，它仍然在你的手上。因此，运行`make release`将调用`make push-qa`，并开始集成的升级过程。你也可以不经过所有的发布过程就升级，自称`make push-qa`(没有确认)

为了更新生产，您必须呼叫`make push-prod`，它将要求您确认。

请注意，这两个命令将更新您的 Changelog(通过调用`make changelog`)，因为我们希望它与每个标签指出的提交保持一致。

```
$ make push-qa
# update tags
git tag -f qa-release
Updated tag 'qa-release' (was db42b2e)
git push --tags --force
...

$ make push-prod
You are on branch 'master'
on version '0.1.2-rc'

Type [y] to comfirm push
or any other key to abort:
... 
```

Enter fullscreen mode Exit fullscreen mode

不用说，如果您没有配置您的 orchestrator 来更新基于新构建的容器，如果您没有配置您的 registry 来更新基于 git 标签的构建....良好的...上面那两个命令就没那么有用了。

# 结论

我怎么强调自动版本控制和发布的便利性都不为过，还有一致的(自动生成的)变更日志。它只是节省了支持最终用户和诊断问题的团队工作时间。

在我的团队中，我们很早就意识到了这一点，并且我们努力尝试构建我们的拉取请求、我们的变更日志、我们的版本号。但是只要是手动的，这就行不通。太繁琐，绝对没有价值(直到屎打在风扇上)。

首先，*发布者*允许我们专注于开发，专注于评论的内容。其次，它极大地促进了发布过程，以至于我们实际上可以极大地增加我们的发布频率，从一个月一次到一周一次(有时更多)。

很好。方便。

希望你也会觉得有用:)