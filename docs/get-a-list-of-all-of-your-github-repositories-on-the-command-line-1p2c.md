# 获取所有 GitHub 库的列表

> 原文：<https://dev.to/benjaminblack/get-a-list-of-all-of-your-github-repositories-on-the-command-line-1p2c>

我的帖子通常是我自己的笔记和参考资料，我在这里发布，希望其他人会觉得有用。

需要`jq`命令行 JSON 处理器实用程序，这里称为使用`npx`。

用你的 GitHub 用户名替换`<username>`,准备在命令行输入你的 GitHub 密码。

如果您有超过 100 个存储库，您将需要多次运行这个命令，每次都要编辑`page=?`参数。

```
curl --silent --user "<username>" "https://api.github.com/user/repos?page=1&per_page=100" | npx jq '.[].ssh_url' | while read repo
do repo="${repo%\"}" repo="${repo#\"}" echo "$repo" done 
```

Enter fullscreen mode Exit fullscreen mode

这个无意义的`repo="${repo%\"}"`从该行中去掉了前后双引号`"`。

如果您想要的是`git://`或`https://`方案而不是 SSH，那么在`jq`查询中，用`git_url`或`clone_url`替换`ssh_url`。

要将输出保存到一个文本文件，在末尾添加一个重定向，如:

```
 ...
   echo "$repo"
done > output.txt 
```

Enter fullscreen mode Exit fullscreen mode