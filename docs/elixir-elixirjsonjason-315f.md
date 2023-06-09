# Elixir :纯 Elixir 的最快 JSON 解析器/生成器 Jason

> 原文：<https://dev.to/gumi/elixir-elixirjsonjason-315f>

[Jason](https://t.co/Md4qwtxzGf) 是由 Elixir 开发者之一[Michał Muskała](https://michal.muskala.eu/) 用纯粹的 Elixir 编写的最快的 JSON 解析器和生成器。 比其他库，特别是[Poison](https://github.com/devinus/poison#poison) 至少快 2 倍。 也不亚于在 C 中作为 NIF 实现的 jiffy (处理时间控制在两倍左右)。 本文得到 Muskała 先生的许可，基于 Jason 库的[`README.md`](https://github.com/michalmuskala/jason#jason)介绍其内容。

解析器和发生器均完全符合[RFC 8259](https://tools.ietf.org/html/rfc8259) 和[ECMA 404](http://www.ecma-international.org/publications/standards/Ecma-404.htm) 标准。 另外，解析器已经过使用[JSONTestSuite](https://github.com/nst/JSONTestSuite) 的测试。

# 安装

安装此软件包时，请在`mix.exs`的`deps`中加上`jason`作为依存关系。

```
def deps do
  [{:jason, "~> 1.1"}]
end 
```

Enter fullscreen mode Exit fullscreen mode

# 基本用法

Jason 编码和解码的代码例如如下所示。 详情请参考[Jason 的文档](https://hexdocs.pm/jason)。

```
iex> Jason.encode!(%{"age" => 44, "name" => "Steve Irwin", "nationality" => "Australian"})
"{\"age\":44,\"name\":\"Steve Irwin\",\"nationality\":\"Australian\"}"

iex> Jason.decode!(~s({"age":44,"name":"Steve Irwin","nationality":"Australian"}))
%{"age" => 44, "name" => "Steve Irwin", "nationality" => "Australian"} 
```

Enter fullscreen mode Exit fullscreen mode

# 与其他库的使用方法

## Postgrex

在`lib`内的`.ex`文件中，必须根据[`Postgrex.Types.define/3`](https://hexdocs.pm/postgrex/Postgrex.Types.html#define/3)确定自定义的类型。

```
Postgrex.Types.define(MyApp.PostgresTypes, [], json: Jason)

## Ectoで使うときはデフォルト拡張を渡さなければなりません
Postgrex.Types.define(MyApp.PostgresTypes, [] ++ Ecto.Adapters.Postgres.extensions(), json: Jason) 
```

Enter fullscreen mode Exit fullscreen mode

这样，只要将模块交给[`Postgrex.start_link/1`](https://hexdocs.pm/postgrex/Postgrex.html#start_link/1)就可以使用了。

## Ecto

为了在 ecto APP 中完全再现`Poison`的当前动作，必须在`config/config.exs`内将`Jason`设定为默认编码器。

```
config :ecto, json_library: Jason 
```

Enter fullscreen mode Exit fullscreen mode

此外，使用 PostgreSQL 时，请将定制型模块确定为`config/config.exs`或`config/<env>.exs`。

```
config :my_app, MyApp.Repo, types: MyApp.PostgresTypes 
```

Enter fullscreen mode Exit fullscreen mode

## 塞(およびPhoenix)

首先，必须规定为[`Plug.Parsers`](https://hexdocs.pm/plug/Plug.Parsers.html)在 JSON 的分析中使用`Jason`。 然后，决定`Plug.Parsers`插头插在哪里。 如果是 Phoenix，添加到端点模块`lib/app_web/endpoint.ex`的代码例如如下所示。

```
plug Plug.Parsers,
  parsers: [:urlencoded, :multipart, :json],
  pass: ["*/*"],
  json_decoder: Jason 
```

Enter fullscreen mode Exit fullscreen mode

并且，在 Phoenix 中，请将编码器规定为`config/config.exs`，如下所示。

```
config :phoenix, :format_encoders,
  json: Jason 
```

Enter fullscreen mode Exit fullscreen mode

用于 Phoenix 通道的自定义 JSON 编码器需要一些工夫。 关于定制串行化器的代码及其使用方法，请参阅[`json_transport_serializer.ex`](https://gist.github.com/michalmuskala/d5fabcd26be2befdfb72b72e0b0f2797)。

## 苦艾酒

请在[`Absinthe.Plug`](https://hexdocs.pm/absinthe_plug/Absinthe.Plug.html)中确定`:json_codec`选项。

```
# 直に呼び出すとき
plug Absinthe.Plug,
  schema: MyApp.Schema,
  json_codec: Jason

# Phoenixルータで使うとき
forward "/api",
  to: Absinthe.Plug,
  init_opts: [schema: MyApp.Schema, json_codec: Jason] 
```

Enter fullscreen mode Exit fullscreen mode

# 基准测试

包括内存测量在内的基准测试请参阅[`decode.txt`](https://gist.github.com/michalmuskala/4d64a5a7696ca84ac7c169a0206640d5)。 HTML 的性能报告在[`jason/decode.html`](http://michal.muskala.eu/jason/decode.html)和[`jason/encode.html`](http://michal.muskala.eu/jason/encode.html)上公开。

## OK

广泛使用的 Elixir 和 Erlang 的 JSON 库将通过`mix bench.encode`及`mix bench.decode`进行基准测试。 请分别通过`bench/output/encode.html`和`bench/output/decode.html`确认执行基准测试的结果。

# 与 Poison 的不同

Jason 和 Poison 有几个不同的功能。

*   更严格地遵守 JSON 规范。
    *   JSON 字符串中的未转义换行符(例如"\"\n\" )会导致解码错误。
*   不支持解码为数据结构(`:as`选项)。
*   没有`MapSet`和`Range`及`Stream`的嵌入式编码器。
*   不支持对任意结构的编码。
    *   `Jason.Encoder`请明确安装协议。
*   高品质输出(默认`pretty: true`)的定制选项不同。

如果需要不支持的集合类型编码器，建议直接将其添加到项目中。

```
defimpl Jason.Encoder, for: [MapSet, Range, Stream] do
  def encode(struct, opts) do
    Jason.Encode.list(Enum.to_list(struct), opts)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

如果必须对未实现协议的结构进行编码，则可以在实现中明确指定哪些字段为 JSON。

```
@derive {Jason.Encoder, only: [....]}
defstruct # ... 
```

Enter fullscreen mode Exit fullscreen mode

也可以对所有字段进行编码。 但是，添加新字段时，请注意不要意外泄露私人信息。

```
@derive Jason.Encoder
defstruct # ... 
```

Enter fullscreen mode Exit fullscreen mode

# 许可证

Jason 是通过 Apache 2.0 许可证发布的。 详情请参考[LICENSE](https://github.com/michalmuskala/jason/blob/master/LICENSE) 文件。

测试和基准以[Poison 库](https://github.com/devinus/poison)为基础。 该许可证最初被认为是[CC0-1.0](https://creativecommons.org/publicdomain/zero/1.0/) 。