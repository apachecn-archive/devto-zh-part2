# 服务人员:当心 Safari 的范围请求

> 原文：<https://dev.to/philnash/service-workers-beware-safaris-range-request-38i7>

您已经实现了一个服务工作器来缓存一些资产。一切都很好，你的服务人员很成功，你感觉很好。但是后来…

过了一段时间，您将一个视频部署到您的站点上。在 Chrome、Firefox 和 Edge 中，一切都运行良好。你去查查 Safari。视频坏了。你不知道哪里出了问题。

那是上个月的我。我发表了一篇关于使用 React 在画布中制作动画的文章，并希望使用一个动画作为标题图像。[一个视频比使用一个可比较的 gif 小得多](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/replace-animated-gifs-with-video/)所以我实现了一个视频头。当我来检查 Safari 中的东西时，我发现它停止了工作，我不知道为什么。

## 诊断问题

我起初以为这可能与我正在使用的 CDN 有关。有一些关于通过 CDN 传输视频的误报，导致一些额外的研究最终没有结果。一旦我完成了这个询问，我就回到失败的请求。

在 Safari 的 inspector 中观察这个请求导致进一步搜索互联网，最终事情开始增加。Safari 正在发送一个获取视频的初始请求，其标题`Range`设置为`bytes=0-1`。你看， [Safari 需要提供视频和音频的 HTTP 服务器来支持像](https://developer.apple.com/library/archive/documentation/AppleApplications/Reference/SafariWebContent/CreatingVideoforSafarioniPhone/CreatingVideoforSafarioniPhone.html#//apple_ref/doc/uid/TP40006514-SW6)这样的`Range`请求。

Nginx 正在为`Range`请求提供正确的响应。CDN 也是。唯一的另一个问题？服务人员。而这打破了 Safari 中的视频。

是时候修理服务人员了。受 Chrome 团队的这个[缓存视频的例子的启发，我做了以下事情。](https://googlechrome.github.io/samples/service-worker/prefetch-video/)

## 范围内请求服务人员

我现有的服务人员实现是检查对我的资产或图像目录的请求，并使用先缓存后网络的策略进行响应。

```
self.addEventListener('fetch', function(event) {
  var url = new URL(event.request.url);
  if (url.pathname.match(/^\/((assets|images)\/|manifest.json$)/)) {
    event.respondWith(returnFromCacheOrFetch(event.request, staticCacheName));
  }
  // other strategies
}); 
```

视频请求将由资产或图像目录提供服务，所以这是插入的地方。我检查了范围标题的存在，并用不同的方法进行了响应。

```
self.addEventListener('fetch', function(event) {
  var url = new URL(event.request.url);
  if (url.pathname.match(/^\/((assets|images)\/|manifest.json$)/)) {
    if (event.request.headers.get('range')) {
      event.respondWith(returnRangeRequest(event.request, staticCacheName));
    } else {
      event.respondWith(returnFromCacheOrFetch(event.request, staticCacheName));
    }
  }
  // other strategies
}); 
```

现在，来实现`returnRangeRequest`。首先是缓存，然后是网络方法，您可能以前在服务人员中见过这种方法。打开缓存并根据请求进行检查。如果存在缓存的响应，则返回该响应，如果缓存中不存在该响应，则从网络中获取该响应，将该响应克隆并存储在缓存中，然后返回结果。

重要的是，结果用 [`Response#arrayBuffer`](https://developer.mozilla.org/en-US/docs/Web/API/Body/arrayBuffer) 变成了一个 [`ArrayBuffer`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) 。这将使我们能够访问原始字节，以便稍后构建我们的响应。

```
function returnRangeRequest(request, cacheName) {
  return caches
    .open(cacheName)
    .then(function(cache) {
      return cache.match(request.url);
    })
    .then(function(res) {
      if (!res) {
        return fetch(request)
          .then(res => {
            const clonedRes = res.clone();
            return caches
              .open(cacheName)
              .then(cache => cache.put(request, clonedRes))
              .then(() => res);
          })
          .then(res => {
            return res.arrayBuffer();
          });
      }
      return res.arrayBuffer();
    })
    .then(...); // The rest goes here
} 
```

现在我们有了一个`arrayBuffer`，来自缓存或网络响应，真正的工作开始了。 [`Range`头](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Range)长得像:

```
Range: bytes=200-1000 
```

在这种情况下，请求是针对响应的第 200 个和第 1000 个字节之间的字节。`Range`头可能省略了结束字节，这意味着它想要从第一个值直到文件结尾的所有字节。我们可以用一点正则表达式提取这些数字:

```
 }).then(function(arrayBuffer) {
    const bytes = /^bytes\=(\d+)\-(\d+)?$/g.exec(
      request.headers.get('range')
    );
    // and so on
  });
} 
```

快速分解这个正则表达式，它查找以“bytes=”开头的字符串，后跟一些数字、一个连字符和可选的在字符串结尾之前的一些数字。这两组数字是使用括号捕获的，以便我们以后可以使用它们。

我检查头部是否满足正则表达式，如果满足，则将开始和结束字节值转换成整数，以索引到`arrayBuffer`中。如果结尾字节索引不存在，那么它被设置为文件的结尾。通过字节索引，可以生成响应。

响应的主体是从开始到结束由`Range`头定义的`arrayBuffer`的[片](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer/slice)。该响应携带一个 [206 状态](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/206)，意味着部分内容。它还需要一个`Content-Range`标题，类似于原来的`Range`标题。它告诉浏览器响应由字节范围组成，并返回文件的总大小。

如果正则表达式失败，那么服务人员将返回一个 [416 错误](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/416)。

```
 }).then(function(arrayBuffer) {
    const bytes = /^bytes\=(\d+)\-(\d+)?$/g.exec(
      request.headers.get('range')
    );
    if (bytes) {
      const start = Number(bytes[1]);
      const end = Number(bytes[2]) || arrayBuffer.byteLength - 1;
      return new Response(arrayBuffer.slice(start, end + 1), {
        status: 206,
        statusText: 'Partial Content',
        headers: [
          ['Content-Range', `bytes ${start}-${end}/${arrayBuffer.byteLength}`]
        ]
      });
    } else {
      return new Response(null, {
        status: 416,
        statusText: 'Range Not Satisfiable',
        headers: [['Content-Range', `*/${arrayBuffer.byteLength}`]]
      });
    }
  });
} 
```

修好了。随着服务人员的重新加载，我的视频再次开始在所有浏览器中播放。

## 服务人员和浏览者

你可以在 GitHub 上的我的服务人员里看到[的全部功能。仅仅是让 Safari 同意显示视频就要做很多额外的工作，但它确实教会了我一些关于浏览器在加载视频内容时的期望，如何响应服务人员内部更复杂的请求，以及 Safari 可以有多挑剔。](https://github.com/philnash/philna.sh/blob/ba798a2d5d8364fc7c1dae1819cbd8ef103c8b67/sw.js#L50-L94)

如果您有同样的问题，我希望我的代码有所帮助。如果您为服务人员使用 Workbox，它有一个[范围请求模块](https://developers.google.com/web/tools/workbox/modules/workbox-range-requests)，您也可以使用它。

如果你自己发现了这个问题，或者你有其他的解决方案，我很乐意听听。请在下面的评论中或在 Twitter 上给我留言，地址是 [@philnash](https://twitter.com/philnash) 。