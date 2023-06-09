# 优雅地处理错误

> 原文：<https://dev.to/katpadi/handling-errors-gracefully-2ced>

有时，将错误视为应用程序“有效”事件流的一部分是有意义的。也就是说，当出现随机错误时，预期的错误不一定会使您的代码变得疯狂。

考虑到这一点，我通过挽救 [StandardError](https://ruby-doc.org/core-2.1.5/StandardError.html) 实例并优雅地处理这些错误，创建了一种实现某种“结果对象”的方法。

```
class Gracefully
  class << self
    alias_method :handle, :new
  end

  attr_reader :error, :value

  def initialize
    @value = nil
    @error = nil
    begin
      @value = yield
    rescue => e
      @error = e
    end
  end

  def success?
    error.nil?
  end

  def on_success
    return self unless success?
    result = yield(@value)
    return result if result.is_a?(Gracefully)
    Gracefully.handle { result }
  end

  def on_failure
    return self if success?
    result = yield(@error)
    return result if result.is_a?(Gracefully)
    Gracefully.handle { result }
  end
end 
```

[优雅地. rb](https://gist.github.com/9aac974a760fc3f5f3a287bb75804de6) [观生](https://gist.githubusercontent.com/katpadi/9aac974a760fc3f5f3a287bb75804de6/raw/f2286a1645a66f7867db9abbd9ebf95113e46da3/gracefully.rb)

简而言之，_**_ 是一个结果对象的包装器——它要么是一个成功值，要么是一个错误对象。**

```
result = Gracefully.handle { Order.find 999 } 
```

在上例中，如果 id 为 999 的订单不存在， ***result.success？*** 法必返假。否则，真的。

如果 ***成？*** 为真， ***result.value*** 将包含成功值。否则， ***result.error*** 将保存错误对象本身。

每当调用 Gracefully.handle 时，它将返回一个 Gracefully 对象。如果想得到原始值，可以使用 ***result.value*** 。

## 更多用法

```
result = Gracefully.handle { ComplicatedService.new(foo, bar).process }
if result.success?
  # do success stuff
  use_value(result.value)
else
  # do failure stuff
  log_error(result.error)
end 
```

## 链接用法

```
Gracefully.handle { SomeComplicatedService.new.call }.on_success do |val|
  val.update!(column_name: 'stuff')
end

Gracefully.handle { SomeComplicatedService.new.call }.on_failure do |e|
  do_whatever_you_wish_with_the_error(e)
end 
```

通过使用这个，我可以灵活地处理错误，并且能够从流程流中的故障中恢复。

帖子[优雅地处理错误](http://blog.katpadi.ph/handling-error-gracefully/)首先出现在[卡特帕迪的点](http://blog.katpadi.ph)上。

-
访问我的网站@ [http://katpadi.ph](http://katpadi.ph) 。**