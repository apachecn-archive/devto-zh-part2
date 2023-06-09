# Rails 集合缓存

> 原文：<https://dev.to/appsignal/rails-collection-caching-1oed>

我们之前在 AppSignal Academy 上看过 Rails 中的[片段缓存。通过缓存视图的较小部分，这极大地提高了视图的性能。当缓存片段时，我们有一个额外的好处，那就是能够在视图的其他地方以很低的成本重用它们。](https://blog.appsignal.com/2018/03/20/fragment-caching-in-rails.html)

这对于小的集合很有效，但是对于大的集合很快就会出现问题。在本文中，我们将了解 Rails 集合缓存是如何工作的，以及如何使用它来加速大型集合的呈现。

## 渲染一个集合

让我们从一个小控制器开始，它为我们博客的索引页面加载最后 100 篇文章。

```
class PostsController < ApplicationController
  def index
    @posts = Post.all.order(:created_at => :desc).limit(100)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

为了在视图中呈现这些帖子，我们循环遍历`@posts`实例变量。

```
<!-- app/views/posts/index.html.erb -->
<h1>Posts</h1>

<div class="posts">
  <% @posts.each do |post| %>
    <div class="post">
      <h2><%= post.title %></h2>
      <small><%= post.author %></small>

      <div class="body">
        <%= post.body %>
      </div>
    </div>
  <% end %>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

在请求该页面时，我们看到从数据库中提取的帖子和呈现的视图。视图层只花了 32 毫秒，这个页面非常快。

```
Started GET "/posts"
Processing by PostsController#index as HTML
  Rendering posts/index.html.erb within layouts/application
  Post Load (1.5ms)  SELECT  "posts".* FROM "posts" ORDER BY "posts"."created_at" DESC LIMIT ?  [["LIMIT", 100]]
  ↳ app/views/posts/index.html.erb:4
  Rendered posts/index.html.erb within layouts/application (19.4ms)
Completed 200 OK in 37ms (Views: 32.4ms | ActiveRecord: 2.7ms) 
```

Enter fullscreen mode Exit fullscreen mode

## 渲染带有偏色的集合

接下来，我们想在另一个视图中使用`post`元素，所以我们将 post HTML 移动到一个部分。

```
<!-- app/views/posts/index.html.erb -->
<h1>Posts</h1>

<div class="posts">
  <% @posts.each do |post| %>
    <%= render post %>
  <% end %>
</div>

<!-- app/views/posts/_post.html.erb -->
<div class="post">
  <h2><%= post.title %></h2>
  <small><%= post.author %></small>

  <div class="body">
    <%= post.body %>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
Started GET "/posts"
Processing by PostsController#index as HTML
  Rendering posts/index.html.erb within layouts/application
  Post Load (1.2ms)  SELECT  "posts".* FROM "posts" ORDER BY "posts"."created_at" DESC LIMIT ?  [["LIMIT", 100]]
  ↳ app/views/posts/index.html.erb:4
...
  Rendered posts/_post.html.erb (0.1ms)
  Rendered posts/_post.html.erb (0.1ms)
  Rendered posts/index.html.erb within layouts/application (205.4ms)
Completed 200 OK in 217ms (Views: 213.8ms | ActiveRecord: 1.7ms) 
```

Enter fullscreen mode Exit fullscreen mode

在视图层上花费了 213 毫秒，可以看到渲染时间大大增加了。这是因为每次发布都需要加载、编译和呈现一个新文件(部分文件)。让我们简单看看如何通过片段缓存来提高渲染时间。

## 片段缓存

正如在[片段缓存](https://blog.appsignal.com/2018/03/20/fragment-caching-in-rails.html)文章中所描述的，我们将在视图中围绕`render`调用使用`cache`助手。这样，我们将缓存每个帖子的局部渲染。

```
<!-- app/views/posts/index.html.erb -->
<h1>Posts</h1>

<div class="posts">
  <% @posts.each do |post| %>
    <%= cache post do %>
      <%= render post %>
    <% end %>
  <% end %>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
Started GET "/posts"
Processing by PostsController#index as HTML
  Rendering posts/index_with_partial_caching.html.erb within layouts/application
  Post Load (1.4ms)  SELECT  "posts".* FROM "posts" ORDER BY "posts"."created_at" DESC LIMIT ?  [["LIMIT", 100]]
  ↳ app/views/posts/index.html.erb:4
...
Read fragment views/posts/index.1ms)
  Rendered posts/_post.html.erb (0.1ms)
Write fragment views/posts/index.1ms)
Read fragment views/posts/index.5ms)
  Rendered posts/_post.html.erb (0.1ms)
Write fragment views/posts/index.1ms)
  Rendered posts/index.html.erb within layouts/application (274.5ms)
Completed 200 OK in 286ms (Views: 281.4ms | ActiveRecord: 2.4ms) 
```

Enter fullscreen mode Exit fullscreen mode

第一个请求不会快很多，因为它仍然需要在第一次呈现每个部分，并将其存储在缓存存储中。

```
Started GET "/posts"
Processing by PostsController#index as HTML
  Rendering posts/index.html.erb within layouts/application
  Post Load (2.2ms)  SELECT  "posts".* FROM "posts" ORDER BY "posts"."created_at" DESC LIMIT ?  [["LIMIT", 100]]
  ↳ app/views/posts/index.html.erb:4
...
Read fragment views/posts/index.1ms)
Read fragment views/posts/index.1ms)
  Rendered posts/index.html.erb within layouts/application (63.8ms)
Completed 200 OK in 78ms (Views: 75.5ms | ActiveRecord: 2.2ms) 
```

Enter fullscreen mode Exit fullscreen mode

在随后的请求中，我们看到花费在视图上的时间大大减少了——从 286 毫秒减少到 78 毫秒。然而，它仍然比我们用原始代码得到的要慢得多——几乎慢了一倍。

注意:如果您在日志中看不到“读/写片段”行，请确保在您的开发环境中启用片段缓存日志记录，在 Rails 5.1 和更高版本中默认设置为`false`:

```
# config/environments/development.rb
config.action_controller.enable_fragment_cache_logging = true 
```

Enter fullscreen mode Exit fullscreen mode

## 收藏缓存

在 Rails 5 中，做了很多工作来提高集合缓存的速度。为了利用这些改进，我们需要更改视图代码。我们可以让 Rails 呈现整个集合并同时缓存它，而不是自己调用`cache`助手。

```
<!-- app/views/posts/index.html.erb -->
<h1>Posts</h1>

<div class="posts">
  <%= render partial: :post, collection: @posts, cached: true %>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

注意,`render @collection, cached: true`简写对这种缓存速度的提高不起作用。

```
Started GET "/posts"
Processing by PostsController#index as HTML
  Rendering posts/index.html.erb within layouts/application
  Post Load (1.4ms)  SELECT  "posts".* FROM "posts" ORDER BY "posts"."created_at" DESC LIMIT ?  [["LIMIT", 100]]
  ↳ app/views/posts/index.html.erb:4
  Rendered collection of posts/_post.html.erb [0 / 100 cache hits] (28.2ms)
  Rendered posts/index.html.erb within layouts/application (46.6ms)
Completed 200 OK in 64ms (Views: 59.9ms | ActiveRecord: 2.0ms) 
```

Enter fullscreen mode Exit fullscreen mode

对于第一个请求，我们已经可以看到在视图层上花费的时间有了很大的改进。这是因为 Rails 现在提前做好了准备，部分内容用于整个集合，而不是单独用于每个帖子。

```
Started GET "/posts"
Processing by PostsController#index as HTML
  Rendering posts/index.html.erb within layouts/application
  Post Load (1.3ms)  SELECT  "posts".* FROM "posts" ORDER BY "posts"."created_at" DESC LIMIT ?  [["LIMIT", 100]]
  ↳ app/views/posts/index.html.erb:4
  Rendered collection of posts/_post.html.erb [100 / 100 cache hits] (19.2ms)
  Rendered posts/index.html.erb within layouts/application (26.5ms)
Completed 200 OK in 37ms (Views: 35.7ms | ActiveRecord: 1.3ms) 
```

Enter fullscreen mode Exit fullscreen mode

在后续的请求中，我们看到了更多的改进——从 64 毫秒减少到大约 35 毫秒。Rails 对集合的优化极大地提高了整个集合的速度。Rails 不是为每个部分检查缓存的可用性，而是同时检查集合的所有缓存键，节省了查询[缓存存储](https://blog.appsignal.com/2018/04/17/rails-built-in-cache-stores.html)的时间。

这个缓存助手的另一个好处是集合的汇总日志记录。在第一个请求中，没有找到任何缓存键`[0 / 100 cache hits]`，但是在第二个请求中，它们都找到了`[100 / 100 cache hits]`。

在更新了数据库中的一些对象之后，我们甚至可以看到有多少键是陈旧的。

```
Rendered collection of posts/_post.html.erb [88 / 100 cache hits] (13.4ms) 
```

Enter fullscreen mode Exit fullscreen mode

通过这种优化的集合呈现和缓存，速度有了很大的提高。当渲染更大的集合时，差异会更大。除非您需要为自己的集合定制视图，否则这种优化策略是 Rails 应用程序的最佳选择。在 AppSignal，我们设法通过这种方式显著提高了呈现数千条记录的管理视图的速度。

关于在 Rails 中缓存集合有什么问题吗？请不要犹豫留下评论！如果您对这篇文章有任何意见，或者您有任何希望我们报道的话题，请与我们联系。