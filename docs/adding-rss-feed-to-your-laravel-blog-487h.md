# 将 RSS 源添加到您的 Laravel 博客

> 原文：<https://dev.to/adi/adding-rss-feed-to-your-laravel-blog-487h>

又见面了，

你可能已经知道了，[我和 Laravel 在一天内重建了我的网站](https://simplestweb.in/blog/building-a-laravel-blog-in-a-day?utm_campaign=laravel-feed-article)。虽然网站在一天的工作中就成形了，但我仍然有许多重要的功能要包括，RSS 提要、博客帖子评论、结构化数据等等。我想解释一下我实现 RSS 提要的解决方案。这可能是一个简单的解决方案，但关键是它像魔咒一样有效。因此，让我们开始为您的站点构建一个 RSS 提要。

### TLDR；

以下是我解决方案的要点。

1.  在`resources`文件夹中创建一个视图文件，该文件将具有可动态填充的 xml 结构
2.  在`routes/console.php`文件中创建一个名为`generate:feed`的 Artisan 命令
3.  这个命令获取所有的文章并将它们发送到 RSS 视图。然后将返回的 xml 格式数据写入到`public`文件夹中名为`rss.xml`的文件中

我确信你没有理解上面的说明，所以让我更详细地解释一下。

#### RSS 视图

在你的`resources/views`文件夹中创建一个文件，我称之为`rss.blade.php`。粘贴如下所示的代码。我会边走边解释。

```
{!! '<'.'?'.'xml version="1.0" encoding="UTF-8" ?>' !!}
<rss version="2.0" xmlns:atom="http://www.w3.org/2005/Atom" xmlns:content="http://purl.org/rss/1.0/modules/content/" xmlns:media="http://search.yahoo.com/mrss/">
  <channel>
    {{ $site['name'] }}
    <link>{{ $site['url'] }}</link>
    <description><![CDATA[{{ $site['description'] }}]]></description>
    <atom:link href="{{ $site['url'] }}" rel="self" type="application/rss+xml" />
    <language>{{ $site['language'] }}</language>
    <lastBuildDate>{{ $site['lastBuildDate'] }}</lastBuildDate>

    @foreach($posts as $post)
    <item>
      <![CDATA[{!! $post->title !!}]]>
      <link>{{ route('pages.post', $post->slug) }}</link>
      <guid isPermaLink="true">{{ route('pages.post', $post->slug) }}</guid>
      <description><![CDATA[{!! $post->description !!}]]></description>
      <content:encoded><![CDATA[{!! $post->content !!}]]></content:encoded>
      <dc:creator xmlns:dc="http://purl.org/dc/elements/1.1/">{{ $post->author }}</dc:creator>
      <pubDate>{{ $post->created_at->format(DateTime::RSS) }}</pubDate>
    </item>
    @endforeach
  </channel>
</rss> 
```

您可能会注意到，我们向这个视图传递了两个变量，`$site`和`$posts`。首先包含一些关于你的网站的信息。第二个包含了您希望包含在这个提要中的一系列文章。

接下来，我们将创建控制逻辑的 Artisan 命令。

#### 工匠命令

你可能会问为什么把所有的逻辑都写在一个命令里而不是一个控制器里。我选择这种方法有几个原因。

1.  我可能想从终端调用这个命令
2.  我也可以从控制器调用它
3.  因为这个命令在公共文件夹中生成一个文件，所以请求不需要通过 Laravel。

下面是我在`routes/console.php`文件中的代码。

```
<?php
use Illuminate\Support\Facades\Storage;
use App\Models\Post;
Artisan::command('generate:feed', function () {
  $this->info("Generating RSS Feed");
  // It is important that you sort by the latest post
  $posts = Post::where('published', true)->latest()->get();

  $site = [
    'name' => 'YOUR SITE NAME', // Simplest Web
    'url' => 'YOUR SITE URL', // Link to your rss.xml. eg. https://simplestweb.in/rss.xml
    'description' => 'YOUR SITE DESCRIPTION',
    'language' => 'YOUR SITE LANGUAGE', // eg. en, en-IN, jp
    'lastBuildDate' => $posts[0]->created_at->format(DateTime::RSS), // This generates the latest posts date in RSS compatible format
  ];
  // Passes posts and site data into the rss.blade.view, out comes text in rss format
  $rssFileContents = view('rss', compact('posts', 'site'));
  // Saves the generated rss feed into a file called rss.xml in the public folder, this works only
  Storage::disk('local')->put('rss.xml', $rssFileContents);
  $this->info("Completed");
}); 
```

这是非常基本的东西，你应该能够识别代码做什么。首先，我们创建一个`Artisan`命令。然后，我们获取按最近排序的所有帖子。接下来，我们用我们的站点细节创建一个数组。然后我们将所有这些数据传递给我们的`rss.blade.php`视图文件，它返回我们需要的格式化的 xml。最后，我们把它写到公共文件夹中的`rss.xml`文件中。这并不难，是吗？。

#### 更新布局文件

将以下代码添加到所有页面的`head`部分。

```
<!-- Change the title and href to your site -->
<link rel="alternate" type="application/rss+xml" title="Simplest Web" href="https://simplestweb.in/rss.xml" /> 
```

上面的代码让 rss 阅读器知道这个站点中有一个 RSS 提要，并将它指向 url。

### 大概就是这样

我希望你理解我为 Laravel 站点实现 RSS 提要的解决方案。你可以在此订阅源中添加更多信息，你可以在下面找到更多资源。如果你喜欢这个教程，请告诉我。我会写更多。如果你在教程的任何地方卡住了，让我知道，我会帮助你。

[https://validator.w3.org/feed/](https://validator.w3.org/feed/)
T3】https://www.w3schools.com/xml/xml_rss.asp

**谢谢**
更多关于我的信息请访问 [simplestweb.in](https://simplestweb.in/?utm_campaign=laravel-feed-article)