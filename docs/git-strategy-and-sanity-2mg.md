# 明智的 Git 策略

> 原文：<https://dev.to/pratikaambani/git-strategy-and-sanity-2mg>

“啊！！git 上有很多文章，我不想要另一篇。”——我在发表前评论这个的第一反应。😜

嗯，在 git 中提交代码库之前，我们作为一个团队遵循一组预定义的规则。我在这里分享同样的东西，因为我发现为了保持 git 的理智，它们非常有前途。

1.  您的代码已格式化。(如果你也患有这种恐惧症，请在评论中告诉我😧 ).
2.  你敢犯错误的代码。😈👿
3.  不要忘记在每次提交时定期更新您的 README.md。你不妨看看[这篇文章](https://dev.to/pratikaambani/a-meaningful-readmemd-565a)。
4.  提交消息必须以吉拉机票号码开始(即 XYZ-1234)。
5.  提交消息的第二个字必须是(添加、删除、更改、重构、更正)之一。
6.  没有人提交主分支和特征/父分支。

# 扩展和实用程序:

我强烈建议您配置，

*   [用 Jenkins](https://www.fourkitchens.com/blog/article/trigger-jenkins-builds-pushing-github/) 配置 GitHub，这将帮助您在提交 GitHub 时触发自动化的 Jenkins 构建
*   [用 Sonar Qube](https://medium.com/devops-process-and-tools/jenkins-configuration-523f6c3d0896) 配置 GitHub，这将在推送 GitHub 中的任何代码时触发代码审查
*   吉拉的 GitHub 可以帮助你检查吉拉的进度(提交日志)

# 创建新存储库时的规则:

这将是在生产环境中创建的任何新存储库的默认结构。
[![repo rules](img/ba90827f9d5ae6ec9a5c301bb1a58c1b.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--2ueYA3Lt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3sj045ra25p8v9vim0db.jpg)

*   母版将只包含 README.md
*   发布将仅通过特征/父分支传递。
*   只有在审查代码时，功能/开发和功能/修复才会合并到功能/父项中
*   所有新的分支将是发展分支的延伸
*   需要新的回购？:想创建多少就创建多少，但只保留它们的私密性。
*   默认协作:只对需要的人

像往常一样，建议总是受欢迎的。编码快乐，愿代码保佑你。😇