# Rails 的 url_for 和 strong 参数并不快

> 原文：<https://dev.to/tiagoamaro/rails-urlfor-and-strong-parameters-arent-fast-49e3>

这个帖子也是[发表在我的网站](https://www.tiagoamaro.com.br/2018/07/14/rails-url-for/)上的。

在本文中，我将描述我最近在一个项目中遇到的一个 Rails 性能警告:Rails 强参数及其路由不是高性能的，如果您生成许多 URL，应该避免使用。

## 场景

该项目需要一个多面过滤器，这将产生成千上万的网址。在单个多面过滤器对象中，我必须生成一个 URL，将当前 URL 与给定对象的选项集合并。

解决这个问题的常见方法是使用 Rails 的`url_for`和强参数。我最终为我的视图对象编写了下面的方法:

```
def current_path(options = {})
  permitted_params = params.permit(DEFAULT_PERMITTED_PARAMS).merge(options)
  url_for(permitted_params)
end 
```

Enter fullscreen mode Exit fullscreen mode

酷！这是简洁，安全，它会给我当前的网址与所有额外的选项，我想与它合并。

你可以想象，有一个警告:这是缓慢的。

## 基准

有多慢？页面加载需要 500 毫秒到 800 毫秒，单个用户请求和繁重的 CPU 负载。我知道那个观点有问题。

在分析出`current_path`方法是罪魁祸首之后，我运行了几个基准测试来测试 Rails 方法与纯 Ruby stdlib 方法相比如何。使用 Ruby 的 stdlib `URI`，这个方法可以写成:

```
def current_path(options = {})
  permitted_params = request.parameters.slice(*DEFAULT_PERMITTED_PARAMS) # Avoid strong parameters 
  uri = URI(request.original_url) # Get the current URL through the request
  uri.query = permitted_params.merge(options).to_param
  uri.to_s
end 
```

Enter fullscreen mode Exit fullscreen mode

下面是基准代码:

```
# Inside the `app/controllers/benchmark_controller.rb` file

def show
  uri = URI(request.original_url)

  Benchmark.ips do |x|
    x.report("ActionDispatch::Routing::UrlFor#url_for with query string") do
      params.permit(:id)
      url_for(params.permit(:id)) # => http://localhost:3000/?id=42
    end

    x.report("Ruby stdlib URI") do
      uri.query = request.params.slice(:id).to_param
      uri.to_s # => http://localhost:3000/?id=42
    end

    x.compare!
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

鉴于我经常观察到的两种不同情况，结果非常有趣:

1.  用户请求符合强参数，不需要清理用户输入
2.  用户请求不符合强参数，它需要清理用户输入

### 允许参数的结果

对 URL `http://lvh.me:3000/?id=42`
的示例请求

```
Warming up --------------------------------------
ActionDispatch::Routing::UrlFor#url_for with query string
                       735.000  i/100ms
     Ruby stdlib URI     9.807k i/100ms
Calculating -------------------------------------
ActionDispatch::Routing::UrlFor#url_for with query string
                          7.449k (± 4.8%) i/s -     37.485k in   5.044535s
     Ruby stdlib URI    103.079k (± 4.9%) i/s -    519.771k in   5.054106s

Comparison:
     Ruby stdlib URI:   103079.2 i/s
ActionDispatch::Routing::UrlFor#url_for with query string:     7448.5 i/s - 13.84x  slower 
```

Enter fullscreen mode Exit fullscreen mode

### 结果带有不允许的参数

对 URL `http://lvh.me:3000/?id=42&count=123`
的示例请求

```
Warming up --------------------------------------
ActionDispatch::Routing::UrlFor#url_for with query string
                       309.000  i/100ms
     Ruby stdlib URI     9.325k i/100ms
Calculating -------------------------------------
ActionDispatch::Routing::UrlFor#url_for with query string
                          3.051k (± 8.4%) i/s -     15.450k in   5.101804s
     Ruby stdlib URI     81.424k (± 3.8%) i/s -    410.300k in   5.046415s

Comparison:
     Ruby stdlib URI:    81423.9 i/s
ActionDispatch::Routing::UrlFor#url_for with query string:     3050.7 i/s - 26.69x  slower 
```

Enter fullscreen mode Exit fullscreen mode

## 分析结果和结论

假设`url_for` + strong 参数每秒有 3k 次迭代(在最坏的情况下),并且一些呈现的页面有超过 1500 个锚标签，这很容易消耗 500 毫秒的处理时间，仅仅是为了呈现分面过滤！

Rails 的易用性和魔力是以性能为代价的。它的路由器方法很容易使用，但是请记住，如果您想要更快的响应，您需要远离 Rails。