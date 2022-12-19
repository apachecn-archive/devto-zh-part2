# 用 Jekyll-Algolia 搜索你的静态网站

> 原文：<https://dev.to/adrienjoly/make-your-static-site-searchable-with-jekyll-algolia-edh>

大约两周前，我发表了一篇名为“[如何在线维护音乐专辑收藏，使用 Jekyll 和 Github 页面](https://dev.to/adrienjoly/how-to-maintain-a-collection-of-music-albums-online-using-jekyll-and-github-pages-3hd6)”的教程。

在该教程结束时，我们能够从 YAML 的专辑列表中生成一个 100%静态的、100%免费托管的、外观漂亮的网站，这要感谢 Jekyll 模板。它看起来像这样:

[![screenshot of album collection made with Jekyll and github pages](../Images/26220f9ce4fb869745b9af1eeea0450b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--N7HYvYFH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gdh6mg1af0nyx3o3p809.png)

我们看到维护相册集非常容易和方便:我们只需编辑`_data/albums.yaml`，它几乎是纯文本格式。

你可以在 github 仓库中找到我们生产的源代码:[github.com/adrienjoly/jekyll-tutorial](https://github.com/adrienjoly/jekyll-tutorial)。

既然我们能够编辑列表并在 HTML 中呈现它，让我们使我们的相册集**可搜索**！

> 注意:本教程是为有经验的开发人员和初学者编写的，也涵盖了意外出错的部分。我的意图是也分享我解决问题的过程，以便初学者可以学习如何解决自己的问题，沿着这条路走下去。感谢您的理解！🤓

# 提出解决方案

加入 Algolia 后，我发现我的一个同事发布了一个插件，使 Jekyll 网站可以被搜索到: [Jekyll-Algolia](https://github.com/algolia/jekyll-algolia) 。(许可证:麻省理工学院)

所以，是的，我肯定对这个有偏见，但我会继续尝试！

# 第一步。集成插件

好了，先说自述文件中提到的前提条件:“*插件至少需要 Jekyll 3.6.0 和 Ruby 2.3.0* ”。

```
$ jekyll -v
jekyll 3.6.2

$ ruby -v
ruby 2.1.4p265 (2014-10-27 revision 48166) [x86_64-darwin14.0]
# ❌ Oops... Let's upgrade ruby!

$ curl -sSL https://get.rvm.io | bash -s stable

# ⚡️ Let's restart the terminal, as suggested by https://stackoverflow.com/a/38194139/592254

$ rvm install 2.3
# (Ended up compiling from its source code, maybe because I'm still on El Capitan?)

$ ruby -v
ruby 2.3.7p456 (2018-03-28 revision 63024) [x86_64-darwin15]
# ✅ We're good to go!

$ rvm use 2.3 --default 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们把插件安装到我们的代码库中:"*把`jekyll-algolia`宝石添加到你的`Gemfile`中，在`:jekyll_plugins`部分*

✋·布洛克:我还没有！

所以，让我们按照 GitHub 的教程建议的相应步骤:[首先，用 Jekyll](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/#step-2-install-jekyll-using-bundler) 在本地设置你的 GitHub Pages 站点。

我在我的项目的根目录下以一个`Gemfile`结束，其内容如下:

```
# Gemfile

source 'https://rubygems.org'
gem 'github-pages', group: :jekyll_plugins

group :jekyll_plugins do
  gem 'jekyll-algolia', '~> 1.0'
end 
```

Enter fullscreen mode Exit fullscreen mode

回到 Jekyll-Algolia 的指令:“*下载所有与`bundle install`* 的依赖关系”。

```
$ bundle install 
```

Enter fullscreen mode Exit fullscreen mode

# 第二步。设置搜索索引

让我们遵循说明的[基本配置](https://github.com/algolia/jekyll-algolia#basic-configuration)部分。

我已经有了一个 algolia.com 的账户，所以我要用那个。您可能需要为自己创建一个。

我们的下一步是在那里创建一个“[应用程序](https://www.algolia.com/manage/applications)”。由于我们的代码库将保持开源，我们可以安全地选择“社区”计划，免费。

[![screenshot of creating a community algolia application](../Images/68180b03c7a6b13411e495521dccf193.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---f9BFtIy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iclpsnb2itdwkgx0x4z4.png)

我将我的应用程序命名为“album-shelf”，因为这是我想要添加搜索的网站的名称。

在选择了一个区域之后(在我的例子中是`EU WEST`，让我们转到“API Keys”选项卡。将您的*应用程序 ID* 复制并粘贴到项目根目录下的一个新的`_config.yml`中，如下所示:

```
# _config.yml

algolia:
  application_id: 'paste_your_application_id_here' 
```

Enter fullscreen mode Exit fullscreen mode

还要复制 *Admin API 键*，这是[运行](https://github.com/algolia/jekyll-algolia#run-it)插件所必需的，就像这样:

```
$ ALGOLIA_API_KEY='paste_your_admin_api_key_here' bundle exec jekyll algolia
[✗ Error] No index name defined 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:您的管理 API 密钥可用于读取、更新和删除您的 Algolia 应用= >请确保您只为自己保留它！(即，将其存储到您的公共 GitHub 存储库中😅)

糟糕，看起来我需要在`_config.yml`文件中添加我的搜索索引的名称:

```
# _config.yml

algolia:
  application_id: 'paste_your_application_id_here'
  index_name: 'albums' 
```

Enter fullscreen mode Exit fullscreen mode

我选择了“相册”这个名字，因为这是我希望访问者能够在我的网站上搜索到的。键入任何你喜欢的名字，在那里！

让我们再次尝试运行插件:

```
$ ALGOLIA_API_KEY='paste_your_admin_api_key_here' bundle exec jekyll algolia
[✗ Error] No records found

Make sure you did not exclude too many files from indexing using the
`files_to_exclude` option. You are currently excluding the following files:
    index.html, index.markdown, index.mkdown, index.mkdn, index.mkd, index.md 
```

Enter fullscreen mode Exit fullscreen mode

该死的！

好了，让我们删除任何可能的排除，完成`_config.yml`像这样:

```
# _config.yml

algolia:
  application_id: 'paste_your_application_id_here'
  index_name: 'albums'
  files_to_exclude: [] 
```

Enter fullscreen mode Exit fullscreen mode

...并尝试再次运行:

```
$ ALGOLIA_API_KEY='paste_your_admin_api_key_here' bundle exec jekyll algolia
Configuration file: /Users/adrienjoly/Dev/adrienjoly/jekyll-tutorial/_config.yml
Processing site...
Rendering to HTML (100%) |======================================================================|
Extracting records (100%) |=====================================================================|
Updating settings of index albums
Getting list of existing records
Updating records in index albums...
Records to delete: 0
Records to add: 4
✔ Indexing complete 
```

Enter fullscreen mode Exit fullscreen mode

成功了！🙌

让我们回到 Algolia 仪表盘，看看我们的搜索索引是什么样的:

[![screenshot of algolia dashboard with index of albums](../Images/3c30e21ec2e551893c4cc9421259a782.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qInnDap9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9mpl1mw32vdmxaw0mgzr.png)

好消息是仪表盘显示“血糖性 magik”，这是我的网站上列出的专辑之一！

坏消息是它包含 4 张唱片，而我的收藏目前包含 2 张专辑。索引过程中一定发生了错误...🤔

感谢我之前在尝试运行`bundle exec jekyll algolia`时得到的一条错误消息(为了简单起见，我没有完全包括在上面)，我发现可以指定应该索引哪些 HTML 元素:`nodes_to_index`属性。

所以让我们告诉 Jekyll-Algolia 通过更新我们的`_config.yml`文件:
来索引我们的`article`元素(正如我们在[之前的教程](https://dev.to/adrienjoly/how-to-maintain-a-collection-of-music-albums-online-using-jekyll-and-github-pages-3hd6)中编写的 Jekyll 模板中所指定的)

```
# _config.yml

algolia:
  application_id: 'paste_your_application_id_here'
  index_name: 'albums'
  files_to_exclude: []
  nodes_to_index: 'article' 
```

Enter fullscreen mode Exit fullscreen mode

再次运行`bundle exec jekyll algolia`命令并刷新 Algolia 仪表盘后，我们的搜索索引中现在有 2 张专辑了！✊

继续之前的快速测试:让我们从 Algolia 的仪表板上搜索我的一张专辑:

[![testing album search from algolia dashboard](../Images/5b9ab6db47d351aa2a0f17e1d6b8c2a3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xwmtzNGx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/765336euv5iojk68elaj.png)

✅工作正常！

# 第三步。集成搜索栏

从我们的网站自动填充搜索索引非常酷！但是在我们在它上面显示一个搜索栏之前，它是完全没有用的。这一新步骤超出了 Jekyll-Algolia 的范围:我们现在需要使用 Algolia 的搜索客户端之一进入我们的网站。

## (3.1)导入`instantsearch.js`

让我们前往 Algolia 的文档网站的“构建搜索 UI”部分。我们将使用最简单和最通用的 UI 集成: [InstantSearch.js](https://community.algolia.com/instantsearch.js/) 。

按照[入门](https://community.algolia.com/instantsearch.js/v2/getting-started.html)说明中的建议，让我们将基于 CDN 的样式表和脚本导入到我们的`index.md`文件:

```
<link rel="stylesheet" href="index.css" />

## Albums

{% for album in site.data.albums %}
  <article>
    <a href="{{ album.url }}">
      <img src="{{ album.img }}" alt="{{ album.title }} {{ album.artist }}"/>
      <p>{{ album.title }}</p>
    </a>
    <p>by {{ album.artist }}</p>
    {% if release-date %}
      <span class="release-date">{{ album.release_date | date: "%b %-d, %Y" }}</span>
    {% endif %}
  </article>
{% endfor %}

<!-- algolia search -->

<link rel="stylesheet" type="text/css" href="https://cdn.jsdelivr.net/npm/instantsearch.js@2.8.1/dist/instantsearch.min.css">
<link rel="stylesheet" type="text/css" href="https://cdn.jsdelivr.net/npm/instantsearch.js@2.8.1/dist/instantsearch-theme-algolia.min.css">

<script src="https://cdn.jsdelivr.net/npm/instantsearch.js@2.8.1"></script>

<script>
  /* we are going to add some javascript code here */
</script>

<!-- end of algolia search --> 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢定期测试我没有打破任何东西:

```
$ bundle exec jekyll serve --incremental

  Server address: http://127.0.0.1:4000
  Server running... press ctrl-c to stop. 
```

Enter fullscreen mode Exit fullscreen mode

当我在浏览器(Google Chrome)中打开`http://127.0.0.1:4000`时，我在 JavaScript 控制台中发现以下错误:

[![javascript error when trying to import algolia instantsearch from localhost](../Images/f7c1a578d0f9d5a9210f90c586a0c96f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_xmQhTvl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jct9kwayph0jht9fy7oj.png)

1.  拒绝应用来自“[https://cdn . jsdelivr . net/NPM/instant search . js @ 2 . 8 . 1/dist/instant search . min . CSS](https://cdn.jsdelivr.net/npm/instantsearch.js@2.8.1/dist/instantsearch.min.css)的样式，因为其 MIME 类型(“text/plain”)不是受支持的样式表 MIME 类型，并且启用了严格的 MIME 检查。
2.  拒绝应用来自“[https://cdn . jsdelivr . net/NPM/instant search . js @ 2 . 8 . 1/dist/instant search-theme-algolia . min . CSS](https://cdn.jsdelivr.net/npm/instantsearch.js@2.8.1/dist/instantsearch-theme-algolia.min.css)的样式，因为其 MIME 类型(“text/plain”)不是受支持的样式表 MIME 类型，并且启用了严格的 MIME 检查。
3.  get[https://cdn . jsdelivr . net/NPM/instant search . js @ 2 . 8 . 1](https://cdn.jsdelivr.net/npm/instantsearch.js@2.8.1)404()
4.  拒绝执行来自'[https://cdn.jsdelivr.net/npm/instantsearch.js@2.8.1](https://cdn.jsdelivr.net/npm/instantsearch.js@2.8.1)的脚本，因为其 MIME 类型(' text/plain ')不可执行，并且启用了严格的 MIME 类型检查。
5.  未捕获的引用错误:未定义 instantsearch

让我们暂时忽略 CSS 错误(1，2)，从 JavaScript 错误(3，4，5)开始，因为它们会阻止搜索初始化:

最后一个错误(5)是由于无法加载`instantsearch.js`(3，4)造成的。显然，它无法加载，因为提供的 URL 没有预期的 MIME 类型。可能是当从`localhost`打开网页时，MIME 类型检查被强制执行，就像我现在正在做的。

CSS 文件似乎也会出现同样的问题。

当试图在单独的浏览器标签中打开这些文件时，我知道这不仅仅是 MIME 类型的问题，这些 URL 会返回带有错误消息的页面:“*找不到请求的发布版本 2.8.1。*“！

通过删除这些 URL 中的“@2.8.1”部分，它们似乎工作得很好！在对我的`index.md`文件应用同样的更改并刷新页面后，所有错误都从 JavaScript 控制台消失了！😇

## (3.2)实例化搜索栏并点击

既然`instantsearch.js`已经正确导入，我们需要写一些代码来实例化我们的搜索栏和点击。

首先，我们将在`index.md`中添加`<div>`元素，以便`instantsearch.js`知道在哪里集成搜索栏和点击。

正如即时搜索教程的“[添加搜索框](https://community.algolia.com/instantsearch.js/v2/getting-started.html#add-a-searchbox)”部分所建议的，我们首先在 Jekyll 模板(显示相册)和导入`instantsearch.js` :
的部分之间添加以下 HTML 代码

```
<div id=="search-box">
  <!-- SearchBox widget will appear here -->
</div>

<div id="hits">
  <!-- Hits widget will appear here -->
</div> 
```

Enter fullscreen mode Exit fullscreen mode

还记得我们在`index.md`的`<script>`元素中写的`/* we are going to add some javascript code here */`评论吗？填充这个`<script>`元素的时候到了！

正如 [Instantsearch 的教程](https://community.algolia.com/instantsearch.js/v2/getting-started.html#initialization)中的“初始化”和“显示结果”中所建议的，让我们在那里添加以下 JavaScript 代码:

```
 const search = instantsearch({
    // TODO: enter our own algolia credentials here
    appId: 'latency',
    apiKey: '6be0576ff61c053d5f9a3225e2a90f76',
    indexName: 'instant_search',
    routing: true
  });

  search.addWidget(
    instantsearch.widgets.searchBox({
      container: '#search-box',
      placeholder: 'Search for albums'
    })
  );

  search.addWidget(
    instantsearch.widgets.hits({
      container: '#hits',
      templates: {
        empty: 'No results',
        item: '<em>Hit {{objectID}}</em>: {{{_highlightResult.name.value}}}'
      }
    })
  );

  search.start(); 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们刷新`http://127.0.0.1:4000`浏览器选项卡，看看它是否工作。(您可能需要重启`$ bundle exec jekyll serve --incremental`命令)

这就是我得到的:

[![screenshot of instantsearch.js with a curly brace glitch](../Images/8cc0ba5a6eef032bf57506ab0f6f7988.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mjpoXyhL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f1jzwe4i4zyxicmlez5w.png)

搜索框显示得很好，但是我们也看到它下面有几行字...🤔

如果我们更细心一些，我们会发现`bundle exec jekyll serve`命令给了我们一个似乎与这个问题有关的警告:

```
Liquid Warning: Liquid syntax error (line 55): Unexpected character { in "{{{_highlightResult.name.value}}" in index.md 
```

Enter fullscreen mode Exit fullscreen mode

我的假设是，我从 Instantsearch 的教程中复制粘贴的模板与 Jekyll 的模板解析器冲突。如果这个假设是正确的，那么将这个 JavaScript 代码移动到一个单独的`.js`文件应该可以解决这个问题。

我可以确认我的假设是正确的:将 JavaScript 代码移动到一个新的`search.js`文件中，并使用`<script src="search.js"></script>`从`index.md`导入该文件。😁

所以现在，我得到的是:

[![screenshot of instantsearch.js integration with default search index](../Images/04c59e39f4fd5c211a92ce341deaed48.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--42mVgFGJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m03ii8ve6dwt2ifhrtz0.png)

搜索栏下显示的列表在我输入时确实会实时适应，但点击的不是专辑！

## (3.3)定制我们的搜索索引

我们需要为我们的`instantsearch.js`实例插入正确的 Algolia 索引。为此，我们必须更改`appId`、`apiKey`和`indexName`属性的值，以匹配我们自己的 Algolia 索引的凭证。

> 注意:`instantsearch.js`将使用这些凭证来发送搜索查询，所以您应该在这里输入您的“仅搜索 API 密钥”。(与“管理 API 密钥”位于同一页面)

完成后，我可以搜索我的相册收藏:

[![screenshot of instantsearch integration with untitled hits](../Images/562a143acf0054b34e59217eb704d476.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Jy-teqtg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7ct7fu3tpb90fac8nbdp.png)

当我们键入作为相册名称一部分的单词时，会有一些点击，但最终的相册名称不会显示出来。

正如你在 Algolia 仪表盘上看到的，相册的标题存储在一个名为`content`的属性中。这就是我们应该展示的。

为此，我们需要从`search.js`更新我们的点击模板，从:

```
 item: '<em>Hit {{objectID}}</em>: {{{_highlightResult.name.value}}}' 
```

Enter fullscreen mode Exit fullscreen mode

...致:

```
 item: '<em>Hit {{objectID}}</em>: {{{_highlightResult.content.value}}}' 
```

Enter fullscreen mode Exit fullscreen mode

那就行了！✊

# 结果文件

你可以在 github 仓库中找到我们生产的源代码:[github.com/adrienjoly/jekyll-tutorial](https://github.com/adrienjoly/jekyll-tutorial)。

以下是我们在本教程中创建和更新的文件:

*   `Gemfile`:

```
# Gemfile

source 'https://rubygems.org'
gem 'github-pages', group: :jekyll_plugins

group :jekyll_plugins do
  gem 'jekyll-algolia', '~> 1.0'
end 
```

Enter fullscreen mode Exit fullscreen mode

*   `_config.yml`:

```
# _config.yml

algolia:
  application_id: 'paste_your_application_id_here'
  index_name: 'albums'
  files_to_exclude: []
  nodes_to_index: 'article' 
```

Enter fullscreen mode Exit fullscreen mode

*   `index.md`:

```
<link rel="stylesheet" href="index.css" />

## Albums

{% for album in site.data.albums %}
  <article>
    <a href="{{ album.url }}">
      <img src="{{ album.img }}" alt="{{ album.title }} {{ album.artist }}"/>
      <p>{{ album.title }}</p>
    </a>
    <p>by {{ album.artist }}</p>
    {% if release-date %}
      <span class="release-date">{{ album.release_date | date: "%b %-d, %Y" }}</span>
    {% endif %}
  </article>
{% endfor %}

## Search

<div id="search-box">
  <!-- SearchBox widget will appear here -->
</div>

<div id="hits">
  <!-- Hits widget will appear here -->
</div>

<!-- algolia search -->

<link rel="stylesheet" type="text/css" href="https://cdn.jsdelivr.net/npm/instantsearch.js/dist/instantsearch.min.css">
<link rel="stylesheet" type="text/css" href="https://cdn.jsdelivr.net/npm/instantsearch.js/dist/instantsearch-theme-algolia.min.css">

<script src="https://cdn.jsdelivr.net/npm/instantsearch.js"></script>

<script src="search.js"></script>

<!-- end of algolia search --> 
```

Enter fullscreen mode Exit fullscreen mode

*   `search.js`:

```
const search = instantsearch({
  appId: 'R6Y1H83FNX',
  apiKey: 'b0f283176b810e5223f70e3ab3ac7c04',
  indexName: 'albums',
  routing: true
});

search.addWidget(
  instantsearch.widgets.searchBox({
    container: '#search-box',
    placeholder: 'Search for albums'
  })
);

search.addWidget(
  instantsearch.widgets.hits({
    container: '#hits',
    templates: {
      empty: 'No results',
      item: '<em>Hit {{objectID}}</em>: {{{_highlightResult.content.value}}}'
    }
  })
);

search.start(); 
```

Enter fullscreen mode Exit fullscreen mode

# 下一步:修复 UI

在本教程中，我们将:

*   创建了一个关于 algolia.com 的搜索索引
*   设置 Jekyll-Algolia 从我们的专辑列表中填充此索引
*   整合的 instantsearch.js(尽管有 CDN 问题)
*   并将我们自己的索引插入到搜索组件中

我们还学习了如何升级`ruby`以及如何从`localhost`预览 Jekyll 站点。

尽管事实上我们在相册收藏上有一个有效的搜索栏，但最终的网站并不好看，也不可用。

在下一个教程中，我们将定义我们的目标是什么样的用户体验，并实现它。希望在那之后，我们的专辑会再次看起来很棒！

敬请关注，如果您有任何意见、问题和建议，请与我们分享！