# Rails 中的俄罗斯娃娃缓存

> 原文：<https://dev.to/appsignal/russian-doll-caching-in-rails--3j8o>

当使用 Rails 内置的[片段缓存](https://blog.appsignal.com/2018/03/20/fragment-caching-in-rails.html)时，渲染视图的一部分被存储为视图片段，如果再次被请求就可以重用。这些缓存的片段被重用，直到它们变得*陈旧*，这意味着它们已经过时，因为它们显示的数据在创建片段后已经改变。

虽然这提供了一个很好的速度提升，特别是对于复杂的视图或有许多渲染部分的视图，我们可以通过加倍使用一种叫做*俄罗斯娃娃缓存*的方法来进一步改进这一点。

当使用这种缓存方法时，视图片段被放置在彼此内部，很像该策略命名的[“Matryoshka”玩偶](https://en.wikipedia.org/wiki/Matryoshka_doll)。通过将缓存片段分成更小的片段，当只有一个嵌套片段发生变化时，外部缓存可以更快地呈现。

## 产品变型

举个例子，让我们使用一个销售产品的商店。每种产品都可以有许多变体，例如，允许销售一种商品的多种颜色。在索引中，我们将显示每种可供销售的产品，以及它的所有变体。

在产品索引中，我们将每个产品部分包装在一个`cache`块中。我们使用`product`对象来构建缓存键，该键用于使缓存的片段无效。它由对象的 id、它的 updated_at 日期和模板树的摘要组成，所以如果对象发生变化，或者模板的内容发生变化，它会自动被认为是过时的。

```
# app/views/products/index.html.erb
<h1>Products</h1>

<% @products.each do |product| %>
  <% cache product do %>
    <%= render product %>
  <% end %>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

在 products partial 中，我们为产品的每个变体呈现一行。

```
# app/views/products/_product.html.erb
<article>
  <h1><%= product.title %></h1>

  <ul>
    <% product.variants.each do |variant| %>
      <%= render variant %>
    <% end %>
  </ul>
</article>
% end %> 
```

Enter fullscreen mode Exit fullscreen mode

## 缓存失效

尽管 Rails 的片段缓存中的缓存键使缓存失效变得更容易，但您永远无法完全摆脱对缓存验证的担心(这是计算机科学中著名的两大难题之一)。

在这个例子中，我们缓存了 products partial，它包含了产品的变体列表。因为缓存键不包含任何关于变体的信息，所以任何新添加的变体都不会显示出来，除非产品本身也发生了变化。

解决这个问题的方法是确保产品*在它的一个变体发生变化时*也发生变化。为此，无论何时发生这种情况，我们都会更新产品的`updated_at`属性。因为这很常见，所以有一个用于`belongs_to`(以及 ActiveModel 的其他关系方法)的参数，叫做`:touch`，它会自动为我们更新父对象的 updated_at。

```
class Variant < ApplicationRecord
  belongs_to :product, touch: true
end 
```

Enter fullscreen mode Exit fullscreen mode

## 嵌套片段

既然我们已经确保了当产品片段的变体改变时更新产品片段，那么也是时候缓存变体了。像以前一样，我们将在每个周围添加一个`cache`块。

```
<article>
  <h1><%= product.title %></h1>

  <ul>
    <% product.variants.each do |variant| %>
      <% cache(variant) do %>
        <%= render variant %>
      <% end %>
    <% end %>
  </ul>
</article> 
```

Enter fullscreen mode Exit fullscreen mode

在冷缓存上(可以通过运行`rake tmp:cache:clear`来清空缓存)，第一个请求将呈现每个产品的一部分。

当现在请求页面时(不要忘记通过运行`rails dev:cache`打开开发中的缓存)，每个产品分部将被缓存为分部，第二个请求将返回缓存的片段。

```
Started GET "/products" for 127.0.0.1 at 2018-03-30 14:51:38 +0200
Processing by ProductsController#index as HTML
  Rendering products/index.html.erb within layouts/application
  Product Load (0.2ms)  SELECT "products".* FROM "products"
  Variant Load (0.9ms)  SELECT "variants".* FROM "variants" WHERE "variants"."product_id" IN (27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51)
  Rendered variants/_variant.html.erb (0.5ms)
  Rendered variants/_variant.html.erb (0.1ms)
  Rendered variants/_variant.html.erb (0.1ms)
  Rendered variants/_variant.html.erb (0.0ms)
  Rendered variants/_variant.html.erb (0.1ms)
  Rendered variants/_variant.html.erb (0.1ms)
  Rendered variants/_variant.html.erb (0.1ms)
  Rendered variants/_variant.html.erb (0.1ms)
  Rendered variants/_variant.html.erb (0.1ms)
  Rendered variants/_variant.html.erb (0.1ms)
  Rendered products/_product.html.erb (44.8ms) [cache miss]
  ...
  Rendered variants/_variant.html.erb (0.1ms)
  Rendered variants/_variant.html.erb (0.1ms)
  Rendered variants/_variant.html.erb (0.1ms)
  Rendered variants/_variant.html.erb (0.1ms)
  Rendered variants/_variant.html.erb (0.1ms)
  Rendered variants/_variant.html.erb (0.1ms)
  Rendered variants/_variant.html.erb (0.1ms)
  Rendered variants/_variant.html.erb (0.1ms)
  Rendered variants/_variant.html.erb (0.1ms)
  Rendered variants/_variant.html.erb (0.1ms)
  Rendered products/_product.html.erb (46.2ms) [cache miss]
  Rendered products/index.html.erb within layouts/application (1378.6ms)
Completed 200 OK in 1414ms (Views: 1410.5ms | ActiveRecord: 1.1ms)

Started GET "/products" for 127.0.0.1 at 2018-03-30 14:51:41 +0200
Processing by ProductsController#index as HTML
  Rendering products/index.html.erb within layouts/application
  Product Load (0.3ms)  SELECT "products".* FROM "products"
  Variant Load (12.7ms)  SELECT "variants".* FROM "variants" WHERE "variants"."product_id" IN (27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51)
  Rendered products/index.html.erb within layouts/application (48.1ms)
Completed 200 OK in 76ms (Views: 59.0ms | ActiveRecord: 13.0ms) 
```

Enter fullscreen mode Exit fullscreen mode

俄罗斯人偶缓存的神奇之处，在改变其中一个变种的时候就能看出来。当其中一个变量发生变化后再次请求索引时，缓存的产品片段会被重新呈现，因为其`updated_at`属性发生了变化。

产品部分包括产品的每个变型。我们刚刚更改的变体的缓存片段是陈旧的，所以需要重新生成，但是其他变体没有更改，所以它们的缓存片段被重用。在日志中，我们可以看到变体和产品部分都被渲染了一次。

```
Started GET "/products" for 127.0.0.1 at 2018-03-30 14:52:04 +0200
Processing by ProductsController#index as HTML
  Rendering products/index.html.erb within layouts/application
  Product Load (0.3ms)  SELECT "products".* FROM "products"
  Variant Load (1.2ms)  SELECT "variants".* FROM "variants" WHERE "variants"."product_id" IN (27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51)
  Rendered variants/_variant.html.erb (0.5ms)
  Rendered products/_product.html.erb (13.3ms) [cache miss]
  Rendered products/index.html.erb within layouts/application (45.9ms)
Completed 200 OK in 78ms (Views: 73.5ms | ActiveRecord: 1.5ms) 
```

Enter fullscreen mode Exit fullscreen mode

通过像这样嵌套缓存片段，视图几乎不会完全呈现，除非缓存完全为空。即使数据发生变化，大多数呈现的页面也是直接从缓存中提供的。

到目前为止，你觉得我们关于 Rails 缓存的文章怎么样？请不要犹豫，通过 [@AppSignal](https://twitter.com/appsignal) 告诉我们。当然，我们也很想知道你希望我们写些什么(与缓存相关的或其他的)!