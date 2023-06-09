# 当数组不是数组时

> 原文：<https://dev.to/tbrisker/when-an-array-is-not-an-array-49hg>

> 一个简单数组排序出错的故事

工头项目发布过程的一部分包括从源代码中提取所有要翻译的字符串，并从我们使用的名为 [Transifex](https://www.transifex.com/) 的在线服务中提取更新的翻译。作为版本 1.19.0 发布过程的一部分，我运行了 rake 任务来处理这个步骤。

不幸的是，它失败了，并出现一个奇怪的错误消息:

```
ArgumentError: comparison of Array with Array failed
/home/tbrisker/.rvm/gems/ruby-2.5.1@foreman/gems/gettext-3.2.9/lib/gettext/po.rb:270:in `sort_by’
/home/tbrisker/.rvm/gems/ruby-2.5.1@foreman/gems/gettext-3.2.9/lib/gettext/po.rb:270:in `sort_by_msgid’
/home/tbrisker/.rvm/gems/ruby-2.5.1@foreman/gems/gettext-3.2.9/lib/gettext/po.rb:227:in `sort’
/home/tbrisker/.rvm/gems/ruby-2.5.1@foreman/gems/gettext-3.2.9/lib/gettext/po.rb:208:in `to_s’
/home/tbrisker/.rvm/gems/ruby-2.5.1@foreman/gems/gettext-3.2.9/lib/gettext/tools/msgcat.rb:57:in `run’
/home/tbrisker/.rvm/gems/ruby-2.5.1@foreman/gems/gettext-3.2.9/lib/gettext/tools/msgcat.rb:32:in `run’
/home/tbrisker/.rvm/gems/ruby-2.5.1@foreman/gems/gettext-3.2.9/lib/gettext/tools/task.rb:391:in `block in define_po_file_task’
/home/tbrisker/.rvm/gems/ruby-2.5.1@foreman/gems/gettext_i18n_rails-1.8.0/lib/gettext_i18n_rails/tasks.rb:65:in `block (2 levels) in <top (required)>’
/home/tbrisker/.rvm/gems/ruby-2.5.1@foreman/gems/rake-12.3.1/exe/rake:27:in `<top (required)>’ 
```

Enter fullscreen mode Exit fullscreen mode

好吧，让我们看看失败函数的代码:

```
def sort_by_msgid(entries)
  entries.sort_by do |msgid_entry|
    # msgid_entry = [[msgctxt, msgid], POEntry]
    msgid_entry[0]
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

嗯…这很奇怪，看起来像一个相当简单的排序函数。查看 gem 的 git [历史](https://github.com/ruby-gettext/gettext/blame/master/lib/gettext/po.rb#L268)表明这个功能已经很多年没有改变了——那么为什么现在它失败了呢？这个评论看起来相当隐晦，如果不深究宝石的内部，很难理解它的含义。是时候拿出 ol' debugger 并在函数中插入一个`binding.pry`断点来尝试理解这里发生了什么。

这是一个 msgid_entry 对象的样子:

```
[[nil, "Failed to fetch: "],
 #<GetText::POEntry:0x00000000144a3a80
  @extracted_comment="",
  @flags=[],
  @msgctxt=nil,
  @msgid="Failed to fetch: ",
  @msgid_plural=nil,
  @msgstr="Falha ao buscar:",
  @param_number=0,
  @param_type=[:msgid, :separator, :msgstr],
  @previous="",
  @references=["../app/assets/javascripts/application.js:74"],
  @translator_comment="",
  @type=:normal>] 
```

Enter fullscreen mode Exit fullscreen mode

它是一个由两个元素组成的数组——第一个，数组本身有两个元素，第二个是一个`GetText::POEntry`对象，它是某种表示翻译的内部 gettext 对象。现在代码中的注释更有意义了——数组包含一个消息上下文(在本例中为`nil`)和一个消息 ID，在本例中为字符串`"Failed to fetch: "`。排序函数根据这个数组的值对条目进行排序(`msgid_entry[0]`)。因此，由于某种原因，这个数组中的一个(或多个)条目包含一个有问题的值。

在循环中进行一些迭代不会导致异常的出现，并且考虑到列表中有超过 3，000 个条目(假设每个需要翻译的字符串都有一个)，手动查找有问题的条目可能会非常困难。

在对条件断点进行了一段时间的试验后，找到了罪魁祸首:

```
[["(%s host)", "(%s hosts)"],
 #<GetText::POEntry:0x000000000f1ea780
  @extracted_comment="",
  @flags=[],
  @msgctxt="(%s host)",
  @msgid="(%s hosts)",
  @msgid_plural=nil,
  @msgstr=nil,
  @param_number=0,
  @param_type=[:msgctxt, :msgid, :msgstr],
  @previous="",
  @references=["../webpack/assets/javascripts/react_app/components/factCharts/index.js:86"],
  @translator_comment="",
  @type=:msgctxt>] 
```

Enter fullscreen mode Exit fullscreen mode

对于第一个数组的第一个元素——消息上下文，这是唯一一个除了`nil`之外还有其他内容的条目。为了使我的工作更容易，第二个元素包含了对字符串来源的确切代码行的引用。

查看代码，[修复](https://github.com/theforeman/foreman/pull/5860/files#diff-e44063a1ea27ac907ae659670db4eb54R86)很明显——有人打错了字，使用了不正确的方法来标记要翻译的字符串。因此，没有使用需要将字符串标记为需要单数和复数翻译的方法，而是调用了常规翻译方法，该方法显然接受了上下文的附加参数。

随着问题的发现和修复，还有一个问题——为什么失败并出现神秘的`comparison of Array with Array failed`错误消息？

为了理解这一点，我们需要深入了解一下 Ruby 的`sort_by`函数是如何工作的。简单地说，它接受一个块，该块将排序对象中的元素作为其参数，并根据块的返回值对它们进行排序。在我们的例子中，条目按照每个条目中第一个元素的值进行排序——包含消息上下文和 ID 的数组。

但是 Ruby 如何对数组进行排序呢？原来它是通过一次比较一个元素来排序的。为了比较元素，它调用函数`<=>`，该函数返回-1、0 或 1，表示第一个元素是小于、等于还是大于另一个元素。但是，如果两个元素不能比较，它也可以返回`nil`，这正是这里发生的情况。

为了说明，让我们来看看当我们比较两个字符串时会发生什么:

```
[1] pry(main)> 'a' <=> 'foo'
=> -1 
```

Enter fullscreen mode Exit fullscreen mode

因为`a`在词汇上小于`foo`，所以函数返回-1。但是当我们试图用`nil`来比较一个字符串时会发生什么呢？

```
[2] pry(main)> nil <=> 'foo'
=> nil 
```

Enter fullscreen mode Exit fullscreen mode

正如所料，由于 Ruby 不知道如何比较这两种不同的对象类型，所以它返回`nil`。但是这和我们的错误有什么关系呢？如果你还记得，Ruby [通过迭代数组元素来比较数组](https://docs.ruby-lang.org/en/2.5.0/Array.html#method-i-3C-3D-3E)。现在让我们看看如果它试图比较包含不能比较的元素的数组会发生什么:

```
[3] pry(main)> [[nil, 'foo'], ['bar', 'baz']].sort
ArgumentError: comparison of Array with Array failed
from (pry):4:in `sort' 
```

Enter fullscreen mode Exit fullscreen mode

其中一个被排序的数组的第一个元素是一个字符串，而另一个是`nil`。这意味着 Ruby 不知道如何对这些数组进行排序，从而导致错误。不出所料，因为这正是我们翻译时遇到的情况，一个数组包含一个字符串，而所有其他数组都有`nil`，所以出现了与我们在本例中尝试排序时相同的错误。