# 用 ActiveRecord 的计数器缓存来缓存计数器

> 原文：<https://dev.to/appsignal/caching-counters-with-activerecords-counter-caches-jbj>

[ActiveRecord 的计数器缓存特性](http://guides.rubyonrails.org/association_basics.html#options-for-belongs-to-counter-cache)允许存储计数器，并在每次创建或删除相关对象时更新计数器，而不是每次加载页面时都计算数据库中的相关记录。在这一集的 AppSignal Academy 中，我们将学习所有关于在 ActiveRecord 中缓存计数器的内容。

让我们举一个有文章和回复的博客的经典例子。每篇文章都可以有回应，我们希望在博客的索引页面上，在每篇文章的标题旁边显示回应的数量，以显示其受欢迎程度。

```
# app/controllers/articles_controller.rb
class ArticlesController < ApplicationController
  def index
    @articles = Article.all
  end

  # ...
end 
```

Enter fullscreen mode Exit fullscreen mode

我们不需要[预加载回复](https://blog.appsignal.com/2018/04/24/active-record-performance-the-n+1-queries-antipattern.html)，因为我们不会在索引页面上显示他们的数据。我们正在显示一个计数器，所以我们只对每篇文章的回复数量感兴趣。控制器找到所有文章，并将它们放在`@articles`变量中，供视图使用。

```
<!-- app/views/articles/index.html.erb -->
<h1>Articles</h1>

<% @articles.each do |article| %>
<article>
  <h1><%= article.title %></h1>
  <p><%= article.description %></p>
  <%= article.responses.size %> responses
</article>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

该视图遍历每篇文章，并呈现其标题、描述和收到的回复数量。因为我们在视图中调用了`article.responses.size`,所以 ActiveRecord 知道它需要对关联进行计数，而不是为每个响应加载整个记录。

*提示*:虽然`#count`听起来像是统计响应数量的更直观的选择，但是这个例子使用了`#size`，因为`#count`将总是执行`COUNT`查询，而`#size`将在响应已经加载的情况下跳过查询。

```
Started GET "/articles" for 127.0.0.1 at 2018-06-14 16:25:36 +0200
Processing by ArticlesController#index as HTML
  Rendering articles/index.html.erb within layouts/application
  Article Load (0.2ms)  SELECT "articles".* FROM "articles"
  ↳ app/views/articles/index.html.erb:3
  (0.2ms)  SELECT COUNT(*) FROM "responses" WHERE "responses"."article_id" = ?  [["article_id", 2]]
  ↳ app/views/articles/index.html.erb:7
  (0.3ms)  SELECT COUNT(*) FROM "responses" WHERE "responses"."article_id" = ?  [["article_id", 3]]
  ↳ app/views/articles/index.html.erb:7
  (0.1ms)  SELECT COUNT(*) FROM "responses" WHERE "responses"."article_id" = ?  [["article_id", 4]]
  ↳ app/views/articles/index.html.erb:7
  (0.1ms)  SELECT COUNT(*) FROM "responses" WHERE "responses"."article_id" = ?  [["article_id", 5]]
  ↳ app/views/articles/index.html.erb:7
  (0.1ms)  SELECT COUNT(*) FROM "responses" WHERE "responses"."article_id" = ?  [["article_id", 6]]
  ↳ app/views/articles/index.html.erb:7
  (0.1ms)  SELECT COUNT(*) FROM "responses" WHERE "responses"."article_id" = ?  [["article_id", 7]]
  ↳ app/views/articles/index.html.erb:7
  (0.1ms)  SELECT COUNT(*) FROM "responses" WHERE "responses"."article_id" = ?  [["article_id", 8]]
  ↳ app/views/articles/index.html.erb:7
  (0.1ms)  SELECT COUNT(*) FROM "responses" WHERE "responses"."article_id" = ?  [["article_id", 9]]
  ↳ app/views/articles/index.html.erb:7
  (0.1ms)  SELECT COUNT(*) FROM "responses" WHERE "responses"."article_id" = ?  [["article_id", 10]]
  ↳ app/views/articles/index.html.erb:7
  (0.1ms)  SELECT COUNT(*) FROM "responses" WHERE "responses"."article_id" = ?  [["article_id", 11]]
  ↳ app/views/articles/index.html.erb:7
  Rendered articles/index.html.erb within layouts/application (23.1ms)
Completed 200 OK in 52ms (Views: 45.7ms | ActiveRecord: 1.6ms) 
```

Enter fullscreen mode Exit fullscreen mode

请求博客的索引会导致 [N+1 次查询](https://blog.appsignal.com/2018/04/24/active-record-performance-the-n+1-queries-antipattern.html)，因为 ActiveRecord 会在单独的查询中延迟加载每篇文章的响应计数。

## 使用`COUNT()`从查询

为了避免为每篇文章运行额外的查询，我们可以将 articles 和 responses 表连接在一起，在一个查询中计算相关的响应。

```
# app/controllers/articles_controller.rb
class ArticlesController < ApplicationController
  def index
    @articles = Article.
      joins(:responses).
      select("articles.*", 'COUNT("responses.id") AS responses_count').
      group('articles.id')
  end

  # ...
end 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，我们在文章查询中加入响应，并选择`COUNT("responses.id")`来计算响应的数量。我们将按产品 id 分组，以统计每篇文章的回复。在视图中，我们需要使用`responses_count`而不是在响应关联中调用`size`。

该解决方案通过使第一个查询更慢和更复杂来防止额外的查询。虽然这是优化该页面性能的良好开端，但我们可以更进一步，缓存计数器，这样我们就不需要对每个页面视图的每个响应进行计数。

## 计数器缓存

由于博客上的文章阅读(希望)比更新更频繁，一个*计数器缓存*是一个很好的优化，可以使查询这个页面更快更简单。

计数器缓存保存一个单独的响应计数器，存储在每个文章的数据库行中，而不是每次显示文章时都计算响应的数量。每当添加或删除响应时，计数器都会更新。

这允许用一个数据库查询来呈现文章索引，而不需要在查询中加入响应。要进行设置，通过设置`counter_cache`选项来翻转`belongs_to`关系中的开关。

```
# app/models/response.rb
class Response
  belongs_to :article, counter_cache: true
end 
```

Enter fullscreen mode Exit fullscreen mode

这需要一个名为`responses_count`的`Article`模型字段。`counter_cache`选项确保无论何时添加或删除响应，该字段中的数字都会自动更新。

*提示*:字段名可以用一个符号替代`true`作为`counter_cache`选项的值。

我们在数据库中创建一个新列来存储计数。

```
$ rails generate migration AddResponsesCountToArticles responses_count:integer
      invoke  active_record
      create    db/migrate/20180618093257_add_responses_count_to_articles.rb
$ rake db:migrate
== 20180618093257 AddResponsesCountToArticles: migrating ======================
-- add_column(:articles, :responses_count, :integer)
  -> 0.0016s
== 20180618093257 AddResponsesCountToArticles: migrated (0.0017s) ============= 
```

Enter fullscreen mode Exit fullscreen mode

因为响应的数量现在缓存在 articles 表中，所以我们不需要在 articles 查询中加入响应。我们将使用`Article.all`来获取控制器中的所有物品。

```
# app/controllers/articles_controller.rb
class ArticlesController < ApplicationController
  def index
    @articles = Article.all
  end

  # ...
end 
```

Enter fullscreen mode Exit fullscreen mode

我们不需要改变视图，因为 Rails 理解对`#size`方法使用计数器缓存。

```
<!-- app/views/articles/index.html.erb -->
<h1>Articles</h1>

<% @articles.each do |article| %>
<article>
  <h1><%= article.title %></h1>
  <p><%= article.description %></p>
  <%= article.responses.size %> responses
</article>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

再次请求我们的索引，我们可以看到一个查询正在执行。因为每篇文章都知道它的响应数量，所以根本不需要查询 responses 表。

```
Started GET "/articles" for 127.0.0.1 at 2018-06-14 17:15:23 +0200
Processing by ArticlesController#index as HTML
  Rendering articles/index.html.erb within layouts/application
  Article Load (0.2ms)  SELECT "articles".* FROM "articles"
  ↳ app/views/articles/index.html.erb:3
  Rendered articles/index.html.erb within layouts/application (3.5ms)
Completed 200 OK in 42ms (Views: 36.5ms | ActiveRecord: 0.2ms) 
```

Enter fullscreen mode Exit fullscreen mode

## 作用域关联的计数器缓存

ActiveRecord 的计数器缓存回调仅在创建或销毁记录时触发，因此在作用域关联上添加计数器缓存不起作用。对于高级案例，比如只统计*发表的*回复的数量，查看[反文化](https://github.com/magnusvk/counter_culture)宝石。

## 填充计数器缓存

对于在计数器缓存之前的文章，计数器将不同步，因为默认情况下它是 0。我们可以通过对对象使用`.reset_counters`方法并传递对象的 ID 和计数器应该更新的关系来“重置”对象的计数器。

```
Article.reset_counters(article.id, :responses) 
```

Enter fullscreen mode Exit fullscreen mode

为了确保在部署时在生产环境中运行，我们将把它放在一个迁移中，该迁移在上次迁移中添加列后直接运行。

```
$ rails generate migration PopulateArticleResponsesCount --force
      invoke  active_record
      create    db/migrate/20180618093443_populate_article_responses_count.rb 
```

Enter fullscreen mode Exit fullscreen mode

在迁移中，我们将为每篇文章调用`Article.reset_counters`，传递文章的 id 和`:responses`作为关联的名称。

```
# db/migrate/20180618093443_populate_article_responses_count.rb
class PopulateArticleResponsesCount < ActiveRecord::Migration[5.2]
  def up
    Article.find_each do |article|
      Article.reset_counters(article.id, :responses)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

此迁移会更新数据库中所有项目的计数，包括计数器缓存之前存在的项目。

## 回调

因为计数器缓存使用回调来更新计数器，所以直接执行 SQL 命令的方法(比如当使用`#delete`而不是`#destroy`时)不会更新计数器。

在由于某种原因确实发生这种情况的情况下，添加一个 Rake 任务或后台作业来定期保持计数同步可能是有意义的。

```
namespace :counters do
  task update: :environment do
    Article.find_each do |article|
      Article.reset_counters(article.id, :responses)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 缓存的计数器

通过对查询中的关联对象进行计数来防止 N+1 查询会有所帮助，但是对于大多数应用程序来说，缓存计数器是显示计数器的更快的方法。ActiveRecord 的内置缓存计数器可以提供很多帮助，像 [counter_culture](https://github.com/magnusvk/counter_culture) 这样的选项可以用于更复杂的需求。

对 ActiveRecord 的计数器缓存有任何疑问吗？请不要犹豫，通过 [@AppSignal](https://twitter.com/appsignal) 告诉我们。当然，我们很想知道您对这篇文章的看法，或者您是否有其他想了解更多的话题。