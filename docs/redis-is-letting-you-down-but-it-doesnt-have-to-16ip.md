# Redis 让你失望了，但没必要

> 原文：<https://dev.to/briankephart/redis-is-letting-you-down-but-it-doesnt-have-to-16ip>

我在 [Heroku](https://www.heroku.com) 上有一个 [Rails](http://www.rubyonrails.org) 应用，它使用 [Heroku Redis](https://elements.heroku.com/addons/heroku-redis) 进行缓存。此应用程序上的某些端点使用页面缓存，因此渲染不需要解析模板文件，除非存在缓存缺失。很棒，而且很快。

但是，随着时间的推移，我在我的性能分析器中注意到，模板文件呈现的频率比我预期的要高，这降低了页面加载的速度。在这种背景下，如果一个页面偶尔需要几百毫秒的渲染时间(而不是不到 150 毫秒)，这不是什么大不了的事情，所以我没有太担心这个问题。有更好的方式来花费我的开发时间，而不是修复几个耗时不到一秒的缓慢请求。

然后，我更新到了 Rails 5.2。尽管我很高兴使用 Readthis gem 进行 Redis 缓存，但我认为我应该尝试一下 Rails 新的内置 Redis 缓存存储，只是为了消除对 gem 的依赖。到目前为止，性能与 Readthis 没有什么不同。

然后，我注意到在[文档](http://edgeguides.rubyonrails.org/caching_with_rails.html#activesupport-cache-rediscachestore)中有一个错误处理程序。像 Readthis 一样，Redis 缓存存储是容错的。如果出现 Redis 错误，它将被视为缓存未命中，而不是异常。这对于故障保护操作来说非常好，但是我想知道是否有任何错误没有被报告。我的错误报告服务是[滚动条](https://rollbar.com)，所以我将这一行添加到我的缓存配置中:

```
error_handler: -&gt; (method:, returning:, exception:) {
Rollbar.warning(exception, method: method, returning: returning)} 
```

Enter fullscreen mode Exit fullscreen mode

一个小时后，我收到了一些来自 Rollbar 的警告，其中包含异常`Redis::ConnectionError: Connection lost (ECONNRESET)`。怎么回事？我的低容量应用程序每小时会失去几次与 Redis 的连接。这种情况持续了一整夜。

我查看了 Redis 缓存存储的代码,注意到默认设置中有一些东西:

`reconnect_attempts: 0`

有这么简单吗？我在配置中设置了`reconnect_attempts: 1`,果然，我的错误消失了，缓存未命中也消失了。这一行代码显著加快了我在这些端点上的平均响应时间，这一切都是通过加快最慢的请求来实现的。

我至今不知道 Redis 为什么会那么频繁的掉线，或者说我在使用 Redis 作为服务时的体验是否正常。不过，对我来说，通过网络提供的服务可能偶尔会出现连接问题，特别是当它试图以足够快的速度进行缓存时。

教训:确保在任何容错 Redis 服务上设置错误监控，看看是否可以从允许重新连接尝试中获益。