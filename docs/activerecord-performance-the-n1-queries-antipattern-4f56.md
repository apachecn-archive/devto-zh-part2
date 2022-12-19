# ActiveRecord 性能:N+1 查询反模式

> 原文：<https://dev.to/appsignal/activerecord-performance-the-n1-queries-antipattern-4f56>

许多[表单](https://en.wikipedia.org/wiki/Object-relational_mapping)，比如 Rails 的 ActiveRecord，都内置了延迟加载功能，允许您将查询关联推迟到需要的时候。通过将这个决定卸载给视图，它允许隐含哪些关联需要被加载。

N+1 查询问题是一个常见但通常容易发现的性能反模式，它导致为每个关联运行一个查询，这在从数据库中查询大量关联时会导致开销。

## active record 中的惰性加载

ActiveRecord 使用隐式延迟加载来简化关系的处理。让我们考虑一下网络商店的例子，例如，每个*产品*可以有任意数量的*变体*，其中包含产品的颜色或尺寸。

```
# app/models/product.rb
class Product < ActiveRecord::Base
  has_many :variants
end 
```

Enter fullscreen mode Exit fullscreen mode

在`ProductsController#show`中，一个产品的详细视图，我们将使用`Product.find(params[:id])`来获取产品并将其赋给`@product`变量。

```
# app/controllers/products_controller.rb
class ProductsController < ApplicationController
  def show
    @product = Product.find(params[:id])
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在这个动作的视图中，我们将通过对从控制器接收的`@product`变量调用`variants`方法来遍历产品的变体。

```
# app/views/products/show.html.erb
<h1><%= @product.title %></h1>

<ul>
<%= @product.variants.each do |variant| %>
  <li><%= variant.name %></li>
<% end %>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

通过调用视图中的`@product.variants`, Rails 将查询数据库以获取变量供我们循环。除了我们在控制器中执行的显式查询之外，如果我们检查 Rails 日志中的这个请求，我们可以看到执行了另一个查询来获取变量。

```
Started GET "/products/1" for 127.0.0.1 at 2018-04-19 08:49:13 +0200
Processing by ProductsController#show as HTML
  Parameters: {"id"=>"1"}
  Product Load (1.1ms)  SELECT  "products".* FROM "products" WHERE "products"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  Rendering products/show.html.erb within layouts/application
  Variant Load (1.1ms)  SELECT "variants".* FROM "variants" WHERE "variants"."product_id" = ?  [["product_id", 1]]
  Rendered products/show.html.erb within layouts/application (4.4ms)
Completed 200 OK in 64ms (Views: 56.4ms | ActiveRecord: 2.3ms) 
```

Enter fullscreen mode Exit fullscreen mode

这个请求执行了两个查询来显示一个产品及其所有变体。

1.  `SELECT "products".* FROM "products" WHERE "products"."id" = 1 LIMIT 1`
2.  `SELECT "variants".* FROM "variants" WHERE "variants"."product_id" = 1`

## 循环懒加载

迄今为止，惰性加载一直很好。例如，通过使用隐式查询，当我们决定不再在这个视图上显示变量时，我们不必记得将它从控制器中删除。

假设我们正在处理`ProductsController#index`，在这里我们想要显示一个所有产品及其每个变体的列表。我们可以像以前一样通过延迟加载来实现。

```
# app/controllers/products_controller.rb
class ProductsController < ApplicationController
  def index
    @products = Product.all
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
# app/views/products/index.html.erb
<h1>Products</h1>

<% @products.each do |product| %>
<article>
  <h1><%= product.title %></h1>

  <ul>
    <% product.variants.each do |variant| %>
      <li><%= variant.description %></li>
    <% end %>
  </ul>
</article>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

与第一个例子不同，我们现在从控制器获得一个产品列表，而不是单个产品。然后视图遍历每个产品，并惰性加载每个产品的每个变体。

虽然这种方法可行，但有一个问题。我们的查询计数现在是 *N+1* 。

## N+1 次查询

在第一个例子中，我们呈现了一个产品及其变体的视图。*查询计数*是 2，因为我们执行了两个查询。这个请求从数据库中返回所有产品(在这个例子中是 3 个),以及它们的每个变体，它执行了四次查询，而不是两次。

```
Started GET "/products" for 127.0.0.1 at 2018-04-19 09:49:02 +0200
Processing by ProductsController#index as HTML
  Rendering products/index.html.erb within layouts/application
  Product Load (0.3ms)  SELECT "products".* FROM "products"
  Variant Load (0.2ms)  SELECT "variants".* FROM "variants" WHERE "variants"."product_id" = ?  [["product_id", 1]]
  Variant Load (0.2ms)  SELECT "variants".* FROM "variants" WHERE "variants"."product_id" = ?  [["product_id", 2]]
  Variant Load (0.1ms)  SELECT "variants".* FROM "variants" WHERE "variants"."product_id" = ?  [["product_id", 3]]
  Rendered products/index.html.erb within layouts/application (5.6ms)
Completed 200 OK in 36ms (Views: 32.6ms | ActiveRecord: 0.8ms) 
```

Enter fullscreen mode Exit fullscreen mode

1.  `SELECT "products".* FROM "products"`
2.  `SELECT "variants".* FROM "variants" WHERE "variants"."product_id" = 1`
3.  `SELECT "variants".* FROM "variants" WHERE "variants"."product_id" = 2`
4.  `SELECT "variants".* FROM "variants" WHERE "variants"."product_id" = 3`

第一个查询由控制器中对`Product.all`的显式调用执行，查找所有产品。在视图中的每个产品上循环时，后续的被延迟执行。

这个示例导致查询计数为 N+1，其中 N 是产品的数量，增加的一个是获取所有产品的显式查询。换句话说；此示例执行一个查询，然后针对第一个查询中的每个结果执行另一个查询。因为在这个例子中 N = 3，所以得到的查询计数是`N + 1 = 3 + 1 = 4`。

当只有三个产品时，这可能不是一个问题，但是查询次数会随着产品数量的增加而增加。因为我们知道这个请求有 N+1 个查询，所以当我们有 100 个产品(`N + 1 = 100 + 1 = 101`)时，我们可以预测查询计数为 101。

## 急切载入联想

我们希望在这个视图中有一个静态的请求数量，而不是像现在这样随着产品数量的增加而增加查询数量。我们可以通过在呈现视图之前在控制器中显式预加载变量来做到这一点。

```
# app/controllers/products_controller.rb
class ProductsController < ApplicationController
  def index
    @products = Product.all.includes(:variants)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

ActiveRecord 的`includes`查询方法确保相关的变量与它们的产品一起加载。因为它知道需要预先加载哪些变体，所以它可以在一个查询中获取所有请求产品的所有变体。

```
Started GET "/products" for 127.0.0.1 at 2018-04-19 10:33:59 +0200
Processing by ProductsController#index as HTML
  Rendering products/index.html.erb within layouts/application
  Product Load (0.3ms)  SELECT "products".* FROM "products"
  Variant Load (0.4ms)  SELECT "variants".* FROM "variants" WHERE "variants"."product_id" IN (?, ?, ?)  [["product_id", 1], ["product_id", 2], ["product_id", 3]]
  Rendered products/index.html.erb within layouts/application (5.9ms)
  Completed 200 OK in 45ms (Views: 40.8ms | ActiveRecord: 0.7ms) 
```

Enter fullscreen mode Exit fullscreen mode

通过预加载变量，即使将来产品数量增加，查询计数也会下降到 2。

1.  `SELECT "products".* FROM "products"`
2.  `SELECT "variants".* FROM "variants" WHERE "variants"."product_id" IN (1, 2, 3)`

## 懒惰还是渴望？

在大多数情况下，在单个查询中获取数据库中的所有相关记录比延迟加载它们要快得多。

在这个示例应用程序中，数据库性能差异仅在三个产品中可以测量，每个产品都有十个变体。平均而言，急切加载产品列表比延迟加载快 12.5%(0.7 毫秒对 0.8 毫秒)。对于 10 种产品，这一差异跃升至 59% (1.22 毫秒对 2.98 毫秒)。对于 1000 个产品，差异几乎是 80%，因为急切查询的时间是 58.4 毫秒，而延迟加载需要大约 290.12 毫秒。

虽然延迟加载的关联在视图中提供了更多的灵活性，而不必更新控制器，但是一个好的经验法则是在将数据传递给视图之前，让控制器处理数据的加载。

从视图中延迟加载适用于显示一个模型对象及其关联的视图(比如我们第一个例子中的`ProductsController#show`),例如，当多个视图需要来自同一个控制器的不同数据时，这可能会很有用。

简而言之:始终关注开发日志，或 AppSignal 中的[事件时间线，以确保您没有执行可能被延迟加载的查询，并跟踪您的响应时间，尤其是当处理的数据量增加时。](https://appsignal.com/tour/performance?utm_source=DevTo&utm_medium=DevToBlogPost&utm_campaign=DevToContentPosting_NPlus1)

如果你喜欢这篇文章，可以看看我们在 [AppSignal Academy](https://blog.appsignal.com/category/academy.html?utm_source=DevTo&utm_medium=DevToBlogPost&utm_campaign=DevToContentPosting_NPlus1) 上写的更多内容。AppSignal 致力于构建更好的应用。在我们的学院系列中，我们将探索应用程序的稳定性和性能，并解释核心编程概念。

我们很想知道您对这篇文章的看法，或者您是否有任何问题。我们总是在寻找可以研究和解释的话题，所以如果你想了解 Ruby 的神奇之处，请不要犹豫，留下你的评论。