# Jekyll 入门

> 原文：<https://dev.to/jaybeekeeper/getting-started-with-jekyll-3nf9>

这个周末我开始和哲基尔一起工作；它对初学者非常友好，本质上只是一个构建静态网站和博客的工具。它是基于 ruby 构建的，可以运行自己的开发环境，所以很容易做出更改并快速看到它们。

我认为这是一个足够好的起点，因为迄今为止，我第一次使用 Ruby web 框架是 Rails——这要轻量得多——以至于与 Rails 进行类比实际上毫无意义。既然这么简单，就没有模型和数据库管理。本质上，Jekyll 使用 Markdown、HTML、CSS 和模板语言“Liquid”的组合来呈现网站。

设置非常快——让我们假设您的机器上已经准备好使用 Ruby 了。

在终点站，我只是跑

```
$ gem install bundler jekyll
$ jekyll new blog-post-site 
```

其中“博客-文章-网站”只是我正在制作的新目录的名称。这让我受益匪浅:

```
 Bundler: Fetching gem metadata from https://rubygems.org/...........
  Bundler: Fetching gem metadata from https://rubygems.org/.
  Bundler: Resolving dependencies...
  Bundler: Using public_suffix 3.0.2
  Bundler: Using addressable 2.5.2
  Bundler: Using bundler 1.16.2
  Bundler: Using colorator 1.1.0
  Bundler: Using concurrent-ruby 1.0.5
  Bundler: Using eventmachine 1.2.7
  Bundler: Using http_parser.rb 0.6.0
  Bundler: Using em-websocket 0.5.1
  Bundler: Using ffi 1.9.25
  Bundler: Using forwardable-extended 2.6.0
  Bundler: Using i18n 0.9.5
  Bundler: Using rb-fsevent 0.10.3
  Bundler: Using rb-inotify 0.9.10
  Bundler: Using sass-listen 4.0.0
  Bundler: Using sass 3.5.6
  Bundler: Using jekyll-sass-converter 1.5.2
  Bundler: Using ruby_dep 1.5.0
  Bundler: Using listen 3.1.5
  Bundler: Using jekyll-watch 2.0.0
  Bundler: Using kramdown 1.17.0
  Bundler: Using liquid 4.0.0
  Bundler: Using mercenary 0.3.6
  Bundler: Using pathutil 0.16.1
  Bundler: Using rouge 3.1.1
  Bundler: Using safe_yaml 1.0.4
  Bundler: Using jekyll 3.8.3
  Bundler: Using jekyll-feed 0.10.0
  Bundler: Using jekyll-seo-tag 2.5.0
  Bundler: Using minima 2.5.0
  Bundler: Bundle complete! 4 Gemfile dependencies, 29 gems now installed.
  Bundler: Use `bundle info [gemname]` to see where a bundled gem is installed.
New jekyll site installed in /Users/jarretbryan/Development/blog-post-site. 
```

所以一旦我进入创建的目录，静态站点的绝对基础已经为我创建好了。基本文件目录如下所示:

[![Basic File Directory](img/df356dcc5af6f37793895d619b7c299c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ilXn9oOR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zbnup0bpnndg0luqguqn.png)

这里最重要的文件是 _config.yml，它是用 YAML (YAML 不是标记语言，是有史以来最讨厌的递归初始化)编写的，用于管理整个网站或博客的设置。YAML 只是一种面向数据的语言(不像 JSON ),可以管理 web 应用程序的数据结构——但它应该是对人类友好的。理论上，你绝对不需要为网站的其他部分使用 HTML 或 CSS 这些都可以通过 this config 文件来管理。

默认的配置文件如下: [![](img/fef39be98d6f750f089349d9d2b7d7c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A8Q8qTU8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8y0bj3y7mdsgv02s0suq.png)

如果我跑了

```
$jekyll serve --livereload 
```

这将把我的服务器设置在 localhost:4000。

`$jekyll serve`

设置服务器，每次对非配置文件进行编辑时，livereload 标志都会刷新它。而在本地主机上的页面本身看起来有点像这样:
[![jekyll minima](img/4df5f2d4e5e436945a3d476ce1dd29c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hNXca2Wo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cqwgu7jz83drf9bh1pzb.png)

而且那还是一个基本的静态网站！真的就这么简单。任何内容都可以通过配置文件进行修改。

文件结构允许你用静态 html 布局为博客文章或其他页面构建站点。“帖子”默认为 markdown 语言，这使得写博客变得异常容易(就像这里的 dev.to！).每篇博文都可以是另一个降价页面。由于该网站是如此的轻量级，它可以完全托管在 GitHub 页面上！我相信它实际上是 GitHub 页面背后的引擎

现在，在 _config.yml 中指定的默认主题被称为“minima ”,虽然它完成了工作，但它并不那么有趣。但是并不缺少主题(免费或付费)，由其他设计者和开发者用默认的文件结构和布局放在一起。我做了一个快速粗略的搜索，找到了一些，它们通常都很容易安装(只要它们有很好的文档)。通常情况下，会提供一行额外的代码添加到 gemfile 中，然后只需更改 YAML 配置文件中的主题。然后运行$bundle，突然你的站点就转型了！

下面是我创建的一些网站的例子，在使用主题的过程中我很少接触到 HTML:
[Jekyll Material 主题](https://github.com/jameshamann/jekyll-material-theme)
[![](img/9cea40566d3f768b1b4222ee08c6880b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TvmtW83R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/42413iicdmvxkz7ep06p.png) 
[瑞士主题](https://github.com/broccolini/swiss)
[![](img/671502c4966fcf861f8918259f3947d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0eKYCilX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p2j4d7plvvpqatdfwysk.png)

我需要深入了解的定制空间很大。但是 Jekyll 是如此的轻量级和灵活，以至于它真的很容易构建出你所需要的基础，而不会陷入 web 应用程序架构中。