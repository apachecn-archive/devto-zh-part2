# 使用 Rack::Reducer 通过 URL 参数动态过滤数据

> 原文：<https://dev.to/chrisfrank/dynamically-filter-data-via-url-params-with-rackreducer-9p6>

无论你是在构建一个小的 API 还是一个巨大的模块，你的应用程序可能需要在某个地方呈现一个数据库记录列表。您可能希望这些记录可以通过 URL 参数进行过滤，如下所示:

`GET /artists?genre=electronic&sort=name`

当相关参数存在时，最好进行排序或过滤，否则返回合理的默认数据集。

```
GET /artists?name=blake` => artists named 'blake'
GET /artists?genre=electronic&sort=name => electronic artists, sorted by name
GET /artists => all artists 
```

Enter fullscreen mode Exit fullscreen mode

您*可以*用手写的`if`语句有条件地应用过滤器，但是您添加的过滤器越多，这种方法就越难看。

在 Rails 中，你可以使用 Plataformatec 古老的 has scope gem。

但是如果你在洛达、辛纳屈或花见工作呢？即使在 Rails 中，如果您宁愿编写专用的查询对象，也不愿用过滤代码污染您的模型和控制器，那该怎么办？

[齿条::减速器](https://github.com/chrisfrank/rack-reducer)可以帮忙。它是一个 gem，将输入的 URL 参数映射到您定义的一组过滤函数，只应用适用的过滤器，并返回您过滤的数据。它可以使您的控制器逻辑最小化...

```
@artists = Artist.reduce(params) 
```

Enter fullscreen mode Exit fullscreen mode

...或者，如果您不喜欢神奇的隐式代码，您可以调用 Rack::Reducer 作为一个函数来构建更显式的查询——也许就在您的控制器中，也许在专用的[查询对象](https://robots.thoughtbot.com/using-yieldself-for-composable-activerecord-relations)中，或者实际上在您喜欢的任何地方。

```
# app/controllers/artists_controller.rb
class ArtistsController < ApplicationController
  def index
    @artists = Rack::Reducer.call(params, dataset: Artist.all, filters: [
      ->(name:) { where('lower(name) like ?', "%#{name.downcase}%") },
      ->(genre:) { where(genre: genre) },
      ->(sort:) { order(sort.to_sym) },
    ])
    render json: @artists
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

Rack::Reducer 可以在任何 Rack 兼容的应用程序中工作，支持任何 ORM，除了 Rack 本身之外没有任何依赖。完整文档在 GitHub 上[。无论你是在 Rails、](https://github.com/chrisfrank/rack-reducer) [Sinatra](http://sinatrarb.com) 、[洛达](http://roda.jeremyevans.net)、 [Hanami](http://hanamirb.org) 还是 raw Rack 工作，我都希望它能对你有用。