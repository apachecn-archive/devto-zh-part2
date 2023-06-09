# 使用 Travis 安全构建和部署到 GitHub

> 原文：<https://dev.to/preslavrachev/using-travis-for-secure-building-and-deployment-to-github-6ch>

Travis 对于开源社区来说是一个非常有用的工具。它的主要目的是执行测试套件，并确保被观察的存储库的稳定性。然而，由于它的一般执行性质，它可以创造性地用于许多其他任务:

*   从远程 API 和数据源获取数据
*   以不同的格式构建和呈现输出(例如，直接使用模板引擎，或静态站点生成器，如 Jekyll)
*   将数据推送到远程 API，或者使用 GitHub 分支作为持久存储

这些方法的共同点是，在调用外部 API 或试图将转换后的输出推送到 GitHub 分支时，需要进行身份验证。这篇文章将向您展示如何让 Travis 构建并部署输出到一个单独的分支。

假设我们在 GitHub 上托管了一个 [Jekyll](https://jekyllrb.com/) 博客，并且希望在对内容进行更改后推送转换后的内容:

下面是我们的`.travis.yml`文件可能的样子:

```
language: ruby/python/java/etc
script:
- ./deploy.sh 
```

Enter fullscreen mode Exit fullscreen mode

以及对应的`deploy.sh` Bash 脚本:

```
#!/bin/sh

./build_your_outputs
git push origin gh-pages 
```

Enter fullscreen mode Exit fullscreen mode

作为 Travis 构建脚本的一部分直接调用`git push`肯定会失败。Travis 没有权限更改我们的存储库。没问题。

首先，你需要颁发一个特殊的令牌，Travis 或任何其他工具才能使用你的 GitHub 库。GitHub 称这些[为个人 API 令牌](https://blog.github.com/2013-05-16-personal-api-tokens/)。你可以随意使用[发出](https://github.com/settings/tokens)，但是要注意这些基本上就像密码一样，所以要小心对待。为了让 Travis 能够对 repo 或分支进行任何修改，您至少需要给予令牌`public_repo`权限。

现在，我们已经设置了令牌，但是我们如何在 Travis 内部使用它，更重要的是，我们如何以安全的方式使用它呢？毕竟，`.travis.yml`文件是公开的，对吗？确实是这样，但是我们不打算以纯文本的形式存储令牌。

我来给你介绍一下[在 Travis](https://docs.travis-ci.com/user/encryption-keys/) 中存储加密值。为此，您需要安装 Travis CLI:

```
gem install travis 
```

Enter fullscreen mode Exit fullscreen mode

安装了 Travis CLI 后，您现在可以加密任何您想要的东西，并将其添加到`.travis.yml`文件:

```
travis encrypt GITHUB_TOKEN=1234abcxyz —add 
```

Enter fullscreen mode Exit fullscreen mode

这样做的目的是将下面一行添加到您的`.travis.yml`文件中:

```
secure: "abunchofrandomgibberish...." 
```

Enter fullscreen mode Exit fullscreen mode

在幕后，Travis 将使用一个私钥，该私钥是在创建您的帐户时设置的，只有 Travis 知道。所以，这应该是相当安全的。

在脚本执行期间，Travis 将解密这些秘密，并将它们解释为 bash 命令。在我们的例子中，这会将 GitHub 令牌的值设置为环境变量`GITHUB_TOKEN`。然后应该可以从构建脚本的所有部分访问它。太棒了。

剩下的一部分是让 Travis 调用您的站点生成器，并将生成的输出推送到名为`gh-pages`的分支的根。根据 GitHub 的惯例，这个分支中的所有 HTML 文件都可以在`https://your name.guthub.io/reponame`下或者您选择的自定义域中公开访问。虽然你可以在一行中执行所有的命令，但是建议创建一个小的 Bash 脚本(例如`deploy.sh`)并调用它。下面是一个示例部署脚本的样子:

```
git remote add origin-pages https://${GITHUB_TOKEN}@github.com/your_username/your_repo.git
git push origin-pages gh-pages 
```

Enter fullscreen mode Exit fullscreen mode

这应该让 Travis 自动构建和部署。

* * *

*注:这篇文章最初出现在[我的博客](https://preslav.me/2018/05/29/using-travis-for-secure-building-and-deployment-to-github/)T3 上*