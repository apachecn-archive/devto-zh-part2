# 在 Ruby 中确保执行、重试失败和重新引发异常

> 原文：<https://dev.to/appsignal/ensuring-execution-retrying-failures-and-reraising-exceptions-in-ruby-4gd4>

引发的异常可以被[拯救](https://blog.appsignal.com/2018/04/10/rescuing-exceptions-in-ruby.html)以在出错时执行替代的代码路径，但是有更多的方法来处理异常。在这一期的 AppSignal Academy 中，我们将讨论`retry`和`ensure`关键字，并了解如何重新引发被挽救的异常。

让我们假设我们正在与一个不可靠的 web API 通信。除了偶尔停机之外，它非常慢，对它的请求可能需要几秒钟。我们的库依赖于这个 API，我们需要使它尽可能有弹性。

## `ensure`

`ensure`关键字用于*确保*代码块运行，即使发生异常。

在我们的库中，我们希望确保由`Net::HTTP.start`打开的 TCP 连接被关闭，即使请求因为超时而失败。为此，我们首先将我们的请求包装在一个`begin` / `ensure` / `end`块中。`ensure`部分的代码将一直运行，即使在前面的`begin`块中出现异常。

在`ensure`块中，我们将确保通过调用`Net::HTTP#finish`来关闭 TCP 连接，除非`http`变量为`nil`，这可能发生在打开 TCP 连接失败时(这也将引发一个异常)。

```
require "net/http"

begin
  puts "Opening TCP connection..."
  http = Net::HTTP.start(uri.host, uri.port)
  puts "Sending HTTP request..."
  puts http.request_get(uri.path).body
ensure
  if http
    puts "Closing the TCP connection..."
    http.finish
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

*注意*:我们手动关闭 TCP 连接，以便稍后重试时可以使用该连接。然而，由于`Net::HTTP.start`使用了一个确保连接关闭的块，上面的示例可以重写以删除`ensure`。有趣的是，确保块也是在 Net::HTTP 中实现[的方式。](https://ruby-doc.org/stdlib-2.4.2/libdoc/net/http/rdoc/Net/HTTP.html#method-i-start)

## `retry`

关键字`retry`允许在一个块中重试一段代码。结合一个`rescue`块，如果我们无法打开连接，或者 API 响应时间太长，我们可以用它再试一次。

为此，我们将向`Net::HTTP.start`调用添加一个`read_timeout`，将超时设置为 10 秒。如果到那时我们的请求还没有得到回应，这将引发一场`Net::ReadTimeout`。

我们还将匹配`Errno::ECONNREFUSED`来处理 API 完全关闭，这将阻止我们打开 TCP 连接。在这种情况下，`http`变量就是`nil`。

异常被挽救，调用`retry`以再次启动`begin`块，这导致代码执行相同的请求，直到没有超时发生。我们将重用持有连接的`http`对象，如果它已经存在。

```
require "net/http"

http = nil
uri = URI("http://localhost:4567/")

begin
  unless http
    puts "Opening TCP connection..."
    http = Net::HTTP.start(uri.host, uri.port, read_timeout: 10)
  end
  puts "Executing HTTP request..."
  puts http.request_get(uri.path).body
rescue Errno::ECONNREFUSED, Net::ReadTimeout => e
  puts "Timeout (#{e}), retrying in 1 second..."
  sleep(1)
  retry
ensure
  if http
    puts "Closing the TCP connection..."
    http.finish
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们的请求将每秒重试一次，直到没有引发`Net::ReadTimeout`为止。

```
$ ruby retry.rb
Opening TCP connection...
Executing HTTP request...
Timeout (Net::ReadTimeout), retrying in 1 second...
Executing HTTP request...
Timeout (Net::ReadTimeout), retrying in 1 second...
Executing HTTP request...
Timeout (Net::ReadTimeout), retrying in 1 second...
Executing HTTP request...
... (in an endless loop) 
```

Enter fullscreen mode Exit fullscreen mode

虽然这可能会确保任何超时都不会引发异常，但重试——像这样反复肯定不会有助于使该 API 重新启动。这是有问题的，因为如果 API 没有响应，这段代码将会一直循环下去。相反，我们应该分散重试，过一段时间就放弃。

## 放弃:使用`raise`重新引发异常

当一个异常被拯救时，引发的异常对象被传递给`rescue`块。我们可以使用它来从异常中提取数据，比如将消息打印到日志中，但是我们也可以使用它来重新引发完全相同的异常，使用相同的堆栈跟踪。

```
begin
  raise "Exception!"
rescue RuntimeError => e
  puts "Exception happened: #{e}"
  raise e
end 
```

Enter fullscreen mode Exit fullscreen mode

因为我们可以访问`rescue`块中的异常对象，所以我们可以将错误记录到控制台或错误监视器中。事实上，拯救和重新提升正是 AppSignal 整合跟踪错误的。

*注意* : Ruby 将最后引发的异常存储在一个名为`$!`的变量中，`raise`关键字会默认使用它。不带任何参数调用`raise`将引发最后一个异常。

在我们的库中，我们可以在几次重试后使用重提升来承受 API 的压力。为此，我们将在`retries`变量中记录我们的重试次数。

每当超时发生时，我们将增加数字并检查它是否小于或等于 3，因为我们希望最多重试三次。如果是这样，我们就`retry`。如果没有，我们将`raise`重新引发上一个异常。

```
require "net/http"

http = nil
uri = URI("http://localhost:4567/")
retries = 0

begin
  unless http
    puts "Opening TCP connection..."
    http = Net::HTTP.start(uri.host, uri.port, read_timeout: 1)
  end
  puts "Executing HTTP request..."
  puts http.request_get(uri.path).body
rescue Errno::ECONNREFUSED, Net::ReadTimeout => e
  if (retries += 1) <= 3
    puts "Timeout (#{e}), retrying in #{retries} second(s)..."
    sleep(retries)
    retry
  else
    raise
  end
ensure
  if http
    puts 'Closing the TCP connection...'
    http.finish
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

通过在对`sleep`的调用中使用`retries`变量，我们可以增加每次新尝试的等待时间。

```
$ ruby reraise.rb
Opening TCP connection...
Executing HTTP request...
Timeout (Net::ReadTimeout), retrying in 1 second(s)...
Executing HTTP request...
Timeout (Net::ReadTimeout), retrying in 2 second(s)...
Executing HTTP request...
Timeout (Net::ReadTimeout), retrying in 3 second(s)...
Executing HTTP request...
Closing the TCP connection...
/lib/ruby/2.4.0/net/protocol.rb:176:in `rbuf_fill': Net::ReadTimeout (Net::ReadTimeout)
...
from reraise.rb:13:in `<main>' 
```

Enter fullscreen mode Exit fullscreen mode

在代码放弃并重新引发最后一个错误之前，我们的请求被重试了三次。然后，我们可以更上一层楼处理这个错误，或者如果没有 API 的响应，我们的应用程序无法完成任务，我们的应用程序就会崩溃。

## 一个弹性的 web API 客户端

通过组合这些方法，我们已经用大约二十行代码构建了一个有弹性的 web API 客户端。如果它关闭或没有响应，它将重试请求，当它不再恢复时，我们将放弃。

我们希望您学到了一些关于处理异常的新知识，并且很想知道您对这篇文章的看法(或者是 [AppSignal Academy 系列](https://blog.appsignal.com/category/academy.html)中的任何其他文章)。请不要犹豫[让我们知道](https://twitter.com/appsignal)你的想法，或者如果你有任何想要了解更多的 Ruby 主题。