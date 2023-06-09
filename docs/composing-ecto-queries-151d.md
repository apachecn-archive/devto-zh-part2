# 编写 Ecto 查询

> 原文：<https://dev.to/jackmarchant/composing-ecto-queries-151d>

Ecto 是一个 Elixir 库，它允许您定义映射到数据库表的模式。这是一个超轻量的 [ORM](https://en.wikipedia.org/wiki/Object-relational_mapping) (对象关系映射器)，允许你定义结构来表示数据。

当我第一次学习如何使用 Ecto 和 Elixir 本身时，我惊讶地发现，您可以像编写函数一样编写查询。鉴于 Elixir 是一种函数式语言，管道在其中发挥了重要作用，因此很容易理解为什么它是一种表达查询的好方法。

要开始编写 Ecto 查询，您可以导入 Ecto。查询模块。
此查询将获取所有已发行的专辑:

```
query = where(MyApp.Album, released: true) 
```

Enter fullscreen mode Exit fullscreen mode

这将返回一个 Ecto。可查询类型，您可以使用 Repo.all(query)将它直接传递给 Repo(一个处理数据库连接的模块)，或者您可以添加到它:

```
# using our previously defined query
released_with_length = where(query, [q], q.length > 20) 
```

Enter fullscreen mode Exit fullscreen mode

我们能够基于上面的现有查询创建一个全新的查询。如果我们现在将它传递给 Repo.all，我们将得到所有超过 20 分钟的发行专辑。您可能已经注意到，在第一个查询中，我们开始使用 Ecto。模式，在第二个示例中，我们使用了第一个查询。这是因为这两个结构都实现了 queryable 协议，本质上是让 Ecto 知道我们可以用它来查询数据。

## 带连接的查询

伴随着巨大的查询而来的是巨大的责任，幸运的是，Ecto 使连接变得很容易，不费吹灰之力。假设我们也有一个 songs 表，每条记录都有一个 album_id 来与一个专辑相关联。
如果我们想要获得一个专辑列表，其中该专辑中的歌曲长于特定的秒数，我们可以使用下面的查询:

```
defmodule EctoQueries do
  @moduledoc """
  Composing Ecto Queries.
  """

  alias EctoQueries.{Repo, Song, Album}

  import Ecto.Query

  @doc """
  Find all songs with length greater than `length`
  """
  @spec find_songs_longer_than(integer()) :: list(Song.t())
  def find_songs_longer_than(length) do
    Song
    |> where([song], song.length > ^length)
    |> find_all()
  end

  @doc """
  Find songs by their album name
  """
  @spec find_songs_by_album_name(String.t()) :: list(Song.t())
  def find_songs_by_album_name(name) do
    Song
    |> join(:inner, [song], album in Album, song.album_id == album.id)
    |> where([_, album], album.name == ^name)
    |> find_all()
  end

  @doc """
  Find all songs
  """
  @spec find_songs :: list(Song.t())
  def find_songs do
    find_all(Song)
  end

  @doc """
  Find all albums
  """
  @spec find_albums :: list(Album.t())
  def find_albums do
    find_all(Album)
  end

  defp find_all(queryable) do
    Repo.all(queryable)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了一些事情，但是主要部分是使用一个函数来连接 songs 表，并限定专辑查询的范围，只返回歌曲长度超过某个整数的专辑。这种模式对于将较低层次的查询抽象成更小的部分很有用，因此您可以将它们连接到一个具有更多上下文的函数中。通常情况下，您可能以前对函数做过这种操作，但是每个函数调用本身都会进入数据库，并且您会使用一个可枚举来过滤结果。

这种类型的组合是通过 Ecto 查询绑定实现的。这些是对已添加到查询中的模式的引用，在列表中的排序方式与它们的添加方式相同。查询绑定中的顺序很重要，这使得跨不同函数进行多重连接变得很困难，就像我们以前将查询拆分成函数一样。

## 示例应用程序-亲自试用

我构建了一个小应用程序来展示这一切如何在应用程序中协同工作。我会鼓励你克隆它并检查它。没有太多的资源可以开始使用 Elixir，但是这个应用程序可能会向您展示如何进行简单的工作，同时还会展示一些更深入的示例，说明组合在 Ecto 和 Elixir 中是多么强大。

它也有测试，所以你可以修改查询并运行`mix test`，看看你是否破坏了什么。

这里是:[在 Github 上编写 Ecto 查询](https://github.com/jackmarchant/composing-ecto-queries)