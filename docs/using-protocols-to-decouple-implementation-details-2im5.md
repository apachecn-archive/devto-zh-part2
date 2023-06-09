# 使用协议来分离实现细节

> 原文：<https://dev.to/jackmarchant/using-protocols-to-decouple-implementation-details-2im5>

协议是在 Elixir 中实现多态性的一种方式。我们可以使用它将一个函数应用于多个对象类型或结构化数据类型，这些类型特定于对象本身。有两个步骤；以函数的形式定义协议，以及该协议的一个或多个实现。

你可能以前在 Elixir 中或者作为其他语言的接口见过这个例子:

```
defprotocol Area do
  @doc "Calculate the area for a given object"
  def area(object)
end

defimpl Area, for: Rectangle do
  def area(rectangle) do
    rectangle.width * rectangle.length
  end
end

defimpl Area, for: Circle do
  def area(circle) do
    :math.pow(circle.radius * :math.pi, 2)
  end
end

# These are arbritrary shape structs, but ignoring that fact, 
# we have defined a protocol and a couple of implementations. 
# Usage is then as easy as:

iex> Area.area(%Rectangle{width: 5, length: 3})
15

iex> Area.area(%Circle{radius: 5})
246.74011002723395 
```

Enter fullscreen mode Exit fullscreen mode

## 什么是多态性

**[来源:维基百科](https://en.wikipedia.org/wiki/Polymorphism_(computer_science))**

> 多态性是为不同类型的实体提供单一接口。

我认为这个定义最好地解释了 Elixir 协议中的 Polymorhism，因为您定义了一个协议作为不同结构化数据类型的接口，将实现与调用代码分开。

多态性的目标是围绕如何在应用程序中使用类型来定义抽象，包括能够对它们执行哪些操作或功能。这些抽象允许你的代码从不相关的实现细节中分离出来。

在 Elixir 中，这意味着我们可以定义特定协议的实现，然后在这些对象类型中的任何一个上调用协议函数，而不需要在运行时知道它是哪个对象。

## 用例

您可能会发现自己所处的典型情况是希望将内部数据结构转换为外部数据结构，也许是为了在 API 调用中使用。
假设我们想将一个(人为的)`User`结构转换成一个`ExternalUser`结构，但是我们的调用代码应该是泛型的，这样它也可以用来转换其他类型。

```
# lib/my_app/protocols/external.ex
defprotocol MyApp.External do
  @doc "Transform data from internal objects to external"
  def transform(data)
end

# lib/my_app/user/implementations/external.ex
defimpl MyApp.External, for: MyApp.User do
  @doc """
  For our mythical external API, 
  we only need ID and name of the user
  """
  def transform(user) do
    %ExternalUser{
      id: user.id,
      name: user.name,
    }
  end
end

# lib/my_app/api.ex
defmodule MyApp.API do
  @moduledoc """
  Transform data and push it to an external service.
  """

  @doc "Push transformed data with some options"
  def push(data, opts \\ []) do
    data
    |> MyApp.External.transform()
    |> ExternalAPI.push(opts)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们现在已经分离了我们的 API 推送服务，`MyApp.API`不知道它在推送什么，只知道它需要在发出请求之前首先转换结构化数据。

### 可枚举-您已经使用了一个协议

如果您还不知道，如果您一直在使用任何`Enum`函数，比如`Enum.map/2`和`Enum.filter/2`，那么作为第一个参数传递给这些函数的数据类型实现了[可枚举](https://hexdocs.pm/elixir/Enumerable.html)协议。这个特定的协议定义了四个功能，需要为您希望使用的任何类型实现这些功能`reduce/3`、`count/1`、`member?/2`和`slice/1`。你可以在 Github 上的 [Elixir 代码中看到这些函数的定义。](https://github.com/elixir-lang/elixir/blob/v1.7.3/lib/elixir/lib/enum.ex#L1)

关于 Elixir 最伟大的事情之一是，你可以很容易地浏览源代码，看看我们一直使用的标准库是如何在内部实现的。理论上，您可以实现自己的可枚举类型，但我不确定这在实践中有多大用处！