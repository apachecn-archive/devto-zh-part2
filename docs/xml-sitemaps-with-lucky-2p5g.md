# 带有 Lucky 的 XML 站点地图

> 原文：<https://dev.to/jwoertink/xml-sitemaps-with-lucky-2p5g>

当涉及到建立一个网站，搜索引擎优化是非常重要的。你要确保当人们搜索东西时，你的网站足够相关，可以显示给用户。SEO 真的很神奇，但是我们可以添加一些东西来帮助解决这个问题。

我将讨论为你的[幸运的](https://luckyframework.org/)应用程序生成站点地图，但是，这大部分也可以应用于 crystal 中的其他框架。

## 网站地图

站点地图是位于你的网站的公共根目录中的 XML 文档。这些使得搜索引擎能够绘制并理解你网站的整个结构。你可以让搜索引擎使用爬虫一页一页地搜索，但是这可能会花费很多时间，并且可能会错过一些页面。有了 XML 站点地图，搜索引擎就知道页面的所有 URL，甚至知道关于这些页面的一些其他相关元数据。它可以直接进入页面，保存它，并在以后引用它。

这些文件实际上有一个标准格式。你可以查看[Sitemaps.org](https://www.sitemaps.org/)了解更多关于这些是如何构建的信息，以及还有哪些其他选项可用。这样做的一个问题是，这个标准比他们的网站显示的信息要多得多。更多信息可以在[谷歌网站站长](https://developers.google.com/webmasters/googleforwebmasters/)上找到。这包括如何在你的网站地图中使用视频和图片。

我们需要做的第一件事是将[站点映射器](https://github.com/jwoertink/sitemapper)碎片包含到我们的幸运项目中。

```
dependencies:
  lucky:
    github: luckyframework/lucky
    branch: master
  sitemapper:
    github: jwoertink/sitemapper
    version: ~> 0.2 
```

Enter fullscreen mode Exit fullscreen mode

一旦进入并开始跑步，你就准备好了！

当生成站点地图时，这是一项发生在站点正常操作之外的任务。它不需要用户交互来生成你的站点地图；这是一项管理任务。您还会不断地添加和/或删除新页面，所以静态地这样做对大多数人来说并不可行。我们会想要使用[幸运任务](https://luckyframework.org/guides/tasks/)。

让我们创建一个名为`generate_sitemaps.cr`的新任务。该文件将保存在`your_app_root/tasks/generate_sitemaps.cr`中。

现在你要把这个放到那个文件里。这是任务的最低要求(以及站点映射器)

```
require "lucky_cli"
require "sitemapper"

class GenerateSitemaps < LuckyCli::Task
  banner "Generates the sitemaps"

  def call
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在疯狂之前，请确保您可以运行这个程序，并且一切都可以编译。从我们的 app 目录，如果我们只是运行`lucky generate_sitemaps`，它什么都不会做！在这一点上我们什么也不想要。任何像编译错误之类的东西，都可以让我们在开始获取大量实际代码之前捕捉到问题。

下一步将是开始编写代码。Sitemapper 的好处在于，如果你需要为不同的站点生成一堆不同的站点地图，比如说一个多租户类型的站点，那么你就有这个能力。我将从单个域开始，然后展示一个如何实现多个域的快速示例。

### 网站地图设置

我们将把我们的站点地图代码添加到任务中的`call`方法中。

```
def call
  sitemaps = Sitemapper.build(host: "https://mycoolapp.io", max_urls: 500, use_index: true) do
    add("/", lastmod: Time.now, priority: 1.0)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

好吧，因为这里发生了很多事情，让我来分解一下。

1.  `host` -该选项用于指定您的域名。在网站地图中，所有的 URL 都是绝对的，这意味着你的根路径看起来像是[https://mycoolapp.io/](https://mycoolapp.io/)
2.  `max_urls`——根据官方规范，你的 sitemap“必须有不超过 50000 个 URL，并且必须不大于 50MB”。默认情况下，Sitemapper 将您设置为每个站点地图 500 个，但是您可以设置得更高。
3.  `use_index` -默认情况下这是假的，但是如果你知道你有超过`max_urls`个页面，那么你需要将它设置为`true`。

### 关于指数

当你为你的每个用户考虑一个页面，或者你的站点是关于什么的时候，一个站点有大量的页面是很常见的。假设你有用户资料，你有 20，000 个用户，那么你将有超过 20，000 个页面，包括你的主页，隐私政策和条款页面，一个关于页面？由于这些信息很容易变得巨大，我们有能力为同一个域生成多个站点地图。它们看起来像`mycoolapp.io/sitemap1.xml`、`mycoolapp.io/sitemap2.xml`等等...这让搜索引擎看到你有很多页面，同时保持每个页面的大小较低。然而，搜索引擎不知道这些网站地图叫什么。他们唯一寻找的是`/sitemap.xml`。所以我们在这种情况下所做的是使用我们的`/sitemap.xml`文件作为指向其他文件所在位置的迷你站点地图的索引。你可以点击这里阅读更多关于[网站地图索引的内容。](https://www.sitemaps.org/protocol.html#index)

### 回码

接下来我们有了这条线`add("/", lastmod: Time.now, priority: 1.0)`。这是添加在我们的网站(我们的主页)的根路径。然后我们使用`lastmod`告诉站点地图我们最后一次修改页面的时间。这是一个`Time`的例子。最后是`priority`。这是一个介于 0.0 和 1.0 之间的浮点数，显示了相对于站点地图中其他 URL 的优先级。这个`add`方法接受 sitemap 协议中可用的任何选项，但是所有必需的选项都是为您内置的。

让我们来看看我们的用户资料:

```
def call
  # Select all users with a public profile, and order by their ID in an ASC order
  users =  User::BaseQuery.new.profile_public(true).id.asc_order
  sitemaps = Sitemapper.build(host: "https://mycoolapp.io", max_urls: 500, use_index: true) do
    add("/", lastmod: Time.now, priority: 1.0)

    users.each do |user|
      # we could use a messy "/profiles/#{user.username}", but this is nicer
      path = Users::Show.path(slug: user.username)
      add(path, lastmod: user.updated_at, priority: 0.6)
    end
  end

  # finalize the sitemaps here
end 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，你应该有足够的信息开始添加更多的页面，但我想看看更多的一套。让我们假设我们的用户可以上传一些视频到他们的个人资料页面，这些视频是分页的。这增加了一些复杂性，但并不可怕。

```
users.each do |user|
  user_path = Users::Show.path(slug: user.username)
  add(user_path, lastmod: user.updated_at, priority: 0.6)

  # get the videos for this user with most recent upload first
  videos = Video::BaseQuery.new.by_user(user.id).uploaded_at.desc_order

  # Add pagination for /profiles/jeremy?page=XX
  total_rows = videos.count
  limit = 12 # 12 videos per page
  total_pages = (total_rows / limit.to_f).ceil
  # skip page one because we added that already
  (2..total_pages).to_a.each do |page|
    add("#{user_path}?page=#{page}", lastmod: user.updated_at, priority: 0.4)
  end

  # Now we add the individual video pages
  videos.each do |video|
    # There's a ton of options here
    map = Sitemapper::VideoMap.new(thumbnail_loc: video.thumbnail_url, title: video.title, description: video.description, content_loc: video.source_url, publication_date: video.released_at)
    video_path = Videos::Show.path(user_slug: user.username, video_slug: video.cached_slug)
    add(video_path, lastmod: video.updated_at, priority: 0.7)
  end

end 
```

Enter fullscreen mode Exit fullscreen mode

*注意:如果你在上面的代码中看到选项周围有引号，那是 Dev.to* 中的错误

你可以在[源](https://github.com/jwoertink/sitemapper/blob/master/src/sitemapper/video_map.cr#L3)或[谷歌](https://developers.google.com/webmasters/videosearch/sitemaps)上阅读更多视频选项。

大概就是这样。其余的将真正取决于你的网站。我唯一想展示的是，一个使用多租户站点的小例子。

```
def call
  sites = Site::BaseQuery.new.active(true)

  sites.each do |site|
    sitemaps = Sitemapper.build(host: "https://#{site.host}", max_urls: 500, use_index: true) do
      # add site specific routes here
    end

    # finalize the sitemaps here
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 敲定网站地图

Sitemapper 目前将所有生成的 XML 放入内存中的巨型字符串中。这可能不是“最佳”的，但是我的应用程序生成了 400 多个站点地图，到目前为止还不错...

这样做的原因是，并不是每个人都可以将一个 XML 文件写到他们的公共文件夹中就完事了。如果你在 [Heroku](https://www.heroku.com/) 上托管，或者你正在使用 [Docker](https://www.docker.com/) ，你可能想要使用类似 [S3](https://aws.amazon.com/s3/) 或其他什么东西来存储实际的站点地图。如果您可以在本地编写 XML，那么您可以使用内置函数来完成这项工作。

```
# this is your_app_root_path/public/sitemaps
# it will generate the folder for you if it needs to
Sitemapper.store(sitemaps, "public/sitemaps/") 
```

Enter fullscreen mode Exit fullscreen mode

最终应该会有一些额外的选项来存储到 S3 或其他地方，但是如果你需要的话，你需要使用`sitemaps`变量，并把数据发送到你需要的任何地方。