# Ruby 的神奇可枚举模块

> 原文：<https://dev.to/appsignal/rubys-magical-enumerable-module-7j2>

又到了一集[红宝石魔法](https://blog.appsignal.com/category/ruby-magic.html?utm_source=DevTo&utm_medium=DevToBlogPost&utm_campaign=DevToContentPosting_Enumerable)的时候了！这一次，我们将看看 Ruby 最神奇的特性之一，它提供了在使用 Ruby 的可枚举类如`Array`、`Hash`和`Range`时使用的大多数方法。在这个过程中，我们将学习如何处理可枚举对象，枚举是如何工作的，以及如何通过实现一个方法来使对象可枚举。

## `Enumerable``#each``Enumerator`

*枚举*指遍历对象。在 Ruby 中，当一个对象描述了一组条目和一个遍历每个条目的方法时，我们称它为可枚举的*。*

内置的可枚举数通过包含`Enumerable`模块来获得它们的枚举特性，该模块提供了诸如`#include?`、`#count`、`#map`、`#select`和`#uniq`等方法。大多数与数组和散列相关的方法实际上并没有在这些类中实现，而是被包含在内。

*注意*:有些方法，比如`Array`类上的`#count`和`#take`，*是专门为数组实现的*，而不是使用`Enumerable`模块中的方法。这样做通常是为了加快操作速度。

[`Enumerable`模块](http://ruby-doc.org/core-2.5.1/Enumerable.html)依赖于一个名为`#each`的方法，该方法需要在包含它的任何类中实现。当用数组上的块调用时，`#each`方法将为数组的每个元素执行块。

```
irb>  [1,2,3].each { |i| puts "* #{i}" }
* 1
* 2
* 3 => [1,2,3] 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在数组*上调用`#each`方法，而*没有传递一个块来执行它的每个元素，我们将接收到一个`Enumerator`的实例。

```
irb>  [1,2,3].each
=> #<Enumerator: [1, 2, 3]:each> 
```

Enter fullscreen mode Exit fullscreen mode

`Enumerator`的实例描述了如何迭代一个对象。枚举器[手动迭代对象](https://ruby-doc.org/core-2.5.1/Enumerator.html#method-i-next)并链式枚举。

```
irb>  %w(dog cat mouse).each.with_index { |a, i| puts "#{a} is at position #{i}" }
dog is at position 0
cat is at position 1
mouse is at position 2 => ["dog", "cat", "mouse"] 
```

Enter fullscreen mode Exit fullscreen mode

`#with_index`方法是一个很好的例子，说明了变化的枚举器是如何工作的。在本例中，对数组调用了`#each`以返回一个枚举数。然后，`#with_index`被调用来向数组的每个元素添加索引，以允许打印每个元素的索引。

## 使对象可枚举

在幕后，像`#max`、`#map`和`#take`这样的方法依赖于`#each`方法来运行。

```
def max
  max = nil

  each do |item|
    if !max || item > max
      max = item
    end
  end

  max
end 
```

Enter fullscreen mode Exit fullscreen mode

在内部，`Enumerable`的方法有 C 实现，但是上面的例子粗略地展示了`#max`是如何工作的。通过使用`#each`循环所有值并记住最大值，它返回最大值。

```
def map(&block)
  new_list = []

  each do |item|
    new_list << block.call(item)
  end

  new_list
end 
```

Enter fullscreen mode Exit fullscreen mode

`#map`函数对每个项目调用传递的块，并将结果放入一个新的列表中，在遍历所有值后返回。

因为`Enumerable`中的所有方法都在某种程度上使用了`#each`方法，所以我们使自定义类可枚举的第一步是实现`#each`方法。

## 实施`#each`

通过实现`#each`函数并将`Enumerable`模块包含在一个类中，它变得可枚举，并免费接收像`#min`、`#take`和`#inject`这样的方法。

尽管大多数情况下允许退回到一个现有的对象，比如一个数组，并在其上调用`#each`方法，但是让我们看一个例子，在这个例子中，我们必须自己从头开始编写它。在这个例子中，我们将在一个*链表*上实现`#each`，使其可枚举。

### 链表:没有数组的列表

一个[链表](https://en.wikipedia.org/wiki/Linked_list)是数据元素的集合，其中每个元素指向下一个。列表中的每个元素都有两个值，名为*头*和*尾*。头部保存元素的值，尾部是到列表其余部分的链接。

```
[42, [12, [73, nil]]
```

对于具有三个值(42、12 和 73)的链表，第一个元素的头是 42，尾是到第二个元素的链接。二次元的头是 12，尾托着三次元。第三个元素的头是 73，尾是`nil`，表示列表结束。

在 Ruby 中，可以通过使用包含两个实例变量`@head`和`@tail`的类来创建链表。

```
class LinkedList
  def initialize(head, tail = nil)
    @head, @tail = head, tail
  end

  def <<(item)
    LinkedList.new(item, self)
  end

  def inspect
    [@head, @tail].inspect
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

`#<<`方法用于向列表中添加新值，其工作原理是返回一个新列表，以传递的值为头，以前的列表为尾。

在这个例子中，添加了`#inspect`方法，这样我们可以查看列表，检查它包含哪些元素。

```
irb>  LinkedList.new(73) << 12 << 42
=> [42, [12, [73, nil]]] 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个链表，让我们在它上面实现`#each`。`#each`函数获取一个块，并为对象中的每个值执行它。当在我们的链表上实现它时，我们可以利用链表的递归特性，在链表的`@head`上调用传递的块，在`@tail`上调用`#each`，如果它存在的话。

```
class LinkedList
  def initialize(head, tail = nil)
    @head, @tail = head, tail
  end

  def <<(item)
    LinkedList.new(item, self)
  end

  def inspect
    [@head, @tail].inspect
  end

  def each(&block)
    block.call(@head)
    @tail.each(&block) if @tail
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

当在我们的链表实例上调用`#each`时，它用当前的`@head`调用传递的块。然后，它调用`@tail`中链表上的每个，除非尾部是`nil`。

```
irb>  list = LinkedList.new(73) << 12 << 42
=> [42, [12, [73, nil]]]
irb>  list.each { |item| puts item }
42
12
73 => nil 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的链表响应了`#each`，我们可以`include Enumberable`使我们的链表可枚举。

```
class LinkedList
  include Enumerable

  def initialize(head, tail = nil)
    @head, @tail = head, tail
  end

  def <<(item)
    LinkedList.new(item, self)
  end

  def inspect
    [@head, @tail].inspect
  end

  def each(&block)
    block.call(@head)
    @tail.each(&block) if @tail
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
irb>  list = LinkedList.new(73) << 12 << 42
=> [42, [12, [73, nil]]]
irb(main):003:0>  list.count
=> 3
irb>  list.max
=> 73
irb>  list.map { |item| item * item }
=> [1764, 144, 5329]
irb>  list.select(&:even?)
=> [42, 12] 
```

Enter fullscreen mode Exit fullscreen mode

## 返回`Enumerator`实例

我们现在可以循环链表中的所有值，但是我们还不能链接可枚举的函数。要做到这一点，我们需要在没有阻塞的情况下调用我们的`#each`函数时返回一个`Enumerator`实例。

```
class LinkedList
  include Enumerable

  def initialize(head, tail = nil)
    @head, @tail = head, tail
  end

  def <<(item)
    LinkedList.new(item, self)
  end

  def inspect
    [@head, @tail].inspect
  end

  def each(&block)
    if block_given?
      block.call(@head)
      @tail.each(&block) if @tail
    else
      to_enum(:each)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

为了在枚举器中包装一个对象，我们对它调用`#to_enum`方法。我们传递`:each`，因为这是枚举器应该在内部使用的方法。

现在，在没有块的情况下调用我们的`#each`方法将允许我们链式枚举。

```
irb(main):226:0>  list = LinkedList.new(73) << 12 << 42
=> [42, [12, [73, nil]]]
irb(main):227:0>  list.each
=> #<Enumerator: [42, [12, [73, nil]]]:each>
irb(main):228:0>  list.map.with_index.to_h
=> {42=>0, 12=>1, 73=>2} 
```

Enter fullscreen mode Exit fullscreen mode

## 九行代码和一个包含

通过使用`Enumerable`模块实现`#each`并从我们自己的模块返回`Enumerator`对象，我们能够通过添加九行代码和一个 include 来增强我们的链表。

这就结束了我们对 Ruby 中可枚举性的概述。如果你喜欢这篇文章，可以看看关于 Ruby Magic 的整个系列。魔术师从不分享他们的秘密。但是我们有。

我们很想知道您对这篇文章的看法，或者您是否有任何问题。我们总是在寻找可以研究和解释的话题，所以如果你想了解 Ruby 的神奇之处，请不要犹豫，留下你的评论。