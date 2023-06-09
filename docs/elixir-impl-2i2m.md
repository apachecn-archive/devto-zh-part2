# elixir :利用@ impl 属性吧

> 原文：<https://dev.to/gumi/elixir-impl-2i2m>

本文以“[Elixir 1.5 中追加的@impl 活用](https://qiita.com/melpon/items/37d430f0551e62ae035c)”为基础进行了修改，整理了文章。

[`@impl`](https://hexdocs.pm/elixir/Module.html#module-impl)是为了表示行为已被正确安装而添加的模块属性。 明确表示行为的回调。 介绍从 Elixir 1.5 开始提供的这个属性。

# `@impl`什么是属性

## 不使用`@impl`的代码

`@impl`属性是可选的。 即使不使用此属性，也可以编译时不发出任何警告，如下所示。

```
defmodule MyApp do
  @behaviour Plug

  def init(opts) do
    opts
  end

  def call(conn, _opts) do
    Plug.Conn.send_resp(conn, 200, "hello")
  end
end 
```

代码中的`init/1`和`call/2`安装了[`Plug`行为](https://hexdocs.pm/plug/readme.html)的回调函数。 但是，只从代码上看不出这一点。 `Plug`必须事先知道行为要求的回调函数是什么。

## `@impl`宣言

因此，将`@impl`属性声明如下。 然后，读代码的人知道“这个函数是回调函数的实现”，变成了非常温柔的状态。

```
defmodule MyApp do
  @behaviour Plug

  @impl Plug  # 追加
  def init(opts) do
    opts
  end

  @impl Plug  # 追加
  def call(conn, _opts) do
    Plug.Conn.send_resp(conn, 200, "hello")
  end
end 
```

即使这样，作为`@impl`的有用性也足够了<sup id="fnref1">[1](#fn1)</sup> 。 但是，还有一个次要的效果。

## 保持一贯性

即使只声明一个`@impl`，如果所有的回调函数都不使用`@impl`，编译时就会发出警告。

```
# ひとつのコールバック関数から@implの定めを外す
# @impl Plug
def call(conn, _opts) do
  Plug.Conn.send_resp(conn, 200, "hello")
end 
```

编译结果:

```
warning: module attribute @impl was not set for function call/2 callback (specified in Plug). This either means you forgot to add the "@impl true" annotation befor
e the definition or that you are accidentally overriding this callback
  lib/my_app.ex:11 
```

相反，如果在非回调函数中添加`@impl`，也会显示警告。

```
# コールバックをcallでなくca11と記述
@impl Plug
def ca11(conn, _opts) do
  Plug.Conn.send_resp(conn, 200, "hello")
end 
```

编译结果:

```
warning: got "@impl Plug" for function ca11/2 but this behaviour does not specify such callback. The known callbacks are:

  * Plug.call/2 (function)
  * Plug.init/1 (function)

  lib/my_app.ex:11

warning: function call/2 required by behaviour Plug is not implemented (in module MyApp)
  lib/my_app.ex:1 
```

有两个警告。 这是由于前者写错了`@impl`而引起的警告。 后者告知，虽然加入了`@behaviour Plug`，但没有定义回调`call/2`。

像这样弄错函数名称，或者自变量的数量不同的话，即使没有`@impl`也会发出警告。 不过，前者的警告信息可以说更容易理解吧。

而且，也可以考虑用`@behaviour`无法防止的情况。 例如，假设`Foo`行为请求`foo/0`回调函数，并且有以下模块:

```
defmodule MyApp do
  @behaviour Foo

  @impl Foo
  def foo() do
    "fooooo"
  end

  def bar() do
    "baaaar"
  end
end 
```

此时，`bar/0`不是回调，只是函数。 但是，根据之后的版本升级，可能会在`Foo`行为中添加`bar/0`回调函数。 于是，就会出现没有在`bar/0`上写`@impl`的警告。 如果没有使用`@impl`的话，不会发出任何警告。 即使偶然与现有函数的名称一致来进行编译，恐怕也不会成为想要的行为。

像这样，今后回调和被冠以名字的函数也可以放心地编写。 根据`@impl`可以保持一贯性。

## 自动变成`@doc false`

回调不是可以自由调用的函数。 因此，在生成文档时，回调函数的实现被设定为不自动放入文档中<sup id="fnref2">[2](#fn2)</sup> 。

# 不用`@impl true`

如果不指定行为而确定`@impl true`，则会自动判断是哪个行为的实现。 但是，还是不要使用这个功能比较好吧。 因为写了`@behaviour`，所以你可能会想，`@impl true`应该很快就会知道是什么行为。

但是，例如，在以下代码中，我不知道`bun/0`函数是哪个行为的实现。 要知道这一点，需要从`Foo.Bar.__using__/1`左右开始看，调查安装了哪个行为。

```
defmodule MyApp do
  use Foo.Bar

  @impl true
  def bun() do
    "cho"
  end
end 
```

使用`@impl`是以容易阅读为目的的。 如果知道是回调函数，但不知道是哪个行为的实现，则这是没有意义的。 只要使用了一个`use`，就可以决定不能使用`@impl true`。 但是，与其制造那样的例外，不如制定经常写行为的名字的规则吧。

# 在模式匹配的情况下，给所有的回调函数附上`@impl`

使用模式匹配时，会多次出现同一函数的规定。 如果那个函数是回调的话，所有的定义都应该加上`@impl`。

```
# コールバック関数への宣言
@impl GenServer
def handle_call(:get_value, _from, state) do
  # ...
end

# すべての同名関数に宣言する
@impl GenServer
def handle_call({:set_value, value}, _from, state) do
  # ...
end 
```

作为`@impl`的功能，只要添加到任意一个中效果就相同。 但是，为了便于阅读，最好把一切都定下来。 可以避免给读代码的人不必要的疑问，比如为什么会有或没有。

# 总结

*   `@impl`对于读码的人来说很容易理解，所以要积极使用。
    *   也有次要的效果。
*   不要使用`@impl true`吧。
*   在进行模式匹配的时候，请在所有的函数中写上`@impl`。

* * *

1.  如果只是简单易懂的话，你可能会认为评论也可以。 但是，能以官方统一的形式写是非常重要的。

2.  如果能在模块的文档中自动写下实现了什么行为的话，看起来会更好。 但是，好像没有那样的功能。