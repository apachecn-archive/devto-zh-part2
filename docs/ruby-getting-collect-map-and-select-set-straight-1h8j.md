# Ruby:获取收集、映射和选择设置直线

> 原文：<https://dev.to/morinoko/ruby-getting-collect-map-and-select-set-straight-1h8j>

在我的一个 Flatiron 编码挑战中，我必须做一个字谜测试器来测试数组中的哪些单词是目标单词的字谜。可能的字谜通过数组传递给了一个`#match`方法。然后在变位词对象上调用`#match`方法，并“收集”所有匹配的单词作为变位词。比如:

```
listen = Anagram.new("listen")
listen.match(%w(enlists google inlets banana))

# => ["inlets"] 
```

Enter fullscreen mode Exit fullscreen mode

因为我想“收集”所有的匹配，起初我试图在数组上使用`#collect`。

```
class Anagram
  attr_accessor :word

  def initialize(word)
    @word = word
    @letters = @word.split("").sort
  end

  # find anagram matches given an array of words
  def match(possible_anagrams)
    possible_anagrams.collect do |possible_anagram|
      @letters == possible_anagram.split("").sort
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

然而，`#collect`的问题在于，它收集了*块*中代码产生的所有返回值，所以我得到了一个数组，其中充满了块表达式返回的值。

随着`#collect`，`listen.match(%w(enlists google inlets banana))`正在返回`=> [false, false, true, false]`。我真正需要的是*获取数组中使代码块返回 true 的项目*。

猜猜谁能做到？你猜对了:`#select`！将`#collect`换成`#select`解决了问题:

```
class Anagram
  attr_accessor :word

  def initialize(word)
    @word = word
    @letters = @word.split("").sort
  end

  # find anagram matches given an array of words
  def match(possible_anagrams)
    possible_anagrams.select do |possible_anagram|
      @letters == possible_anagram.split("").sort
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，只有使块为真的项目才会包含在返回的数组中:=> ["inlets"]。

如果您查看 Ruby 文档，这种差异会非常明显:

*   [#collect](https://ruby-doc.org/core-2.2.0/Array.html#method-i-collect) :"创建一个新数组，包含块返回的值。"
*   [#select](https://ruby-doc.org/core-2.2.0/Array.html#method-i-select) :"返回一个新数组，包含数组中给定块返回真值的所有元素。"

好吧，那么`#map`呢？原来#map 和#collect 是完全一样的东西。很多时候你会听到某些函数做同样的事情，但实际上有细微的差别。然而，在`#map`和`#collect`的例子中，它们的源代码完全相同。点击[#收藏](https://ruby-doc.org/core-2.2.0/Array.html#method-i-collect)和[#地图](https://ruby-doc.org/core-2.2.0/Array.html#method-i-map)文档下的“点击切换来源”自行查看！

*编辑:根据下面的讨论评论重构的初始代码(谢谢！)*