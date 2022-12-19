# 我在 Windows 上运行开发到源代码

> 原文：<https://dev.to/nsebhastian/im-running-devto-source-code-on-windows-5bb>

大家好，

我的名字是内森，我目前正在寻找对有趣的 OSS 做出贡献。既然我有安装了 WSL 的 windows 开发机器，我想我将从帮助 Windows 文档开始。

我已经阅读了文档和 gorails 教程，现在该站点在我的本地主机上运行良好。更多细节，你可以看我对这个问题的评论

[![nathansebhastian avatar](../Images/89f518b03fae50ac943de81cb8f8427f.png) ](https://github.com/nathansebhastian)  **[nathansebhastian](https://github.com/nathansebhastian)** commented on [Sep 09, 2018](https://github.com/thepracticaldev/dev.to/issues/306#issuecomment-419692198)

大家好，

我想为 dev.to 做点贡献，因为我有 windows machine 来开发 WSL，所以我想我会从这里开始。我已经阅读了文档和 gorails 教程，现在该站点在我的本地主机上运行良好。下面是我运行它的步骤概要:

1.  首先安装 WSL 18.04 并检查 postgre，使用 apt 安装

2.  从 debsource 安装 NodeJS 8

    ```
    curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
    sudo apt-get install -y nodejs 
    ```

    如果 npm -v 给出语法错误:word unexpected(应为“in”)，您可以尝试重新启动终端。它对我有用

3.  安装来自 https://yarnpkg.com/en/docs/install#debian-stable 的纱线

4.  使用 gorails 教程安装 ruby 2.5.1

5.  跳过 git 配置，因为我已经有一个了

6.  使用 gorails 教程安装 Rails 5.1.6

7.  Fork dev.to git 并将其克隆到我的本地机器上

8.  gem 安装捆绑器

9.  宝石安装工头

10.  运行`rbenv rehash`

11.  捆绑安装

12.  bin/yarn-network-time out 1000000(我遇到了这个[问题](https://github.com/yarnpkg/yarn/issues/4890)

13.  获取 API 密钥(仅限 algolia)

14.  将 database.yaml 配置为连接到在 Windows 中运行的 postgresql，而不是 Ubuntu。确保它看起来像下面这样

    ```
    development:
    <<: *default
    database: PracticalDeveloper_development
    host: localhost
    username: postgres
    password: root 
    ```

    您必须添加 **host: localhost** 否则 Rails 将会连接到 Unix Postgres，而不是 Windows。WSL 的 Postgres 仍然有一些问题。看这个[问题](https://github.com/Microsoft/WSL/issues/2628)

15.  如果语句超时，将变量注释掉

16.  bin/setup 并等待直到完成

17.  bin/启动。第一次运行作业需要一段时间。

18.  打开 Windows Chrome，点击 localhost:3000

[![dev to running on windows](../Images/df4976c5bd790cbe44cb5e1246a7b3b0.png)T2】](https://user-images.githubusercontent.com/10413648/45261385-c2862f00-b42a-11e8-9eba-38f230426bcf.png)

[![dev to running on windows 2](../Images/b2250c6e0ac8ffcdecab17a404f71b95.png)T2】](https://user-images.githubusercontent.com/10413648/45261389-d03bb480-b42a-11e8-9d80-13bd64a4a342.png)

我没有做任何事情，除了浏览周围，将包括其他 API 键，并尝试注册后，看看控制台是否会产生一些错误。我希望这有所帮助。

如果需要更多信息，请随时联系我:)

PC 详情:操作系统:Windows 10x 64 WSL:Linux Ubuntu 18.04 仿生海狸

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/306#issuecomment-419692198)

这也是我的第一篇帖子，很高兴见到大家😀