# 为静态站点实现全文搜索

> 原文：<https://dev.to/albogdano/implementing-full-text-search-for-your-static-site-4ool>

[![](img/ebb4e6f1839cf2dfd21fa8d662e5ad26.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2-RA0OpP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://erudika.com/assets/img/blogpost_media8.png)

静态站点生成器非常棒——它们可以快速完成我们的博客、登录页面和项目网站的工作。它们使构建、部署和遗忘变得简单——免费托管，无需维护，一种“无服务器”技术。静态网站非常适合那些很少改变的内容。它们缺乏动态功能，但主要问题是你不能在不离开网站的情况下快速搜索一段内容，这阻碍了用户体验。

我们使用 Hexo 生成我们的博客，这是一个简单的静态站点生成器，我们对此非常满意。我们一直认为我们的搜索框可以改进，把用户送到谷歌是草率的。在本教程中，我将向您展示如何升级您的博客或静态网站上的搜索框。

因此，我们有一堆为我们的网站生成的 HTML 文件，我们需要在某个地方对它们进行索引，以便我们以后可以查询它们。搜索框将向搜索 API 发送 AJAX 请求，并将结果呈现给用户——非常简单的设置。所以我们需要三样东西:

1.  一个快速索引我们内容的工具；
2.  一个全文搜索 API，最好是免费的；
3.  一些 JavaScript 代码来调用 API 并显示结果。

目前，有几种实现全文搜索的解决方案。根据你愿意花费的时间和金钱，你可以托管自己的搜索服务器，比如 Elasticsearch 或 Solr，或者付费使用 Algolia 这样的服务。现在，对于像一个有十篇文章的博客这样简单的东西，支付服务器或每月订阅费有点过分。这就是我们灵活、开源的后端服务的用武之地。它对开发者完全免费，里面的搜索功能是由 elastic search——*提供的**！***

最初，当我们开始这项任务时，我们没有为我们的博客帖子建立索引的工具，这导致我们编写了 [para-cli](https://github.com/Erudika/para-cli) —一个与我们的 para 后端服务器一起工作的命令行工具。它是用 JavaScript for Node.js 编写的，使用起来非常简单。让我们继续安装它:

```
$ npm install -g para-cli
# run setup and set endpoint to either 'http://localhost:8080' or 'https://paraio.com'
$ para-cli setup 
```

现在你可以通过在控制台中输入`para-cli`来看到所有的选项。有几个用于读取、写入和删除对象的基本命令和一个用于搜索的命令。目前，我们只对`create`命令感兴趣。

如果您还没有免费账户，请访问[ParaIO.com](https://paraio.com/signin)并注册。接下来，我们将创建一个新的应用程序来存储我们的博客文章。如果你已经有一个包含一些对象的应用程序，你仍然可以通过使用不同类型的对象来适应你的站点内容，比如`blogpost`。

[![](img/3e5e17100fd623d0ac483963eca5a311.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M9J8W1cT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://erudika.com/assets/img/ftsearch1.png)

记下您的 Para 访问和密钥，我们将在 CLI 工具中用到它们。现在是时候收集我们的 HTML 文件并将它们发送到 Para 进行索引了。

```
$ para-cli create "blog/20*/**/*.html" --type "blogpost" --sanitize \
--accessKey "app:myapp" --secretKey "[key]" 
```

[![](img/ed4621fcc2ba99e0cdcfdd3ccd2b1e1c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Wk1hm3Nk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://erudika.com/assets/img/ftsearch2.png)

在我们的例子中，HTML 文件位于一个名为“blog”的文件夹中，每年都有子文件夹。CLI 工具将解析每个 HTML 文件并寻找这两个标签:

```
<meta property="og:title" content="…">
<meta property="og:url" content="…"> 
```

大多数静态站点生成器，像 Octopress 和 Hexo，会自动在代码中包含这些标签。我们还想索引新发布的博客文章，所以我们只需再次执行上面的命令，一切都会同步。如果一切顺利，我们现在应该已经准备好了所有内容的索引。让我们通过发送搜索查询来测试一下:

```
$ para-cli search "some keyword" --accessKey "app:myapp" --secretKey "[key]" 
```

这应该以 JSON 对象数组的形式返回结果。厉害！现在，为了让我们的客户端 JavaScript 小部件能够在没有密钥的情况下访问 Para search API，我们必须允许公众访问
和`blogposts`资源。

[![](img/43308708a379a00f034dccc968f78786.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FqpV7vSf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://erudika.com/assets/img/ftsearch3.png)

在我们的例子中，搜索框是用老式的 jQuery 和一个名为
[typehead.js](https://twitter.github.io/typeahead.js/) 的插件实现的。代码非常简单——它调用搜索 API 并查询类型为`blogpost`的对象。结果是带有几个属性的 JSON 对象的形式，但是我们只对找到的对象感兴趣，所以我们把它转换成一个`Array`。

```
var APPID = "app:myapp";
var ENDPOINT = "https://paraio.com/v1";

$.ajaxSetup({
  headers: {'Authorization': 'Anonymous ' + APPID}
});

var blogposts = new Bloodhound({
  datumTokenizer: Bloodhound.tokenizers.whitespace,
  queryTokenizer: Bloodhound.tokenizers.whitespace,
  remote: {
    url: ENDPOINT + '/blogposts?q=%QUERY',
    wildcard: '%QUERY',
    transform: function (res) {
      return res.items || [];
    }
  }
});

$('#search-box').typeahead({
  hint: false,
  highlight: true,
  minLength: 3
},
{
  name: 'blogposts',
  source: blogposts,
  templates: {
    notFound: '<i>No results.</i>'
  },
  display: function (result) {
    return result.name;
  }
});

$('#search-box').bind('typeahead:select', function (ev, result) {
  window.location = result.url || '';
}); 
```

最后，我们添加一些 CSS 样式来使我们的小部件看起来更好。其中一些类是特定于 typehead.js 的，但也可以修改。

```
#search-box {
  background: #fff;
  width: 100%;
  padding: 10px;
  border-radius: 3px;
  border: 1px solid #ddd;
  font-size: 1.4em;
}
.tt-menu {
  width: 100%;
  margin: 2px 0;
  padding: 5px 10px;
  background-color: #fff;
  border: 1px solid #ccc;
}
.tt-suggestion:last-child {
  border-bottom: none;
}
.tt-suggestion {
  border-bottom: 1px dashed #ccc;
  font-size: 1.4em;
  padding: 10px 0;
}
.tt-suggestion.tt-cursor {
  color: #fff;
  background-color: #0097cf;
}
.twitter-typeahead {
  width: 100%;
} 
```

## 最终结果

*就是这样！*你可以在这一页的上方，标题的上方，试用已经完成的东西。我们选择 jQuery 只是因为它已经包含在我们的 HTML 代码中，但是使用其他 JS 框架如 Angular 或 React 也可以很容易地获得相同的结果。