# Zotero 卓越同步

> 原文：<https://dev.to/michaelmior/zotero-remarkable-sync-8ph>

在过去的几个月里，我真的很喜欢我的出色的平板电脑。(去年我给[写了一篇简短的评论](https://dev.to/michaelmior/remarkable-review-apc)，如果你想看更多细节的话)。我对这款设备最大的不满之一是，用它获取文件可能会很痛苦。目前没有网络应用，所以唯一的选择是 Windows 和 macOS 的桌面应用或 Android 的移动应用。一旦我看到有人在 GitHub 上发布了一个[卓越的 API](https://github.com/splitbrain/ReMarkableAPI) ，我知道我必须找到某种方式来减轻我的痛苦。

我使用 Zotero 来管理我的论文参考资料，不出所料，阅读论文是我的卓越。我决定想办法让我想看的新报纸出现在我的网站上。使用卓越的和 Zotero APIs，这被证明是一个相当简单的周末项目。你可以在 GitHub 上找到结果[。](https://github.com/michaelmior/zotero-remarkable)

首先，需要设置几个环境变量，详见自述文件。然后，一旦脚本运行，它将在 Zotero 集合中查找项目，下载它们的附件，并将它们上传到卓越的 API。将这个脚本放入 cron 作业意味着我的论文现在会定期同步。我目前在 Heroku 上免费为自己托管这个程序，使用[调度程序插件](https://devcenter.heroku.com/articles/scheduler)来运行这个任务。我禁用了任何 web 进程(`heroku ps:scale web=0`)，所以唯一运行的是 cron 作业。由于 cron 作业运行速度非常快，它完全符合 Heroku 的免费使用限制。

将来我可能会决定在资源库中添加一个 [Heroku 按钮](https://devcenter.heroku.com/articles/heroku-button)，但是手动配置相当简单。只需设置所需的环境变量，禁用 web dyno，并使用调度程序设置 cron 作业。希望这最终对其他人有用！