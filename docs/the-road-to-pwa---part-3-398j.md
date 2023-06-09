# 通往艾滋病毒/艾滋病的道路-第三部分

> 原文：<https://dev.to/s1hofmann/the-road-to-pwa---part-3-398j>

> 这篇文章最初发布在[我的个人博客](https://blog.simon-hofmann.org/pwa/js/2018/06/03/road-to-pwa03.html)

# 故事继续...

既然我们已经发现了[服务人员能够做什么](https://dev.to/s1hofmann/-the-road-to-pwa---part-2-46d9)，是时候实际使用他们了。

我们要用它来做的事情是* *打鼓* * **缓存**！
缓存使我们的应用程序即使在用户离线时也能运行，
这是渐进式网络应用程序的关键特性之一。

因此，我们将在这篇文章中讨论以下内容:

*   缓存和缓存版本控制
*   预缓存
*   动态缓存
*   缓存实用程序

## 缓存和缓存版本控制

缓存被命名为请求及其相应响应的键/值存储。它们允许我们预先缓存静态数据以备后用，或者动态添加动态内容以供离线使用。

缓存必须手动管理，因此数据既不会自动添加也不会自动更新。
缓存也没有过期时间，每当我们想要清除过期数据时，我们可以手动删除它或删除整个缓存。

由于我们必须手动管理我们的缓存，我们还必须确保我们的缓存提供最新的数据。
在我们继续之前，让我们看看如何打开缓存:

```
caches.open($cacheName).then(cache => {}); 
```

Enter fullscreen mode Exit fullscreen mode

打开缓存时，我们必须提供缓存名称。如果存在具有所提供名称的缓存，它将被打开，否则将以此名称创建新的缓存对象。

`caches.open(...)`返回一个`Promise`，它解析为打开的缓存，所以我们能够在一个`.then(cache => {})`块中修改缓存。

现在，由于缓存是使用名称进行操作的，所以通过混淆缓存名称很容易在应用程序中引入错误。因此，显而易见的解决方案是在一个中心位置存储和管理正在使用的缓存。

```
const currentCaches = {
  static: "static-cache-v1",
  dynamic: "dynamic-cache-v1"
}; 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码片段还展示了我们如何将版本控制应用到缓存中。
它们的名字由它们的类型组合而成，在这个例子中，我们处理一个静态和一个动态缓存，以及一个版本字符串，在这个例子中是`v1`。

因此，每当我们更改静态缓存中的数据时，我们都必须更新缓存版本，以确保我们更新的数据也在我们的缓存中更新。

### 缓存清理

正如我们在我的上一篇文章中了解到的，服务工作者生命周期的激活阶段非常适合清除过时的缓存。

```
self.onactivate = event => {
  const KNOWN_CACHES = Object.values(currentCaches);

  event.waitUntil(
    caches.keys().then(cacheNames => {
      return Promise.all(
        cacheNames.map(cacheName => {
          if (KNOWN_CACHES.indexOf(cacheName) < 0) {
            console.log("Purging outdated cache:", cacheName);
            return caches.delete(cacheName);
          }
        })
      );
    })
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们通过调用`event.waitUntil(...)`来延长`activate`事件，并检查每个可用的缓存是否在我们的已知缓存列表中。如果没有，我们将删除它，因为它不再需要。

由于`caches.delete(...)`返回了一个`Promise`，我们将清理代码包装在一个`Promise.all(...)`中，它接受一个`Promise`对象的列表，并且只有当所有这些`Promise`都解析时才会解析。

## 预缓存

顾名思义，预缓存在实际需要之前存储数据。在 PWAs 中，这通常用于存储正确显示应用程序静态“外壳”所需的资产。
这包括以下内容

*   半铸钢ˌ钢性铸铁(Cast Semi-Steel)
*   射流研究…
*   形象
*   字体
*   等等。

提供应用程序基本版本所需的静态资产的缓存通常被称为“应用程序外壳”策略。

应用程序外壳缓存发生在服务人员的`install`阶段，允许我们显示应用程序的静态内容，即使用户离线。

```
self.oninstall = event => {
  const dataToCache = [
          "/app-icon-48x48.6dc6b62a.png",
          "/apple-icon-76x76.3b41636a.png",
          "/main-image.8ec44c4f.jpg",
          "/main-image-lg.8b45ce89.jpg",
          "/manifest.f43e1207.webmanifest",
          .
          .
          .
        ];

  event.waitUntil(
    caches.open(currentCaches.static).then(cache => {
      cache
        .addAll(dataToCache)
        .catch(error =>
          console.log("Failed to initialize static cache:", error)
        );
    })
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

[`cache.add(...)`](https://developer.mozilla.org/en-US/docs/Web/API/Cache/add) 将 URL 作为参数，获取它并将结果请求/响应对放入当前打开的缓存中。
它的扩展， [`cache.addAll(...)`](https://developer.mozilla.org/en-US/docs/Web/API/Cache/addAll) 工作方式完全相同，但它处理的不是一个 URL，而是一个完整的 URL 列表。
`cache.addAll(...)`提供了一个很好的、简单的方法来添加静态资产列表到我们的缓存中。另一方面，一旦单个资产无法获取，这个调用将为您留下一个半初始化的缓存。

## 动态缓存

好了，现在我们能够显示应用程序的静态外壳，即使用户离线。但是如果我们还想在离线模式下显示动态数据呢？

在安装时，我们不知道任何动态数据，如用户图像，文本帖子等。，所以我们不能把它们放到我们的静态缓存中。幸运的是，我们还能够拦截来自应用程序内部的任何请求。

通过监听`fetch`事件，我们能够拦截和解析任何请求和/或响应。执行动态缓存的最佳位置。

```
self.onfetch = event => {
  event.respondWith(
    caches.match(event.request).then(cachedResponse => {
      if (cachedResponse) {
        return cachedResponse;
      } else {
        return fetch(event.request)
          .then(fetchedResponse => {
            if (!fetchedResponse.ok) {
              return fetchedResponse;
            } else {
              return caches
                .open(currentCaches.dynamic)
                .then(cache => {
                  if (event.request.method === "GET") {
                    const clonedResponse = fetchedResponse.clone();
                    cache.put(event.request, clonedResponse);
                  }
                  return fetchedResponse;
                })
                .catch(reason =>
                  console.log("An error occured while caching data:", reason)
                );
            }
          })
          .catch(reason => {
            console.log("An error occured while fetching data:", reason);
          });
      }
    })
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

这个为动态缓存提供的代码片段应用了所谓的“缓存优先”策略。对于应用程序发出的每个请求，我们首先检查是否已经有缓存的响应。如果是这样，我们将立即返回缓存的响应。这将最终导致更快的响应时间，但也有可能导致数据过时。

在缓存未命中时，我们`fetch`初始请求，检查请求是否成功，并将请求/响应对添加到我们的缓存中。
这也可以通过使用已知的`cache.add(...)`或`cache.addAll(...)`方法来完成，但是如果您想将额外的定制逻辑应用到缓存中，这是一个更详细的开始。

需要注意的一点是对`fetchedResponse.clone()`的调用。
因为响应是流，所以只能使用一次。因此，为了在将获取的响应添加到我们的缓存后返回它，我们必须创建它的克隆。

## 缓存实用程序

缓存是一个相当沉重的话题。缓存有多种策略，这也取决于当前的情况，哪种策略可能是最合适的。

Mozilla 提供了所谓的[“服务工作者食谱”](https://serviceworke.rs/)，其中包含了关于各种缓存策略的更多细节。

此时，您可能还会问自己，是否每次需要预缓存和/或动态缓存时，我们都必须重新发明轮子。

答案是:不会。

谷歌提供了一个叫做[工具箱](https://developers.google.com/web/tools/workbox/)的工具，它可以帮助你编写样板代码，比如预缓存、动态缓存等等。，因此您不必手动编写缓存代码。

## 结论

在这篇文章中，我演示了如何表演

*   缓存版本控制
*   缓存清理
*   预缓存
*   动态缓存

在我的下一篇文章中，我们将看看如何在 IndexedDB 中存储动态内容，敬请关注！

再见

西蒙