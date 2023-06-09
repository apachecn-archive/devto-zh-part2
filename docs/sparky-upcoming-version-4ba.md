# 火花，即将推出的版本

> 原文：<https://dev.to/melezhik/sparky-upcoming-version-4ba>

Sparky 是一个轻量级的异步作业运行器，支持通过 docker 和 ssh 运行任务。

这里只是一个关于[即将到来的版本](https://github.com/melezhik/sparky/blob/master/Changes)的新内容的小广告贴。

# 布尔玛 CSS

我换成了[布尔玛](https://bulma.io/)，这意味着更好的设计，至少 IMHO(:

[![projects page](img/361a7275ee6e24815a180898f8aae8ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5DSUkYyQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jorrsywgy9o1k6wr49lf.png)

这是默认的`nuclear`主题(我有物理学硕士学位，这是为什么:)

您可以通过编辑配置文件来改变它:

```
ui:
  theme: cosmo 
```

Enter fullscreen mode Exit fullscreen mode

# 项目页面

现在，带有每个项目的最新构建结果的项目列表显示在索引页面上。

# 手动运行构建

如果您不想等到 cron 运行作业，您可以通过 UI 运行它:

[![run now](img/41eac79afe0594cf5b34ae4d84da647e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x8ed9AyE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xjwifanj60vn7j9d6q5d.png)

# 系统安装人员

都在`utils/`文件夹里。在您的系统上安装 Sparky 时很有用:

*   `$ sparrowdo --local_mode --sparrowfile=utils/install-sparky-web-systemd.pl6`

*   `$ sparrowdo --local_mode --sparrowfile=utils/install-sparkyd-systemd.pl6`