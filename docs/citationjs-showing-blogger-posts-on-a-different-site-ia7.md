# Citation.js:在不同的网站上显示博客帖子

> 原文：<https://dev.to/larsgw/citationjs-showing-blogger-posts-on-a-different-site-ia7>

> ![Lars Willighagen profile image](../Images/3f82a50389ebaeea8070bdbab6feee41.png)Lars Willighagen@ larswillighagen![twitter logo](../Images/4c8a2313941dda016bf4d78d103264aa.png)我为 Blogger 做了一个小客户端，它接受一个标签并将其转换成自己的小博客:[citation.js.org/blog/?post=542…](https://t.co/CYoveVJIOE)。但是没有元数据，因为都是客户端的。2018 年 8 月 06 日下午 18:31[![Twitter reply action](../Images/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1026536137958072320)[![Twitter retweet action](../Images/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1026536137958072320)[![Twitter like action](../Images/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1026536137958072320)

我制作了一个以[材料为主题的](https://getmdl.io)页面，展示来自 Blogger 的 Citation.js 博客帖子。它支持分页，标签，搜索和链接个人职位。由于这是一个单一的静态页面，我不能支持元数据的`meta`和`link`标签，这将需要 JavaScript，而索引器不运行。

Blogger API 的伟大之处在于，您可以为单个标签生成提要，例如 Citation.js，并在该标签中搜索标签和一般查询。这才使得这一切成为可能。URL 方案很简单:

```
# Tag feed
https://$BLOG.blogspot.com/feeds/posts/default/-/$TAG

# Tag-in-tag feed
https://$BLOG.blogspot.com/feeds/posts/default/-/$TAG/$OTHER_TAG

# Search-in-tag feed
# Note: don't copy this, there's a ZWS before ?q= for syntax highlighting
https://$BLOG.blogspot.com/feeds/posts/default/-/$TAG​?q=$QUERY

# Post
https://$BLOG.blogspot.com/feeds/posts/default/$POST 
```

Enter fullscreen mode Exit fullscreen mode

分页和响应格式有点复杂，在下面的代码中处理。

除了材料主题，它只使用普通的 JavaScript 来生成页面。搜索栏甚至根本不用 JavaScript，就好 ol' `form`语义。它使用的 JavaScript 相当简单。首先，解析查询并生成 API URL。

```
window.onload = function () {
  var params = {}

  location.search.slice(1).split('&').map(function (pair) {
    pair = pair.split('=')
    params[pair[0]] = pair[1]
  })

  var url

  if (params.post) {
    url = 'https://larsgw.blogspot.com/feeds/posts/default/' + params.post + '?alt=json-in-script&callback=cb'
  } else if (params.tag) {
    url = 'https://larsgw.blogspot.com/feeds/posts/default/-/Citation.js/' + params.tag + '?alt=json-in-script&callback=cb'
  } else if (params.query) {
    url = 'https://larsgw.blogspot.com/feeds/posts/default/-/Citation.js/?q=' + params.query + '&alt=json-in-script&callback=cb'
  } else {
    url = 'https://larsgw.blogspot.com/feeds/posts/default/-/Citation.js?alt=json-in-script&callback=cb'
  }

  var startIndex = location.href.match(/start-index=(\d+)/)
  if (startIndex) {
    url += '&' + startIndex[0]
  }

  load(url)
} 
```

Enter fullscreen mode Exit fullscreen mode

因为 Blogger 的唯一 JSON API 是 JSON-in-script，所以我们添加了一个脚本元素来加载资源。然后调用回调函数`cb`。

```
function cb (data) {
  content.innerHTML = data.feed ? templates.feed(data.feed.entry) : templates.feedItem(data.entry)

  // pagination
  if (data.feed) {
    var href = location.href
    var hasIndex = href.indexOf('start-index') > -1
    var hasParams = href.indexOf('?') > -1
    var indexPattern = /start-index=(\d+)/

    var prev = find(data.feed.link, function (link) { return link.rel === 'previous' })
    if (prev) {
      prev = 'start-index=' + prev.href.match(indexPattern)[1]
      var url = hasIndex ? href.replace(indexPattern, prev) : href + (hasParams ? '?' : '') + prev
      paginatePrev.setAttribute('href', url)
    }

    var next = find(data.feed.link, function (link) { return link.rel === 'next' })
    if (next) {
      next = 'start-index=' + next.href.match(indexPattern)[1]
      var url = hasIndex ? href.replace(indexPattern, next) : href + (hasParams ? '&' : '?') + next
      paginateNext.setAttribute('href', url)
    }
  }
}

function load (url) {
  loader.setAttribute('src', url)
} 
```

Enter fullscreen mode Exit fullscreen mode

回调然后使用简单的模板，这些模板只是 JS 函数，接收 API 响应并输出 HTML 以在页面上显示结果。然后，它计算出分页。下面是一个示例模板。它提取文章 id 来制作链接，并做一些预处理，删除 stackedit 元数据和样式，并将每个标题降低两级。然后，它将 HTML 与一些附加的实用函数和子模板放在一起。

```
 feedItem: function (item) {
    var id = item.id.$t.replace(/^.*\.post-(\d+)$/, '$1')
    var content = item.content.$t
      .replace(/^[\s\S]*<div class="stackedit__html">([\s\S]*)<\/div>[\s\S]*$/, '$1')
      .replace(/<(\/?)h([1-6])/g, function (match, slash, level) {
        if (+level > 4) {
          return '<' + slash + 'b'
        } else {
          return '<' + slash + 'h' + (+level + 2)
        }
      })

    return '<div class="mdl-card mdl-shadow--2dp mdl-cell mdl-cell--12-col">' +
      '<div class="mdl-card__title">' +
        '<h2 class="mdl-card__title-text">' +
          '<a href="?post=' + id + '">' + item.title.$t + '</a>' +
        '</h2>' +
      '</div>' +
      '<div class="mdl-card__supporting-text mdl-card--border">' +
        '<p>' +
          '<span><i class="material-icons">edit</i> ' +
            templates.author(item.author[0]) +
          '</span>' +
          '<span><i class="material-icons">access_time</i> ' +
            formatDate(item.updated.$t) +
          '</span>' +
          '<span><i class="material-icons">link</i> <a href="' +
            canonical(item.link) +
          '">Original post</a></span>' +
        '</p>' +
        '<p>' +
          '<span><i class="material-icons">bookmark</i> ' +
            map(item.category, templates.tag).join('  ') +
          '</span>' +
        '</p>' +
      '</div>' +
      '<div class="mdl-card__supporting-text">' +
        content +
      '</div>' +
    '</div>'
  }, 
```

Enter fullscreen mode Exit fullscreen mode

完整的源代码可以在[这里](https://github.com/citation-js/site/blob/master/static/js/blog.js)获得，页面可以在[这里](https://citation.js.org/blog/)查看。结果:

[![Blog screenshot](../Images/2a1bb334d5063ac1a8032f98afc8467a.png "Blog screenshot")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eiEsoHRE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/Qd8EXpblQui1CSErr_BU1Os9Ykk-ximWT_hcTxGhtJBwY6JoM7jSxt7FlAwOSkAs57G4nZ64ccAtMg%3Ds2000)