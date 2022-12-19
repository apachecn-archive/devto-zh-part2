# 使用 DocFX 为您的项目创建站点

> 原文：<https://dev.to/hardkoded/creating-a-site-for-your-project-using-docfx-4n4g>

# 动机

许多教程解释了如何通过一系列步骤来完成某项任务。假设你和作者有相同的背景，最重要的是，假设你成功地执行了每一个步骤。

所以我想与你分享一个实时(ish)帖子，解释我如何使用 [DocFx](https://dotnet.github.io/docfx/) 创建一个网站。当然，我们的背景会有所不同，但是我希望我可以通过解释正确的步骤和揭露你可能遇到的障碍来帮助你建立你的网站。

# 简介

我们已经使用 [XML 文档注释](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/xmldoc/xml-documentation-comments)在[puppeter-Sharp](https://github.com/kblok/puppeteer-sharp/)上记录了所有代码，并且我们想要使用 [DocFX](https://dotnet.github.io/docfx/) 和 [Github 页面](https://pages.github.com/)创建一个站点。最后，我们想让它在[http://www.puppeteer-sharp.com](http://www.puppeteer-sharp.com)上可用。

# 我们知道什么？

*   我们知道我们需要告诉 dotnet 编译器用文档生成一个 XML 文件。我们已经在这么做了。
    *   我们知道我们想要使用[DocFX]。([https://dot net . github . io/docfx/tutorial/walk through/walk through _ create _ a _ docfx _ project . html](https://dotnet.github.io/docfx/tutorial/walkthrough/walkthrough_create_a_docfx_project.html))
    *   我们希望它成为我们 CI 中的一个自动步骤。

# 番茄 1 -设置

因为我们需要多次运行 CI 构建，并且我们不想弄乱 Puppeteer 的 repo，所以让我们创建一个新的 repo，这样我们就可以玩它了。docfx-playground 听起来是一个相当~~酷~~体面的名字。

[![Repo Image](../Images/43f099f936977b2c2b00e40000e02684.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--StN_vKnE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/kblok.github.io/master/img/docfx-tutorial/1_repo.png)

我们还需要为这个新项目建立一个 CI。[搞定](https://ci.appveyor.com/project/kblok/docfx-playground)。
T3![App Veyor](../Images/305b7a1cda2e44772e1d3cb038eadc3c.png)T5】

现在，让我们将我们的 P#(是的，我称之为 puppeter-Sharp，P#)复制到这个新的 repo 中。

## DocFX

现在，我们需要两个文件夹:

*   我们知道 DocFX 的项目需要一个文件夹。一个`docfx_project`文件夹。
*   我们也知道它会尝试使用一个`_site`文件夹来生成站点，但是因为我们知道 [Github Pages 会寻找一个文档文件夹](https://help.github.com/articles/configuring-a-publishing-source-for-github-pages/)，我们将在那里生成我们的站点。

## docfx_project

如果我们运行`docfx init`，它应该会在我们的项目中创建一个 docfx_folder

> docfx 初始化-q

[![Dcofx](../Images/e97059d43b86e795d8a508bd7e3fe86a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--H5sd_ZuM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/kblok.github.io/master/img/docfx-tutorial/3_docfxproject.png)

厉害！

现在，我们只需要在`docfx.json`文件中设置项目位置:

```
"metadata": [
    {
      "src": [
        {
          "cwd": "../",
          "files": [
            "lib/PuppeteerSharp/**.csproj"
          ]
        }
      ],
      "dest": "api",
      "disableGitFeatures": false
    }
  ], 
```

Enter fullscreen mode Exit fullscreen mode

很公平。现在如果我们调用`docfx metadata`它应该做什么...某物...

> docfx 元数据 docfx_project/docfx.json

嘣！
[![DocFx metadata](../Images/e9d3d65d8e1fb682b65e5b9577c847f0.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--ws2yc7cV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/kblok.github.io/master/img/docfx-tutorial/4_docfx_metadata.png)

我还不知道那些文件是什么。但这是事实。让我们试着建一个网站。

> docfx 构建 docfx_project/docfx.json -o docs

良好的...情况可能会更糟。首先，它在我的`docs`文件夹下创建了一个`_sites`文件夹。我不是故意设置输出`docs`的。
然后...主页和 API 索引页面可以有更多的链接来连接新页面。

*网站首页*
[![First Home](../Images/4655b0f18ccd35b1c5552063ec7ce6dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vp-rPZ-B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/kblok.github.io/master/img/docfx-tutorial/5_first_home.png) 
*API 首页*
[![FirstAPI](../Images/e62e100f9c69b43813675779a91e21d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UtlZNuxW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/kblok.github.io/master/img/docfx-tutorial/6_first_api.png)
*部分 doc 页面*
[![Doc Page](../Images/7ea7acee38a5795a633f9a8737f6fab8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IolLmSg9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/kblok.github.io/master/img/docfx-tutorial/7_first_doc.png)

#### 番茄 1 号结束

## 番茄 2 号-塑造 DocFx 网站

为了去掉`_sites`文件夹，我们需要改变`docfx.json`文件中的`dest`属性。

> " dest ":" "

太好了，现在我们的网站正在使用`docs`文件夹。
现在让我们试着改进一下网站:

*   我会做一些时尚的东西。
*   在主页上添加一些内容。
*   了解如何获得 API 的链接。

我以为这会是一个错过的番茄。但是事实证明，你需要调用`docfx serve`才能使网站处于良好状态！

> docfx 服务器

[![Good API Page](../Images/7a1c3d00553438c4f3d901ccaa5f0b16.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pavPl2ZL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/kblok.github.io/master/img/docfx-tutorial/8_first_good_api.png)

#### 番茄末日 2

## Pomodoro 3 - GitHub 页面和 AppVeyor

好吧，我们有一个网站运行。我们来推一下，设置 Github 页面。

[![Githubpage](../Images/a94b780e7c2723e7034777ee21e02bd4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_ScG0wOx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/kblok.github.io/master/img/docfx-tutorial/9_set_github_page.png)

我们有一个网站！

[![Site](../Images/e3c27e9f389a5e1d208d870a3328b482.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Rcpsn_ii--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/kblok.github.io/master/img/docfx-tutorial/10_first_site.png)

虽然这里有许多需要改进的地方，但是让我们继续讨论 AppVeyor。这个想法很简单:

*   我们只需要在创建标签(release)时构建 docfx。
*   我们还需要更新 DocFx 元数据和网站。
*   最后，我们需要把它推回回购。

因此，AppVeyor 有一个[很棒的文档](https://www.appveyor.com/docs/how-to/git-push/)来帮助我们设置 git，所以我们可以将内容推回到 repo。所以基本上我们需要[在 GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) 上设置一个个人访问令牌，加密它，并在我们的脚本中使用它。git 设置部分看起来像这样:

```
- git config --global credential.helper store
- Add-Content "$HOME\.git-credentials" "https://$($env:git_access_token):x-oauth-basic@github.com`n"
- git config --global user.email "dariokondratiuk@gmail.com"
- git config --global user.name "Darío Kondratiuk" 
```

Enter fullscreen mode Exit fullscreen mode

然后我们生成文档:

```
- docfx metadata docfx_project/docfx.json
- docfx build docfx_project/docfx.json -o docs 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将它提交回回购协议。

```
- git checkout master
- git add docfx_project/*
- git add docs/*
- git commit -m "Docs for"
- git push origin 
```

Enter fullscreen mode Exit fullscreen mode

哎哟...我从 DocFx 得到这个错误

> [18-06-08 12:17:14.994]警告:extract metadata(C:/projects/docfx-playground/lib/puppetersharp/puppetersharp . csproj)工作区失败，出现:[Failure] Msbuild 在处理文件“C:\ projects \ docfx-playground \ lib \ puppetersharp \ puppetersharp . csproj”时失败，出现消息:C:\ Program Files \ dot net \ SDK \ 2 . 1 . 300 \ Sdks \ Microsoft。NET.Sdk\targets\Microsoft。NET . generateassemblyinfo . targets:(161，5):“GetAssemblyVersion”任务意外失败。

当我尝试推回到 GitHub 时，出现了这个错误:

> 致命错误:无法访问'[https://github.com/kblok/docfx-playground.git/':](https://github.com/kblok/docfx-playground.git/':)请求的 URL 返回错误:403
> 550

#### 番茄末日 3

## 番茄 4 - gh-pages 分店

计划有变！我在 Github 上找到一篇[文章，其中提到你可以使用一个名为`gh-page`的分支发布一个 Github 页面。这很好，因为 **docfx 提交不会干扰我的主分支**，而且**我将能够保护我的**](https://help.github.com/articles/configuring-a-publishing-source-for-github-pages/)**[主分支](https://help.github.com/articles/configuring-protected-branches/)** 。

我用一个脚本找到了[这个要点](https://gist.github.com/ramnathv/2227408)来创建一个空的`gh-pages`分支。

```
cd /path/to/repo-name
git symbolic-ref HEAD refs/heads/gh-pages
rm .git/index
git clean -fdx
echo "My GitHub Page" > index.html
git add .
git commit -a -m "First pages commit"
git push origin gh-pages 
```

Enter fullscreen mode Exit fullscreen mode

厉害！现在我们有一个空的`gh-pages`分支。
让我们回到 AppVeyor 代码。为了能够推送至 GitHub，我们需要将`https`添加至我们的遥控器。

```
git remote add pages https://github.com/kblok/docfx-playground.git 
```

Enter fullscreen mode Exit fullscreen mode

然后我们将使用`git subtree push`把我们的站点推到我们新的远程分支。

```
git subtree push --prefix docs pages gh-pages 
```

Enter fullscreen mode Exit fullscreen mode

好的，现在我在 AppVeyor 上得到这个

```
"GetAssemblyVersion" task failed unexpectedly.
331System.NullReferenceException: Object reference not set to an instance of an object. 
```

Enter fullscreen mode Exit fullscreen mode

#### 番茄末日 4:(

# 番茄 5 - Git 减

[这篇文章](https://dev.to/letsbsocial1/deploying-to-gh-pages-with-git-subtree)告诉我，在做`git subtree push`之前，我必须先注册子树。

```
git subtree add --prefix docs gh-pages 
```

Enter fullscreen mode Exit fullscreen mode

我还了解到，在创建一个子树之后，您需要提交并推送该子树:

```
git checkout master
git subtree add --prefix docs gh-pages
docfx metadata docfx_project/docfx.json
docfx build docfx_project/docfx.json -o docs
git add docs/
git commit -m "Docs"
git subtree push --prefix docs pages gh-pages 
```

Enter fullscreen mode Exit fullscreen mode

但是 push 命令卡住了:(

#### 番茄末日 5

# Pomodoro 6 -设置 GitHub 访问令牌

我发现[是一个设置 git 的好脚本](https://github.com/dustinchilson/dustinchilson.com/blob/master/_tools/before_build.ps1)。

所以在尝试推送到 Github 时出现 403 错误是因为我在创建令牌时必须检查“Repo”选项。

[![Repo check](../Images/bd274b99fd8ea453ca44dab546483599.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--459g2WwB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/kblok.github.io/master/img/docfx-tutorial/11_repo_check.png)

我刚刚知道这是注册子树的正确方法

```
git subtree add --prefix docs pages/gh-pages 
```

Enter fullscreen mode Exit fullscreen mode

我们完了。现在我们只需要将所有这些打包到一个 if 块中，这样只有当我们从 GitHub 创建一个新的标签/发布时，文档才会更新。

这就是最终 git 脚本的样子

```
if($env:APPVEYOR_REPO_TAG -eq 'True'){
    git config --global credential.helper store
    Add-Content "$env:USERPROFILE\.git-credentials" "https://$($env:git_access_token):x-oauth-basic@github.com`n"

    git config --global user.email "dariokondratiuk@gmail.com"
    git config --global user.name "Darío Kondratiuk"
    git remote add pages https://github.com/kblok/docfx-playground.git
    git fetch pages
    git checkout master
    git subtree add --prefix docs pages/gh-pages
   docfx metadata docfx_project/docfx.json
   docfx build docfx_project/docfx.json -o docs
    git add docs/* -f
    git commit -m "Docs build $($env:APPVEYOR_BUILD_VERSION)"
    git subtree push --prefix docs pages gh-pages
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们设置我们的自定义域，我们已经准备好了！

[![Setup domain](../Images/e1960456d40a5eb5e27143bfb6f0d1b6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aDYmAg97--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/kblok/kblok.github.io/master/img/docfx-tutorial/12_setup_domain.png)

哦，如果你想知道这个 DocFx 错误:

> [18-06-08 12:17:14.994]警告: [ExtractMetadata](//C:/projects/docfx-playground/lib/PuppeteerSharp/PuppeteerSharp.csproj) 工作区失败，原因为:[Failure] Msbuild 在处理文件“C:\ projects \ docfx-playground \ lib \ puppetersharp \ puppetersharp . csproj”时失败，消息为:C:\ Program Files \ dot net \ SDK \ 2 . 1 . 300 \ Sdks \ Microsoft。NET.Sdk\targets\Microsoft。NET . generateassemblyinfo . targets:(161，5):“GetAssemblyVersion”任务意外失败。

结果是，尽管得到了元数据，元数据仍在生成。

#### 番茄末日 6

# 最后的话

这是一次有趣的旅行，虽然比我预期的时间长了一点。虽然六个番茄只有两个半小时，但我每天只花了一个番茄来完成这个任务，所以我的脑袋在这个上面转了将近一个星期。我希望这篇文章能帮助你经历这个过程，让你的生活更轻松。

永远不要停止编码！

最初发布于[harkoded.com](http://www.hardkoded.com/blog/creating-docfx-site)