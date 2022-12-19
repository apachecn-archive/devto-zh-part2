# 使用 Vue.js 将您的 YouTube 视频添加到您的静态站点

> 原文：<https://dev.to/raymondcamden/adding-your-youtube-videos-to-your-static-site-with-vuejs-3d6o>

这篇文章归功于我和我的好友托德·夏普的一次讨论。他通过服务器端代码将他的 YouTube 视频添加到他的网站上，我认为编写一个快速的纯 JavaScript 版本的代码也会很有趣。虽然可能有很多这样的 bazallion 库，但为了我自己的利益，我想用 [Vue.js](https://vuejs.org/) 快速模拟这个想法。此外——虽然 JavaScript 相当琐碎，但我在本周晚些时候有一篇很好的后续文章，展示了一个完全静态的(有点！)这样做的方式。

## 获取您的 RSS 网址

所以第一步是获取你的 RSS 网址。我第一次在谷歌上搜索解决方案时，找到了一个仍然有用的旧解决方案。如果你去你的频道，例如，[https://www.youtube.com/user/TheRaymondCamden](https://www.youtube.com/user/TheRaymondCamden)，然后查看源，只需 ctrl-f(查找)为`rssXML`。您将看到类似这样的内容:

```
"rssUrl":"https://www.youtube.com/feeds/videos.xml?channel_id=UC8KROrnEHSnnV3z5J_FoSIg" 
```

Enter fullscreen mode Exit fullscreen mode

这是你的网址。我敢 100%打赌，有一种更简单的方法可以做到这一点，但这就是我的工作。

## 解析 RSS

所以早在 2015 年(很久以前……)，我写了一篇关于用 JavaScript 解析 RSS 的文章:[用 JavaScript - Options](https://www.raymondcamden.com/2015/12/08/parsing-rss-feeds-in-javascript-options) 解析 RSS 提要。我最喜欢的选择仍然是 [YQL](https://developer.yahoo.com/yql/) 。我很震惊它仍然存在，考虑到雅虎似乎-我不知道-不一定增长-但是的，它仍然工作。*然而*，我用来解析 RSS 的 YQL 并不适用于这个 RSS 提要。具体来说:

```
select * from rss where url="https://www.youtube.com/feeds/videos.xml?channel_id=UC8KROrnEHSnnV3z5J_FoSIg" 
```

Enter fullscreen mode Exit fullscreen mode

我想这可能是等号，但逃避它并没有帮助。我用我的 RSS 进行了快速测试，以确保该特性在总体上仍然有效，而且确实有效，所以我改用简单的 XML。我不是说 XML 解析是我之前的博客文章中的第一个选项，而是从 YQL 中获取 XML，YQL 会很好地为您解析 XML。其 YQL 为:

```
select * from xml where url = "https://www.youtube.com/feeds/videos.xml?channel_id=UC8KROrnEHSnnV3z5J_FoSIg" 
```

Enter fullscreen mode Exit fullscreen mode

## 使用 Vue.js

好了，现在我知道了如何从我的视频提要中获取和解析 XML。在我的第一稿中，我只是想将每个视频作为原始对象转储出来。我从这个布局开始:

```
<div id="app" v-cloak>
  <div v-for="video in videos">
    {{video}}
    <hr/>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

基本上，这将打印出每个对象的 JSON 版本。现在来看看 JavaScript:

```
let feed = 'https://www.youtube.com/feeds/videos.xml?channel_id=UC8KROrnEHSnnV3z5J_FoSIg';

let yql = `https://query.yahooapis.com/v1/public/yql?q=select%20entry%20from%20xml%20where%20url%20%3D%20'${feed}'%20&format=json&env=store%3A%2F%2Fdatatables.org%2Falltableswithkeys`;

const app = new Vue({
  el:'#app',
  data:{
    videos:[]
  },
  created:function() {
    fetch(yql)
    .then(res => res.json())
    .then(res => {
      res.query.results.feed.forEach(o => {
        this.videos.push(o.entry);        
      });
      console.log(res.query.results.feed);
    })
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

我为我的 RSS 提要准备了一个变量，然后为 YQL URL 准备了一个变量。理论上，您可以简单地更改第一行来使用您提要。然后，我做了一个快速的`fetch`调用来获取解析后的 XML。如您所见，结果有点复杂:`res.query.results.feed`。这是一个带有名为`entry`的键的对象数组。因此，为了使我的用法简单一点，我创建了一个新的数组，只包含那个`entry`值。您可以在下面看到结果:

[https://codepen.io/cfjedimaster/embed/djmvPo?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/djmvPo?height=600&default-tab=result&embed-version=2)

太棒了。

## 添加漂亮的

好了，在这一点上，你有很多选择如何显示视频。结果集包括缩略图和链接，所以非交互式的图片列表就可以了。你也可以为每个视频嵌入一个 YouTube 播放器。[文档](https://developers.google.com/youtube/player_parameters)对此提供了指导，但基本形式是:

```
<iframe id="ytplayer" type="text/html" width="640" height="360"
  src="https://www.youtube.com/embed/M7lc1UVf-VE?autoplay=1&origin=http://example.com"
  frameborder="0"></iframe> 
```

Enter fullscreen mode Exit fullscreen mode

请注意，他们使用的是你绝对想切换到 0 的`autoplay=1`，因为自动播放是魔鬼，尤其是在显示一堆视频的时候！这是我用来展示视频的东西。肯定可以更好:

```
<div id="app" v-cloak>
  <h2>My Videos</h2>
  <div v-for="video in videos">
    <iframe id="ytplayer" type="text/html" width="640" height="360"
    :src="'https://www.youtube.com/embed/'+video.videoId+'?autoplay=0&origin=http://example.com'"
  frameborder="0"></iframe>
    <hr/>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

请注意，`origin`应该更改为您的 URL 以获得额外的安全性，但它在 CodePen 上工作得很好:

[https://codepen.io/cfjedimaster/embed/QBmpJJ?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/QBmpJJ?height=600&default-tab=result&embed-version=2)

请在下面留言，让我知道你的想法。正如我所说的，我有一个有趣的转折，希望在本周晚些时候出现！