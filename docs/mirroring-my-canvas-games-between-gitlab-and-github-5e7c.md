# 在 GitLab 和 GitHub 之间镜像我的画布游戏

> 原文：<https://dev.to/matthewodle/mirroring-my-canvas-games-between-gitlab-and-github-5e7c>

大约两年前，当我们开始在工作中使用 GitLab 时，我将我的私人项目迁移到了 git lab。我对 GitHub 没有什么反感，但是我真的很喜欢 GitLab 的界面。问题是 GitHub 是存放你的东西的地方，如果你想让其他人也能方便地访问它。这意味着我必须为每个项目维护两个不同的遥控器。

今天，我解决了这个问题。

GitLab 的镜像推送界面真的很好用。您可以通过设置>存储库>推送至远程存储库来访问它。

[![](img/382678ab123087b246820ea94af453e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OlSjv6kb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2vf8rqo06ojr49k2oo1x.png)
[![](img/1037966fb9ce78cef3549fa3edc1fceb.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--OTQG2gpK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y5e96c14frqptfolo0xw.png)

用法:

*   将带有适当凭证的 GitHub 项目的 URL 添加到“Git 资源库 URL”字段
*   单击“立即更新”
*   GitLab 将显示:![](img/fce61779890461aa2c3377dae1ffaf31.png)
*   GitHub 将显示:![](img/6fb03b380c6c27d657b6f02074425c9d.png)

喔。

来源:

*   [https://stack overflow . com/questions/21962872/how-to-create-a-git lab-web hook-to-update-a-mirror-repo-on-github](https://stackoverflow.com/questions/21962872/how-to-create-a-gitlab-webhook-to-update-a-mirror-repo-on-github)
*   [https://git lab . com/help/workflow/repository _ mirroring # pushing-to-a-remote-repository](https://gitlab.com/help/workflow/repository_mirroring#pushing-to-a-remote-repository)
*   [https://news.ycombinator.com/item?id=8908235](https://news.ycombinator.com/item?id=8908235)