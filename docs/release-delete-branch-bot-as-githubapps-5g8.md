# 将 Delete-branch-bot 发布为 GithubApps

> 原文：<https://dev.to/kamontia/release-delete-branch-bot-as-githubapps-5g8>

# 特征

我们发布了与 [@chaspy](https://dev.to/chaspy) 共同开发的名为 **Delete-branch-bot** 的 GitHubApps。
当分支合并到主分支时，自动删除分支。

你不用再按“删除分行”按钮了！

之前:必须按“删除分支”按钮，这不酷。
[![](../Images/e9024bfdafbaebe61172c773bb27b0b8.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--NCxOw6Bq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n4y6te5pyd8egv9tswzs.png)

After: Delete-branch-bot 监视您的存储库的事件。当拉请求被合并到主请求中时，它会自动删除分支。很酷。
[![](../Images/275fccaf4d744f208f7373cffdcd8e8b.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--VojOxDUu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jg9b6desi1apvhfodl6h.png)

# 如何安装

1.  进入[删除分支机器人](https://github.com/apps/delete-branch-bot)页面或搜索*删除分支机器人*
2.  将其安装到您的存储库中
3.  准备自动删除分支。祝你好运:)

# 技术

建筑很简单。

1.  亚马逊 API 网关
2.  亚马逊拉姆达。用 Go 语言实现。

[![](../Images/e4791e4001822b207c7a8994969d14c0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dH4AEpAU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3iof0vnf1uv48ooc0d8e.png)

这是我的第一个帖子。很抱歉我的英语不好。

如果你评论什么，我会很高兴。:)