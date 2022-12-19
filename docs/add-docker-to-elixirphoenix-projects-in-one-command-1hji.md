# 在一个命令中添加 Docker 到 Elixir/Phoenix 项目

> 原文：<https://dev.to/jackmarchant/add-docker-to-elixirphoenix-projects-in-one-command-1hji>

最近，我写了大量的灵药代码，一些 Phoenix 网站和一些其他的小型灵药应用程序。每当我创建一个新项目时，有一件事一直困扰着我，那就是我会想立刻添加 Docker，因为我知道会依赖 Redis 或 Postgres 等，或者在项目进行到一半时添加，这会大大降低我拼凑东西的速度。

我喜欢 Elixir 的一点是，无论是 web 应用程序还是从耳朵里出来的监督树，你都可以很快开始编写应用程序。无论如何，我发现自己又回到了以前使用 Docker 的项目中，复制了一些必要的文件来开始我所习惯的工作流程。

## 前坞站

所以我决定尝试在一个 Elixir CLI 应用程序(Escript)中修复这个问题。它真的没做什么，它写了一些文件，做了一些字符串替换，使设置 docker 变得容易。我称它为 [exdocker](https://github.com/jackmarchant/ex_docker) ，因为我不再像以前那样有创造力了。

### 安装

确保`~/.mix/escripts`在您机器的`$PATH`中。你可以通过将`export PATH=~/.mix/escripts:$PATH`添加到你的`.bashrc`或者类似的文件中来实现。

1.  `mix escript.install hex ex_docker`
2.  `source ~/.bashrc` -加载脚本并`$PATH`更新

### 用法

*   创建新的药剂项目`mix phx.new my_project` `exdocker my_project`
*   添加到现有的 Elixir 项目`exdocker my_project`或`exdocker .`中，在当前目录下运行它。

在项目的根目录下创建了三个文件:

*   你的 docker 容器的配置
*   Dockerfile -指定运行 Elixir/Phoenix 需要安装什么
*   makefile——docker-compose 命令的方便目标

然后，您可以从根目录运行 make init shell 来构建和运行 Docker 容器，当该命令完成时，您将处于安装了 Elixir 和 Mix 的 shell 会话中，这样您就可以像往常一样继续开发。

任何时候你需要使用它，它都可以用`exdocker`来执行。

如果您觉得这有用，请务必告诉我！