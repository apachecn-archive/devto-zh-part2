# 如何在你的 rails 应用附近建立一个简单的 jekyll 博客？

> 原文：<https://dev.to/marvindanig/-how-to-set-up-a-simple-jekyll-blog-near-your-rails-app-5c3o>

所以你的原型已经准备好上线了，你需要一个博客来向全世界宣布你的新贵。

太好了！，但是你是怎么做到的呢？

… \ *(ツ)* /

建立一个博客是如此容易，以至于你不想错过你的公司在网络上所有的“领域权威”。错过了搜索引擎优化果汁来与子目录一样:`/domain.com/blog`？

在我看来，如果你正在创业，努力一点，建立自己的博客是很重要的。博客是搜索之神看待你的方式，至少，而不是媒介。

下面的指南将帮助你在你的 rails 应用程序旁边建立一个简单的 Jekyll 博客，但是这个过程本质上是一样的，不管你在哪个栈(node、python、java 或其他)上。

> 这篇文章假设你像我一样喜欢 *ruby_on_rails* 并且有一个应用程序(startup？)活在自己的`domain.com`。现在，你需要在`domain.com`的`/blog`网址上开一个博客，用你的公告震撼世界。
> 
> 该死。，你现在处于正确的时间正确的地点，所以继续读下去…
> 
> 当然，我们上周也用同样的技术发布了[公告](https://bubblin.io/blog/announcement)！

* * *

我们想为[泡泡超级本](https://bubblin.io)开一个博客，发布新功能和更新。我们在`rails 5.2`上，希望我们的博客和主要产品一样位于同一个*顶级域名*([https://bubblin.io/blog](https://bubblin.io/blog)vs . blog . bubblin . io)。为什么？因为 SEO，如果你之前错过了。

建立博客有几种方法，还有相当多的开源选项(wordpress/ghost 等)。)之外，但为了简单起见，我们决定使用 Jekyll 和 *markdown* 。

> Jekyll 和所有 rails 应用一样都是基于`rack`的，我真的很喜欢用 markdown 编写。

对于门外汉来说，Jekyll 是一个简单的博客感知生成器，所以我们需要做的就是通过`public`目录在线发布它和我们的 rails 应用程序。网络服务器会处理剩下的事情。请注意，博客不必与 rails 应用程序本身耦合，它只需像兄弟姐妹一样坐在附近，以类似于静态上传的方式工作。

让我们先来看看设置。

# 设置

假设您的启动程序(或 rails 应用程序)将被称为`domain.com`，在您的终端中:

```
 $ mkdir ~/projects/domain.com
$ cd ~/projects/domain.com
$ rails new domain.com          // your app may already exist. 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了 rails 应用程序(*~/project/domain . com/domain . com*)，让我们开始准备我们的博客吧。在写这篇文章的时候，jekyll 要求你的系统至少安装了 Ruby 2.2.5，但是最新的说明，请查看 Jekyll 安装[指南](https://jekyllrb.com/docs/installation/)。

```
$ cd ~/projects/domain.com      // or cd .. if you're inside the rails app.
$ gem install bundler jekyll
$ jekyll new domain.blog
$ cd domain.blog && bundle install 
```

Enter fullscreen mode Exit fullscreen mode

继续把你的博客提交给它自己的新回购:

```
$ git init && git add . -A
$ git commit -m "Setting up a jekyll blog on the side…"
$ git remote add origin git@github.com:username/domain.blog.git
$ git push -u origin master 
```

Enter fullscreen mode Exit fullscreen mode

一旦 Jekyll 完成，你将拥有位于*~/project/domain . com/domain . blog*的博客和位于*~/project/domain . com/domain . com*的 Rails 应用，就像这样:

```
➜ marvindanig-@bubblin in ~/projects/domain.com: $ ls

domain.blog domain.com 
```

Enter fullscreen mode Exit fullscreen mode

# 配置

这是我们的 jekyll 博客的样子:

```
➜  ls ~/project/domain.com/public/domain.blog
404.html      Gemfile.lock  _posts        bin           index.md
Gemfile       _config.yml   _site         contact.md 
```

Enter fullscreen mode Exit fullscreen mode

修改 jekyll 实例的`_config.yml`上的`baseurl`选项，使其指向`/blog`。

```
baseurl: "/blog"            // the subpath of your site, e.g. /blog, /docs 
```

Enter fullscreen mode Exit fullscreen mode

这对于正确加载和链接资产非常重要。为了确保一切正常工作，启动 Jekyll 服务器并在浏览器上预览博客。您也可以将主题应用到您的博客，以匹配您站点的布局。我们定制了我们的 Jekyll 博客，并以一个*宝石*和一个[指南](https://github.com/bookiza/bubblin-jekyll)的形式开源了我们的主题，以帮助你快速地接触:

[https://github . com/bookiza/bubblin-Jekyll](https://github.com/bookiza/bubblin-jekyll)

(简单地叉一下，替换掉 logos 和链接就搞定了！)

由于我们将在 [Linode](https://www.linode.com/?r=98af25a1733481db3f0afe644f31fd2faff8fecd) 上托管 Bubblin，我想确保`public/`目录被正确提交。如果需要提交目录，请从`.gitignore`文件中删除“public/”。我们下一步要做的只是在我们的部署器菜谱上添加一个`shared`目录，告诉 rails 对我们博客的请求将去往何处。在此基础上，我们可以构建 Jekyll 站点并将其同步到服务器上的博客目录中。很简单。

我们使用 Capistrano 进行构建部署，所以我在我们的部署者菜谱上添加了“blog/”作为`linked_directory`:

```
➜ $ cd ~/project/domain.com/domain.com/config/        // In your rails/node/python app
➜ $ vi ~/project/domain.com/domain.com/config/deploy.rb 
```

Enter fullscreen mode Exit fullscreen mode

将`public/blog`添加到以下行:

```
set :linked_dirs, %w{public/blog log tmp/pids tmp/cache tmp/sockets vendor/bundle public/system} 
```

Enter fullscreen mode Exit fullscreen mode

用`\wq!`退出 vim。

# 发布

为了发布，我们将使用一个两行的部署脚本来构建 jekyll 并将其同步到 Rails 应用程序的公共目录中。如果你没有安装 rsync，你可以使用`scp`来代替，或者你需要安装一个带有你喜欢的包管理器的工具。因为我用的是 mac，所以我用的是自制软件。

```
$ cd ~/project/domain.com/domain.blog/ && mkdir bin     // Create a `bin` dir here.
$ cd bin && touch deploy                // Note there is no dotfile extension on this script
$ vi deploy 
```

Enter fullscreen mode Exit fullscreen mode

添加以下代码行:

```
#! /bin/sh

jekyll build
rsync -va _site/ ../domain.com/public/domain.blog
# rsync -va _site/ deployer@domain.com:/var/www/domain.com/shared/public/blog   // Uncomment for production. 
```

Enter fullscreen mode Exit fullscreen mode

用`\wq!`退出 vim。让它可执行也是一个好主意:

```
chmod +x ~/project/domain.com/domain.blog/bin/deploy 
```

Enter fullscreen mode Exit fullscreen mode

在你博客的根目录下执行`$ . bin/deploy`，你会看到 rsync 运行并将 Jekyll 构建放入你的 rails 应用的`public/`目录。

现在你可以独立地使用应用程序和博客，而不会混合依赖。

这就是如何在你的 rails 应用程序附近设置一个简单的 jekyll 博客。祝你的创业一帆风顺！

* * *

你知道[泡泡林](https://bubblin.io)之于书籍，就像哲基尔之于博客吗？

这篇文章的压缩版出现在 Bubblin 博客上