# 在 GitHub 页面上创建 Hugo 站点

> 原文：<https://dev.to/dgavlock/creating-a-hugo-site-on-github-pages-3cjo>

# 快速背景故事

所以我刚刚开始我的 web 开发之旅，当我在创建一个网站的时候，我遇到了一个很棒的静态站点生成器，叫做 Hugo！

这是我的第一篇文章，所以我希望它有用:)

# 雨果是什么？

Hugo 自称他们是“建立网站最快的框架”，我不得不说，即使对我这样的初学者来说，他们也让这个任务变得相当简单！经过一段时间的试错和一些 youtubing 之后，我能够很快地在 GitHub 页面上建立并运行一个网站！也就是说，我发现有些事情还不清楚，所以我将尝试一步一步地描述我所经历的事情，以帮助其他人更顺利地建立和运行他们的网站！

# 简介&必备装备

你也可以跟随 Windows 或 Linux 的 [Hugo 安装页面](https://gohugo.io/getting-started/installing/)，因为这篇文章是在 MacOS 上！如果你这样做了，你可以跳到这篇文章的 Hugo 入门部分！

**注意:如果你有自制软件，跳到这里的，如果你还没有[配置 git](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup) 或者有一个 [github](https://github.com/) 账户，请做这两件事。使用您用来创建 github 帐户的电子邮件来配置 git！**

## 安装自制软件

**注意:如果你有自制软件，你可以跳到下载页面**

我不会在这里做任何假设，所以我们会让你安装 MacOS 版的 homebrew，它非常简单，让你以后处理其他任务更加简单！通过按 Command + Space 打开终端，键入“终端”，然后按 enter，然后复制并粘贴以下内容(您应该在您的个人目录中):

```
 /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```

Enter fullscreen mode Exit fullscreen mode

您会想要接受任何抛出的提示，然后砰！搞定了。你现在可以开始酿造了！(注:查看[家酿](https://brew.sh/)这是我得到这段代码的地方)。这将使接下来的几步变得更好！

## 安装 Go

**注意:如果你已经安装了 Go，那就跳过这一步吧！**

现在你已经有了自制软件，要安装 Go，你需要在终端中输入以下内容:

```
 brew install go 
```

Enter fullscreen mode Exit fullscreen mode

再次，接受任何提示，直到下载完成，你将结束与 Go 安装！如果你不知道 Go 是什么，它是由 Google 创建的一种编程语言，现在越来越流行，Hugo 将利用这种语言来创建网站框架。如果你对围棋感兴趣，请点击这里

# 雨果入门

如果您在这里，您应该安装了以下软件:

*   公司自产自用
*   去

## 安装 Hugo

现在来安装 Hugo，你可能已经猜到了，安装 Hugo 和前两次安装一样简单。只需键入以下内容:

```
brew install hugo 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！我们已经准备好和雨果一起工作了！

## 为雨果设置

让我们建立一个目录来做我们所有的工作。在您的终端中，通过键入以下命令转到您的主目录:

`cd ~`

然后为目录名" Sites "键入:`mkdir Sites`

移动到目录`cd Sites`

现在你已经进入了站点目录，现在你可以开始使用 Hugo 了。
第一步真的很简单类型:

```
 hugo new site yoursitename 
```

Enter fullscreen mode Exit fullscreen mode

hugo 新站点命令将会创建你的站点的框架。执行命令
后，您应该会看到以下提示

```
Congratulations! Your new Hugo site is created in /Users/YourName/Sites/yoursitename.

Just a few more steps and you're ready to go:

1\. Download a theme into the same-named folder.
   Choose a theme from https://themes.gohugo.io/, or
   create your own with the "hugo new theme <THEMENAME>" command.
2\. Perhaps you want to add some content. You can add single files
   with "hugo new <SECTIONNAME>/<FILENAME>.<FORMAT>".
3\. Start the built-in live server via "hugo server".

Visit https://gohugo.io/ for quickstart guide and full documentation. 
```

Enter fullscreen mode Exit fullscreen mode

这些方向是你需要采取的下一步措施，让你的网站达到你的目标。我们将简要介绍一下这些组件，以便您能够理解这些组件是什么以及如何与之交互。现在你应该`cd yoursitename`然后`ls`检查目录，你会看到如下:

```
archetypes content layouts static config.toml themes data 
```

Enter fullscreen mode Exit fullscreen mode

这是你的 Hugo 站点的框架，在这里你将为你的站点创建新的内容，一旦我们把你的站点发布到 GitHub 上，我会介绍这些内容。这里需要注意的重要事情是`theme`目录。主题目录是你定制你的站点的地方。

## 雨果主题

Hugo 允许你创建自己的主题或者下载别人做的预制开源主题！预制选项非常适合 Hugo 入门，所以这是我们现在要走的路线。

预先制作的主题可以在[雨果主题](https://themes.gohugo.io/)页面找到。

选择一个主题。

(对于教程，你会看到我的网站使用的编码器主题)。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[【Luiz depra】](https://github.com/luizdepra)/[【Hugo-coder】](https://github.com/luizdepra/hugo-coder)

### 雨果的极简主义博客主题。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Hugo Theme Badge](img/0cca5bfaf2ea7ad9a3fafdefbdd0394e.png) ](https://themes.gohugo.io/hugo-coder/) [ ![MIT License Badge](img/1a1ba9fd8a9930a937d32b0788090dc3.png)](https://github.com/luizdepra/hugo-coder/blob/master/LICENSE.txt)

[![Hugo Coder Logo](img/1febb804234218290ab8b378bf28ea47.png)T2】](https://github.com/luizdepra/hugo-coder)

一个简单干净的雨果博客主题。

[![](img/4a044ded6ca03c11247e4e0b28d2b87b.png)T2】](https://github.com/luizdepra/hugo-coder/blob/mastimg/screenshot.png)

## 现场演示

此处见[。](https://hugo-coder.netlify.app/)

## 快速启动

1.  将存储库作为子模块添加到 Hugo 项目存储库中，`git submodule add https://github.com/luizdepra/hugo-coder.git themes/hugo-coder`。
2.  配置您的`config.toml`。你可以使用[这个最低配置](https://github.com/luizdepra/hugo-coder/wiki/Configurations#complete-example)作为基础，或者在这里寻找关于所有配置[的完整解释。](https://github.com/luizdepra/hugo-coder/wiki/Configurations) [`exampleSite`](https://github.com/luizdepra/hugo-coder/tree/master/exampleSite) 里面的 [`config.toml`](https://github.com/luizdepra/hugo-coder/blob/master/exampleSite/config.toml) 也是很好的参考。
3.  用`hugo serve`建立你的网站，在`http://localhost:1313/`看到结果。

## 额外指南

*   [多语言模式](https://github.com/luizdepra/hugo-coder/wiki/Multilingual-Mode)

## 斯塔克宾

该主题已准备好导入 Stackbit。这个主题可以部署到 Netlify，您可以连接任何无头 CMS，包括 Forestry、NetlifyCMS、DatoCMS 或 Contentful。

[![Create with Stackbit](img/9cfebcb962deecd631c9416959aeedce.png)T2】](https://app.stackbit.com/create?theme=https://github.com/luizdepra/hugo-coder)

## 许可证

编码器是在麻省理工学院的许可下被许可的。

## 维护

这个主题由作者 Luiz de Prá在这些令人敬畏的[贡献者](https://raw.githubusercontent.com/luizdepra/hugo-coder/master/CONTRIBUTORS.md)的帮助下得以维持。

## 赞助

如果你喜欢我的项目或它…

</article>

[View on GitHub](https://github.com/luizdepra/hugo-coder)

一旦你选择了一个主题，点击它的页面上的下载，这将把你带到它的 github 库。点击绿色的“克隆或下载”按钮**注意:确保在弹出框**中点击“使用 SSH ”,然后复制 SSH 密钥。现在你已经复制了 SSH 密钥，回到你的终端，确保你在`themes`目录中，输入`git clone`并按`control + v`将其粘贴到终端中，然后回车！**注意:如果你还没有配置 git，现在你应该有一个名为`coder`或者你选择的任何主题的存储库；`cd coder`然后按‘ls’来查看目录的内容。**

这些都是你需要的文件，使你的网站看起来就像你选择的主题！现在`cd exampleSite`检查那里的文件。

现在，我发现让你的站点快速运行的最简单的方法是从`exampleSite`中取出文件，并用你运行`hugo new site sitename`命令时创建的文件替换它们。换句话说，将所有内容从`exampleSite`目录下的`content`移动到`yoursitename`目录下的`content`。因此，您将对`exampleSite`目录中的所有文件执行此操作，直到该目录完全清空。任何有类似于`config.toml`文件的复制的地方，删除由`hugo new site`命令创建的那个。然后将所有其他内容移出编码器目录，即使它没有匹配的位置，只需将它移出编码器并移到 youwebsitename 目录中。唯一不需要移动的文件是:

```
theme.toml README.md Makefile LICENSE.md 
```

Enter fullscreen mode Exit fullscreen mode

正确移动所有东西是非常重要的！

一旦你完成了这些，就进入 config.toml 文件并查看一下。如果你使用编码器主题，它看起来应该是这样的(没有我添加的注释，在它告诉你在文件中改变什么来定制网站之前，在右边有

### **重要提示:您需要将 baseurl 更改为下面指定的内容！**

```
baseurl = "http://examplesite" <- Change to http://yourgithubusername.github.io

title = "johndoe" <- Name your website here

theme = "hugo-coder" 

languagecode = "en" 

paginate = 20
canonifyurls = true

pygmentsstyle = "b2"
pygmentscodefences = true
pygmentscodefencesguesssyntax = true

disqusShortname = "yourdiscussshortname" <- Your short name for commenting

[params]
    author = "John Doe" <- Your name
    description = "John Doe's personal website" <- How you would describe the site
    keywords = "blog,developer,personal"
    info = "Full Stack DevOps and Magician" <- A small description of what your are
    avatarurl = "images/avatar.jpg" <- your image
    footercontent = "Enter a text here." <- a footer (quotes are nice)

    hideCredits = false
    hideCopyright = false

    rtl = false

    # Custom CSS
    custom_css = []

[[params.social]]
    name = "Github"
    icon = "fab fa-github fa-2x"
    weight = 1
    url = "https://github.com/johndoe/" <- Change to your Github profile url
[[params.social]]
    name = "Gitlab"
    icon = "fab fa-gitlab fa-2x"
    weight = 2
    url = "https://gitlab.com/johndoe/" <- Change to your GitLab profile url
[[params.social]]
    name = "Twitter"
    icon = "fab fa-twitter fa-2x"
    weight = 3
    url = "https://twitter.com/johndoe/" <- Change to your twitter profile url
[[params.social]]
    name = "LinkedIn"
    icon = "fab fa-linkedin fa-2x"
    weight = 4
    url = "https://www.linkedin.com/in/johndoe/" <- Change to your Linkedin profile url
[[params.social]]
    name = "Medium"
    icon = "fab fa-medium fa-2x"
    weight = 5
    url = "https://medium.com/@johndoe" <- Change to your medium profile url

[[menu.main]]
    name = "Blog"
    weight = 1
    url  = "/posts/"
[[menu.main]]
    name = "About"
    weight = 2
    url = "/about/"
[[menu.main]]
    name = "Projects"
    weight = 3
    url = "/projects/"
[[menu.main]]
    name = "Contact me"
    weight = 5
    url = "/contact/"

[languages]
    [languages.en]
        languagename = "English" # The language name to be displayed in the selector.
        title = "John Doe"

        # You can configure the theme parameter for each language.
        [languages.en.params]
        author = "John Doe" <- Your name
        info = "Full Stack DevOps and Magician" <- What you do
        description = "John Doe's personal website" <- quick description
        keywords = "blog,developer,personal"

        [languages.en.menu] # It is possible to change the menu too.

        [[languages.en.menu.main]]
        name = "About"
        weight = 1.0
        url = "/about/"

        [[languages.en.menu.main]]
        name = "Blog"
        weight = 2.0
        url = "/posts/"

        [[languages.en.menu.main]]
        name = "Projects"
        weight = 3
        url = "/projects/"
        [[languages.en.menu.main]]
        name = "Contact me"
        weight = 5
        url = "/contact/"

-----------------------------delete below (unless you know polish) ----------------
    [languages.pl]
        languagename = "Polski"
        title = "John Doe po polsku"

        [languages.pl.params]
            author = "John Doe"
            description = "Strona domowa John'a Doe"
            keywords = "blog,developer,strona domowa"
            info = "Full Stack DevOps i Magik"

        [languages.pl.menu]

            [[languages.pl.menu.main]]
            name = "O mnie"
            weight = 1.0
            url = "/pl/about/"

            [[languages.pl.menu.main]]
            name = "Blog"
            weight = 2.0
            url = "/pl/posts/"

            [[languages.pl.menu.main]]
            name = "projektowanie"
            weight = 3
            url = "/projektowanie/"
            [[languages.pl.menu.main]]
            name = "kontakt"
            weight = 5
            url = "/kontakt/"
------------------------------------------------------------------------ 
```

Enter fullscreen mode Exit fullscreen mode

保存包含您的更改的文件。现在转到您的终端并转到目录`yoursitename`

## 本地托管您的 Hugo 网站

现在你已经对网站做了基本的修改，是时候在本地托管你的网站了！这可以让你在把你的网站放到网上给人们看之前，先看看它是什么样子的。

**确保你在`yoursitename`** 目录中，并键入

```
hugo server 
```

Enter fullscreen mode Exit fullscreen mode

阅读那里的说明，并将本地主机 url 粘贴到您的浏览器中！

转眼间。你有自己的网站！恭喜你！所有的链接应该工作，并在其中有`exampleSite`职位和信息！如果他们不这样做，你需要检查并确保你把所有东西都正确地移到了`exampleSite`目录之外。

现在你有了一个很棒的网站，转到`about.md`文件，写下你自己，然后在“关于”页面刷新你的浏览器。您应该会看到网站在您的浏览器中自动更新！这对于原型文章来说非常方便！稍微摆弄一下文件，开始理解目录中的内容是如何组织的，以及它们在浏览器网站上的外观。理解事物是如何布局的，将使以后使用 Hugo 更加直观。

## 创建新内容的基础知识

在你的 hugo 站点上创建一个新文件非常简单。首先，当创建新内容时，您将总是希望位于您的`yoursitename`目录中，然后您将总是使用`hugo new (contentpathhere)`命令。试着用下面的命令
发布一个新帖子

```
hugo new posts/firstpost.md 
```

Enter fullscreen mode Exit fullscreen mode

找到 firstpost.md，用东西编辑一下，然后确保头里的`draft`设置改成了`false`而不是`true`。保存文件，进入浏览器并刷新页面，然后进入博客，砰！你在你的网站上创建了你的第一个帖子！这是在 hugo 网站上发布新帖子的基本流程，当你对 Hugo 的基础知识更加熟悉时，你应该阅读更多关于创建新内容的内容！

## 托管在 GitHub 页面上

现在你已经完成了基本的站点，我们将在 GitHub 上为每个人展示它！GitHub 免费提供这项服务，所以不用担心付钱！

你要做的第一件事是去 github 创建两个资源库

```
1\. A repository with the exact name as your website directory 'yoursitename'
2\. A repository with the name 'yourgithubusername.github.io' 
```

Enter fullscreen mode Exit fullscreen mode

一旦你创建了这些存储库，从存储库中获取 SSH 密钥，其中包含你的网站的确切名称，然后转到你的站点目录`yoursitename`并键入`git init`，然后键入`git add remote`并粘贴 SSH 密钥。然后输入`git pull origin master`。

现在`cd ..`转到`Sites`目录，键入`git clone`，从‘yourgithub username . github . io’添加 SSH 密钥。一旦克隆了`cd yoursitename`，输入`hugo -d ../yourgithubusername.github.io`。然后`cd ../yourgithubusername.github.io`然后输入

```
git add .
git commit -m "your message"
git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

然后输入`cd ../yoursitename`和

```
git add .
git commit -m "your message"
git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

现在转到 GitHub，转到“yourgithubusername.github.io”存储库，点击设置，滚动直到你看到你的网站的链接，上面写着[http://yourgitubusername . GitHub . io](http://yourgitubusername.github.io)。你现在应该可以在这里看到你的网站了！恭喜你。你现在在 GitHub 页面上服务一个网站！现在你可以把你的朋友、家人和潜在雇主引到这个地址，向他们展示你发布和做的所有酷事！

## 结束

我希望这对那些希望快速创建他们的第一个网站的人有所帮助，请随时提出修改建议或问题！我会帮忙澄清任何事情！

非常感谢你阅读我的第一篇文章！