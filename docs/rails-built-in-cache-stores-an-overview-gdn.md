# Rails 的内置缓存存储:概述

> 原文：<https://dev.to/appsignal/rails-built-in-cache-stores-an-overview-gdn>

当生成一个新的 Rails 应用程序时，缓存已经设置好了。这使得使用`cache`助手或者使用`Rails.cache.fetch`存储外部 API 结果来包装一些视图片段[一样快。缓存的数据存储在 Rails 的一个缓存存储中，它可以将数据保存在内存、Memcached、Redis 中，甚至直接写入磁盘。](https://blog.appsignal.com/2018/03/20/fragment-caching-in-rails.html)

但是哪种不同的缓存存储最适合哪种情况呢？在本次概述中，我们将了解每个选项。

## 文件存储

当没有专门配置缓存存储时，默认使用`:file_store`。顾名思义，它将缓存条目写入文件系统。除非您通过显式地将`cache_store`配置设置为`:file_store`并传递一个您想要使用的目录的路径来选择一个不同的位置，否则这些文件将在应用程序根目录的`tmp/cache`目录中结束。

```
config.cache_store = :file_store , "/path/to/cache/directory" 
```

Enter fullscreen mode Exit fullscreen mode

缓存文件保存在磁盘上，不会自动清除。这意味着您需要自己小心地删除它们，以确保它们不会填满您的磁盘。例如，定期运行`Rails.cache.cleanup`将使您的缓存没有过期的条目。

文件存储在开发中很有用，因为每个条目都存储在缓存目录中，允许您通过删除项目的文件来从缓存中删除项目。例如，这对于测试缓存失效很有用。

在生产中，当每个服务器进程运行在同一个文件系统上时，文件存储非常有用。虽然它比基于内存的缓存存储要慢，但是如果缓存有可能变得非常大，那么文件存储也可以很大。

## 记忆储存

从版本 5.0 开始，Rails 在生成新的应用程序时会自动在开发配置中设置`:memory_store`。当使用内存存储时，缓存的数据保存在 Ruby web 服务器进程的内存中。

因为数据保存在与 web 服务器相同的进程中的内存中，所以内存存储非常适合在开发中使用，因为每当您重新启动开发 web 服务器时，它就会自动清除。

默认情况下，内存存储将使用 32 兆字节，但是您可以在配置缓存存储时通过传递`:size`选项来覆盖它。

```
config.cache_store = :memory_store, { size: 16.megabytes } 
```

Enter fullscreen mode Exit fullscreen mode

当缓存已满时，最近最少使用的条目会自动从缓存中删除，以便为新条目腾出空间。

在生产中使用内存存储是可能的(也是您能找到的最快的解决方案)，但是对于运行多个服务器进程的系统不推荐使用。由于进程不能访问彼此的缓存，每个进程都需要维护自己的缓存副本。

## Memcache 存储

`:mem_cache_store`使用 [Dalli gem](https://github.com/petergoldstein/dalli) 和 [Memcached](https://www.memcached.org) 将条目存储在一个集中的内存缓存中。

数据保存在一个单独的进程中，而不是 Ruby 服务器进程中。因此，当您的应用程序重新启动时，缓存不会被丢弃，而是只要 Memcached 服务器继续运行，缓存就会一直留在内存中。当它重新启动时，您将从一个新的缓存开始。

默认情况下，Memcache 存储将假设缓存服务器运行在 localhost 上，但是您可以传递一个或多个地址来使用远程服务器。

```
config.cache_store = :mem_cache_store, "cache-1.example.com", "cache-2.example.com" 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，Memcached 被配置为使用 64 MB 的最大缓存大小，但这可以使用命令行选项或在`memcached.conf`文件中[配置](https://github.com/memcached/memcached/wiki/ConfiguringServer)。与内存存储一样，当缓存达到最大值时，它将开始删除最近最少使用的项目。

`:mem_cache_store`是生产环境的首选缓存存储。通过使用中央 memcached 服务器，缓存可以在多个 web 服务器之间共享，甚至在使用远程 memcached 服务器时可以在多个主机上共享。

## 重定向缓存商店

Rails 5.2 引入了`:redis_cache_store`存储，它允许你在 [Redis](https://redis.io) 中存储缓存条目，就像你使用 Memcache 存储一样。

要将 Redis 用作 Rails 缓存存储，请使用一个专用的 Redis 缓存，它被设置为 *LRU* (最近使用的)缓存，而不是将存储指向现有的 Redis 服务器，以确保当存储达到最大值时，条目会被删除。

Redis store 与 [Redis gem](https://github.com/antirez/redis) (包括 [Redis::Distributed](https://github.com/redis/redis-rb/blob/master/lib/redis/distributed.rb) )和 [hiredis](https://github.com/redis/hiredis) 一起工作，并提供对许多配置选项的支持，如设置一个或多个远程服务器。

```
cache_servers = %w[redis://cache-01:6379/0 redis://cache-02:6379/0]
config.cache_store = :redis_cache_store, { url: cache_servers } 
```

Enter fullscreen mode Exit fullscreen mode

Redis 会定期将数据集写入磁盘，因此当缓存服务器重新启动时，大部分缓存的数据会保留下来。在开发中，可以使用`Rails.cache.delete`从控制台中移除缓存的项目。

在产品中，Redis 在提供集中式缓存存储方面与 Memcached 竞争。虽然它在 Rails 应用程序中的使用还没有 Memcached 广泛，但 Redis 商店将来很可能会成为一个流行的 Rails 缓存商店。

## 使用哪个缓存存储？

总的来说，Rails 的文件和内存存储对于开发来说是很棒的，但是如果知道并理解了它们的注意事项，也可以在生产中用于较小的应用程序。生产级 Memcached 和 Redis 存储通常是更大的生产应用程序的更好选择，尤其是在多台主机上运行多个 web 服务器时。

这就结束了我们对 Rails 中缓存存储的概述。请务必查看 Rails 指南中的[缓存存储部分](http://guides.rubyonrails.org//caching_with_rails.html#cache-stores),了解每个存储的更多信息和配置选项。

你觉得这篇文章和之前的 [AppSignal 学院系列](https://blog.appsignal.com/category/academy.html)的文章怎么样？我们还有一些关于 Rails 缓存的文章，但是请不要犹豫[让我们知道](https://twitter.com/appsignal)你希望我们接下来写什么(缓存相关的或者其他的)!