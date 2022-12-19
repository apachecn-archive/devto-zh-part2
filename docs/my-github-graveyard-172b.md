# 我的 GitHub 墓地

> 原文：<https://dev.to/avalander/my-github-graveyard-172b>

继[艾萨克](https://dev.to/isaacandsuch/github-graveyards-ill-show-you-mine-49lh)和 [K](https://dev.to/kayis/my-github-graveyards-2l95) 之后，我决定在 GitHub 上发布一些我的旧项目。

# [任务列表](https://github.com/Avalander/task-list)

这是我实际完成的一个项目。这是一个简单的待办事项列表，目标是尝试渐进式 web 应用程序。它完全离线工作(甚至没有后端)，使用服务人员和 IndexedDB 来存储源文件和数据。

# [恢复营业](https://github.com/Avalander/restventures)

Restventures 背后的想法是建立一个经典的文本冒险游戏，但不是文本命令，玩家将通过 REST 端点与游戏交互。它从未起飞，但我仍然认为这个概念很酷。有一天我可能会继续下去。

# [五子棋](https://github.com/Avalander/GoBot)

这是我第一次也是最后一次尝试用 Go 构建东西。这是一个不和谐的机器人，计划是实现几个文本命令，这将做很酷的事情。我唯一执行过的命令是`uptime`，它记录了机器人已经运行了多长时间。尽管我没有走多远，但我学会了如何构建和部署 Go 服务器。

# [草莓报警](https://github.com/Avalander/strawberry-alarm)

这是另一个完成的项目。这是我提交给 [Game Dev 的令人敬畏的一周](https://www.gamedev.net/calendar/event/95-week-of-awesome-v/)竞赛的一款游戏。有趣的部分是，我使用函数式反应式编程来构建它(嗯，可以说，它仍然是黑客马拉松代码)。我在[我的游戏开发博客](https://www.gamedev.net/blogs/blog/2227-avalanders-journal/)上写了一些关于这个过程和事后分析的帖子。

# [甘道夫](https://github.com/Avalander/Gandalf)

甘道夫是一个管理 shell 脚本的向导(因此得名)。基本上，我输入的不是`nano /usr/local/bin/my-script`，而是`gandalf create my-script -e nano`(任何编辑器都可以通过`-e`参数指定)。它允许创建模板并在脚本中包含模板，模板是脚本可以访问的文件或目录。

最初我想用它为我的项目编写可定制的样板文件，但是我从来没有把它做得足够复杂。我仍然用它来处理简单的脚本，因为它帮助我区分我创建的脚本和第三方软件包安装在我的`/usr/local/bin`目录中的脚本，这在清理旧东西时很方便。

# [榆树样板](https://github.com/Avalander/elm-boilerplate)

这是一个带有 webpack 的 Elm 项目的模板项目。没什么特别的，我只是厌倦了每次开始一个新项目时都键入相同的 webpack 配置文件。