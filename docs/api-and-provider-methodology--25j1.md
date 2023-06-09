# API 和提供商方法

> 原文：<https://dev.to/thermatix/api-and-provider-methodology--25j1>

# 简介

因此，设计一个库/框架时最大的问题之一是当你需要调用一个外部依赖时。

特别是在这篇文章的上下文中，当这种依赖由于某种原因不再可用时，你会怎么做？大多数人需要为新的依赖关系实现一个新的接口，因此可能会对 API 进行重大修改以适应不好的情况。

我提到这一点是因为我经常看到所有的库直接调用 API 方法中的外部依赖关系(我对此也感到内疚-_-)，然后当依赖关系改变时，必须完全改变 API，这需要不同的数据结构。

这对许多人来说是个打击。

这就是为什么我主张将 API 和外部调用分离到单独的类中，这确实增加了复杂性和代码量，但好处是当你的外部依赖性改变时，你的 API 不会改变，使用你的库的任何人也不会改变。

当我想到要这样做时(咳咳),我喜欢把逻辑分成:

*   API:你的库的 API

*   提供者:将您的调用为外部依赖的逻辑

*   格式化程序:如果需要，处理 API 和提供者之间的数据格式化的逻辑

# 基本提供者逻辑

现在你可以用任何你喜欢的方式来做这件事，只要有一个清晰的关注点分离。以 ruby 为例，我喜欢利用你可以定义`def self.`的事实来使我的提供者 API 易于使用，所以我可以做这样的事情:

```
Module Providers
    class << self
        attr_writer :listing
        def 
            @listing[provider]       
        end
    end
    class Provider_Base
        def self.inherited(child, name = child.class.to_s.downcase.to_sym)
            # Normally I'd add logic to ensure that only this method can add to
            # `Providers.listing`, but I can't be arsed for this example
            Providers.listing[name] = child
        end
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

我在函数声明中使用`name = child.class.to_s.downcase.to_sym`的原因是，如果你愿意，你可以通过使用
来改变名字的设置方式

```
Module Providers
    class OtherProviderBase < Provider_Base
        def self.inherited(child)
            super(child, :some_other_naming_mechaism)
        end
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以改为从`OtherProviderBase`继承。

# 使用您的提供商

现在在你的 api 中使用提供者变得非常容易:

```
class API

    PROVIDER_NAME = :provider_name

    def some_method
        Providers[PROVIDER_NAME].some_method
    end

end 
```

Enter fullscreen mode Exit fullscreen mode

为什么会这样？假设您构建了一个新的依赖项提供者，您可以将提供者更改为:

```
class API

    PROVIDER_NAME = :other_provider_name

    def some_method
        Providers[PROVIDER_NAME].some_method
    end

end 
```

Enter fullscreen mode Exit fullscreen mode

最终结果是 API 不会改变，也不会对你的用户造成破坏。

使用上面的提供者示例，您可以像这样添加格式化程序:

```
class API
    PROVIDER_NAME = :provider_name

    def some_method
        Formatters[PROVIDER_NAME].some_method(
            Providers[PROVIDER_NAME].some_method)
    end

end 
```

Enter fullscreen mode Exit fullscreen mode

你甚至可以更进一步，这样做:

```
class API

    PROVIDER_NAME = :provider_name

    def some_method(*args)
        method = :some_method
        output = Providers[PROVIDER_NAME].send(method, (Formatters::Input[PROVIDER_NAME].send(method,*args))
        Formatters::Output[PROVIDER_NAME].send(method, output)
    end

end 
```

Enter fullscreen mode Exit fullscreen mode

# 供应商管道

您甚至可以提供一种机制(当然是在适当的时候)来自动进行管道传输，就像下面这样:

```
module Pipelines
    attr_reader :listing

    def pipline_for(action, *pipes)
        @listing[action] = pipes
        define_method(action, do) |*args|
            run_pipline_for(action, *args)
        end
    end

    def self.extended(child)
        child.include Instance_Methods
    end

    module Instance_Methods
       def run_pipline_for(action, *args)
           Pipelines.listing[action].inject(args) do |result, (item, custom_action)|
              item[PROVIDER_NAME].send(custom_action || action, *result)
           end
       end
    end
end

class API
    extend Pipeline

    PROVIDER_NAME = :provider_name

    pipline_for :action, Formatters::Input, [Providers, :get_remote_data], Formatters::Output
    pipline_for :other_action, Formatters::Input, Providers, [Processors, :differing_name], [Formatters::Output, :custom_output]

end 
```

Enter fullscreen mode Exit fullscreen mode

> 抱歉，这可能超出了这篇文章的范围，但是当我看到一些有趣的代码时，我控制不住自己。我也不确定当`Pipeline`包含在内时`PROVIDER_NAME`是否会在`API`类的本地上下文中被选中，所以你可能需要编写一个设置和检索的机制，但是你明白了。

# 结论

通过将您的外部依赖项分离到它的逻辑中，然后由您的 API 调用，您就可以在不同版本之间保持您的 API 不变，然后版本之间唯一的差异就变成了依赖项，它们之间没有任何破坏性的变化。

附言:我完全承认没有检查或测试过这些代码，因为它们只是一些例子。