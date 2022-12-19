# 如何更改 Jekyll 应用程序中的主题

> 原文：<https://dev.to/craicoverflow/-how-to-change-the-theme-in-your-jekyll-application-k5j>

*最初发布于[https://endaphelan . me/technology/programming/changing-theme-in-Jekyll-application](https://endaphelan.me/technology/programming/changing-theme-in-jekyll-application)T3。*

遵循这些步骤，您将能够在您的 Jekyll 应用程序中更改主题。

创建一个新的孤立分支，并确保它是空的。

```
git checkout --orphan newtheme
git rm -rf .
git clean -dfx 
```

现在让我们添加主题的存储库作为上游远程，并将文件拉进我们的分支。我用的是 Taylan Tatl 的月亮主题。

```
git remote add upstream https://github.com/TaylanTatli/Moon.git
git fetch upstream
git pull upstream master 
```

安装 Gem 依赖项并运行您的应用程序，看看前面的步骤是否成功。

```
bundle install jekyll serve 
```

目前一切都好吗？很好。现在是时候合并你的博客文章、配置和其他自定义文件了。

这将带来您来自`master`的帖子:

```
git checkout master -- _posts 
```

您可以安全地合并这些文件和文件夹:

*   `README.md`等自定义降价文件。
*   `_posts`目录。
*   `_drafts`目录。
*   `CNAME`文件。
*   图标文件。
*   其他自定义文件和目录，如图片。

一些旧文件会与新主题中的文件冲突。这里最安全的做法是将文件复制到一个新名称，然后手动合并。

```
git show master:_config.yml > _config.yml.old 
```

不小心覆盖了一个主题文件？没问题:

```
git checkout upstream/master -- index.md 
```

我建议手动合并这些文件:

*   `_config.yml`
*   `Gemfile`

现在用`newtheme`分支替换`master`:

```
git checkout newtheme
git merge -s ours master --allow-unrelated-histories
git checkout master
git merge 
```

再次运行应用程序，查看您的更改是否生效:

```
jekyll serve 
```

推动您的更改:

```
git push 
```

删除`newtheme`分支，不留犯罪证据。

```
git branch -d newtheme 
```

## 学分

这个解决方案改编自 Daniel Pelsmaekerwith 在 StackOverflow 上的[回答](https://stackoverflow.com/a/37186333)，可以与 Git、Bundler 和 Jekyll 的最新版本一起工作。