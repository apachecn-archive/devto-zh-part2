# 解释 git 配置，就像我五岁一样

> 原文：<https://dev.to/jessachandler/explain-git-config-like-im-five-750>

当我为一个运行在 heroku 上的应用程序设置 git 配置，最终拥有一个独立的登台和生产环境时，我感觉自己完全理解了。然而，我这样做已经有几个月了，现在事情并不像我想象的那样工作。**显然，我一点也不明白。**

本质上，我希望有一个 heroku staging 应用程序(通用 heroku url)和我的 heroku production 应用程序(在我的 url ),我可以在我的终端上通过 heroku CLI 获得这两个应用程序的日志并管理它们。这似乎是一个相当典型的需求，我希望我不是唯一感到困惑的人。

我的情况是这样的:

*   我按照 Heroku 开发指南来设置我的环境...*我想*
*   一切都运转良好。我的主推送进入了我的 github repo，并自动部署在我的生产 heroku 应用程序上。我的分期推送直接到了我的 heroku 分期 app。
*   我很关心一个问题，在我的主分支上运行了`heroku logs`和一系列 heroku 命令，只是为了不断地获得关于我的暂存环境的信息。再多的`heroku logs -r production`也改变不了这种情况。
*   为了从我的生产应用程序中获取日志，我运行了`heroku git:remote -a appname -r production`。遗憾的是，这将我的生产指针从 github repo 改回到 heroku(它期望从 github 获取代码)。
*   我在 git.config 中将生产远程的`url`手动更改回 github。现在，更改再次正确地推送到 github，但是我无法使用 CLI 查看生产 heroku 日志。

这怎么这么混乱？我试着在谷歌上搜索 git-config 指南，但是遇到了一个又一个解释者，他们只是一步一步地输入 shell 命令进行设置，并没有真正讨论*内部到底发生了什么。git/config 文件。*

我求助于我神奇的 **DEV。在这里向**的朋友求助。解释这整个。把我当 5 岁小孩一样对待。

FWIW -我的看起来像:

```
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
    ignorecase = true
    precomposeunicode = true
[branch "master"]
    remote = production
    merge = refs/heads/master
[remote "heroku"]
    url = git@heroku.com:myapp.git
    fetch = +refs/heads/*:refs/remotes/heroku/*
[remote "production"]
        url = https://github.com/me/myapprepo.git
    merge = refs/heads/master
[remote "staging"]
    url = https://git.heroku.com/adjective-noun-number.git
    fetch = +refs/heads/*:refs/remotes/staging/*
[heroku]
    remote = staging
[push]
    default = tracking 
```