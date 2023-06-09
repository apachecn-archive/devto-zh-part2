# 灵药和我的 ISO-8859-1 字符编码问题

> 原文：<https://dev.to/lobo_tuerto/elixir-and-my-iso-8859-1-character-encoding-problem-5dpa>

* * *

你可以在 [lobotuerto 的 notes - Elixir 和我的 ISO-8859-1 字符编码问题查看这篇文章的最新更新版本。](https://lobotuerto.com/blog/elixir-and-my-iso-8859-1-character-encoding-problem/)

* * *

来自**墨西哥，**我已经为几种语言的字符编码问题纠结了很长时间…

现在，是**长生不老药的**时间。

# 问题

当我阅读[小灵丹妙药& OTP 指南](https://www.manning.com/books/the-little-elixir-and-otp-guidebook)——一个强烈推荐的指南——时，我被 **ID3** 解析器示例程序:
卡住了

```
defmodule ID3Parser do
  def parse(file_name) do
    case File.read(file_name) do
      {:ok, mp3} ->
        mp3_byte_size = byte_size(mp3) - 128

        <<_::binary-size(mp3_byte_size), id3_tag::binary>> = mp3

        <<"TAG",
          title::binary-size(30),
          artist::binary-size(30),
          album::binary-size(30),
          year::binary-size(4),
          _rest::binary>> = id3_tag

        IO.puts "#{artist} - #{title} (#{album}  #{year})"

      _ ->
        IO.puts "Couldn't open #{file_name}"
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

* * *

使用[克莱曼婷](https://www.clementine-player.org/)我为一个名为`some-song.mp3`的文件编辑了 **ID3** 标签。

并把`Éso`作为它的`title`。

我想知道这个程序是否能很好地处理这些问题。它没有。

* * *

当 **ID3** 标签只包含有效的 [ASCII 字符](https://ascii.cl/)时，一切正常，只要我在`title`、`artist`或`album`中放一个*重音字符*，我得到的就是这样的错误:

```
iex(1)> ID3Parser.parse "some-song.mp3"

** (ArgumentError) argument error
    (stdlib) :io.put_chars(:standard_io, :unicode, [
      <<89, 111, 112, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
      0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 32, 45, 32, 201, 115, 111,
      0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ...>>, 10]) 
```

Enter fullscreen mode Exit fullscreen mode

# 解

在对这里的和那里的进行了一些研究之后，然后是一些[错误报告](https://github.com/elixir-lang/elixir/issues/7599) …我发现 **ID3v1** 标签——程序试图解析的标签——理论上应该被编码为 **ISO-8859-1，**也被称为`Latin 1`。

我需要的是[一种方法](http://erlang.org/doc/man/unicode.html#characters_to_binary-3)将这些字节从 **ISO-5589-1** (拉丁文 1)转换成 **UTF-8** (Unicode)，并给`IO.puts`一些它可以毫无问题地打印出来的东西。

我发现在这个 **Erlang** 工厂:

```
:unicode.characters_to_binary(your_string, :latin1) 
```

Enter fullscreen mode Exit fullscreen mode

这是最终的程序，正确地解析了预期编码中的 **ID3v1** 标签——小心，编码是预期的，但不能保证:

```
defmodule ID3Parser do
  def parse(file_name) do
    case File.read(file_name) do
      {:ok, mp3} ->
        mp3_byte_size = byte_size(mp3) - 128

        <<_::binary-size(mp3_byte_size), id3_tag::binary>> = mp3

        <<"TAG",
          title::binary-size(30),
          artist::binary-size(30),
          album::binary-size(30),
          year::binary-size(4),
          _rest::binary>> = id3_tag

        to_convert = [title, artist, album, year]
        [title, artist, album, year] =
          Enum.map(to_convert, fn tag -> from_latin1(tag) end)

        IO.puts "#{artist} - #{title} (#{album}  #{year})"

      _ ->
        IO.puts "Couldn't open #{file_name}"
    end
  end

  defp from_latin1(string) do
    :unicode.characters_to_binary(string, :latin1)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

希望这能帮助其他处于同样困境的人。

# 链接

*   [长生不老药:二进制、字符串和查理斯](https://elixir-lang.org/getting-started/binaries-strings-and-char-lists.html)
*   [moji bake 问题](https://simple.wikipedia.org/wiki/Mojibake)