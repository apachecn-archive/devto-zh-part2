# 是鲁比，一定有更好的办法

> 原文：<https://dev.to/rpalo/its-ruby-there-must-be-a-better-way-4f7e>

最近，我在 Ruby track 上做了一个关于[exercisem](https://exercism.io)的挑战，我挣扎了很久，但当我最终找到一个最终解决方案时，我惊讶于 Ruby 的强大让我简化了我的代码。

这是一个非常酷的挑战，所以，我会先列出前提，然后你可以尝试一下。如果你真的很喜欢，这个练习的 Ruby 测试在 exercisem Ruby repo 中[。其他语言的大多数 repos 也有类似的测试套件。这次演习的机器人名字叫做**。**](https://github.com/exercism/ruby/blob/master/exercises/robot-name/robot_name_test.rb)

 **## 前提

您正在创建一系列机器人，每个机器人都有一个唯一的名称。他们的名字都遵循`letter letter number number number`的模式。几个有效的名字可能是`AB123`、`YE801`或`RP100`。这意味着有`26 * 26 * 10 * 10 * 10`可能的名字。所以挑战来了。

1.  写一个创建机器人的类。
2.  创建的每个机器人都必须有一个与上面模式匹配的唯一名称。
3.  机器人必须能够`reset`，抹去他们的记忆，忘记他们的名字，接受一个新的仍然是独一无二的。不用担心回收他们的名字，放回泳池。我们假设一个名字一旦被取了，就用完了。
4.  名称序列**必须是随机的**。这意味着顺序一定是不可预测的。
5.  机器人类必须有一个`forget`方法，让它忘记机器人的当前状态，重置它可能拥有的任何共享状态。

有道理吗？好吧，如果你想尝试这个挑战，那就开始吧。下面我就来分享一下我的旅程。

## 我的旅程

### 1。天真的猜测和检查

这个练习的难度主要在于*名字*的可能性实在太多了。任何通过循环或追加到一个列表来构建所有这些字符串和组合的尝试都太慢了。我尝试了许多不同的方法，在我到达版本 1 之前，实际上有几个错误的开始版本。我的第一个想法是天真地跟踪使用的名字，生成一个随机的名字，并检查它是否在列表中:

```
class Robot
  LETTERS = ('A'..'Z').to_a
  NUMBERS = ('0'..'9').to_a

  @@taken_names = []

  def self.forget
    @@taken_names = []
  end

  attr_reader :name

  def initialize
      reset
  end

  def reset
    name = ""
    loop do
      name = LETTERS.sample(2) + NUMBERS.sample(3)
      break if ! @@taken_names.include?(name)
    end
    @name = name
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这对于创造的第一批机器人来说非常有效，但是一旦你飞得足够高，开始发生碰撞，你就会很快意识到你已经有了一个算法，理论上，它可以无限长地运行，在 676，000 股的干草堆中搜索一根针！

但这是露比！一定有更好的办法！也许我们应该用另一种方式，从可能的名字列表开始，然后弹出它们，保证没有冲突。

### 2。从名单上弹出名字

这是一个很好的想法，但是如何建立名单呢？类似这样的？

```
@@names = LETTERS
  .product(LETTERS, NUMBERS, NUMBERS, NUMBERS)
  .map(&:join)
# => @@names = ['AA000', 'AA001' ... 'ZZ998', 'ZZ999'] 
```

Enter fullscreen mode Exit fullscreen mode

好的，这很有效。`product`方法在其所有参数之间创建了一个“笛卡尔乘积”。比如:

```
[1, 2, 3].product([4, 5])
# => [
# [1, 4],
# [1, 5],
# [2, 4],
# [2, 5],
# [3, 4],
# [3, 5],
# ] 
```

Enter fullscreen mode Exit fullscreen mode

上面那个巨大的产品创建了这样一个列表:

```
[
  ['A', 'A', '0', '0', '0'],
  ['A', 'A', '0', '0', '1'],
  ... 
```

Enter fullscreen mode Exit fullscreen mode

这就是为什么我们通过`.map(&:join)`方法将它们连接成一个字符串。

我们的类的启动时间(以及`forget`运行时间)相当长，但也许这是允许的，看看它为我们的算法节省了多少时间。对吗？对吗？

不对。当我们的列表很大时，随机选择一个索引然后弹出它需要。永远不会。因为，每次我们弹出一个项目，这个项目后面的所有项目都必须找出如何处理它留下的空白。这个名单太大了，就像试图把泰坦尼克号。李奥怎么样了？！(太快了？)

我甚至尝试生成一个巨大的整数列表，并将每个整数转换为我自己定制的编号系统(基数 26，基数 26，基数 10，基数 10，基数 10)，但这更令人困惑，也没有任何好处。

```
class Robot
  LETTERS = ('A'..'Z').to_a
  NUMBERS = ('0'..'9').to_a

  @@possible_names = LETTERS
    .product(LETTERS, NUMBERS, NUMBERS, NUMBERS)
    .map(&:join)

  def self.forget
    @@possible_names = LETTERS
      .product(LETTERS, NUMBERS, NUMBERS, NUMBERS)
      .map(&:join)
  end

  def initialize
    reset
  end

  def reset
    next_index = rand(0...@@possible_names.length)
    @name = @@possible_names.pop(next_index)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这是红宝石！一定有更好的办法！

### 最终解决

事实证明，是有的。我从一个预先构建的列表开始工作的基本想法是正确的，但是我没有让 Ruby 为我工作足够的时间。我有很多可以改进的地方。

首先，名单的建立。我都忘了 Ruby 里的*range 有多牛逼*了。

```
@@names = 'AA000'..'ZZ999' 
```

Enter fullscreen mode Exit fullscreen mode

没错。Ruby 知道如何增加字符串中的每个字符(甚至是字母)来填补空白。老实说，当 Ruby Track 的导师在所有这些工作之后向我指出这一点时，我只哭了 12 分钟。

接下来，随机访问。与其每次随机选择，为什么不提前洗牌一次呢？但是你不能在 Ruby 中打乱一个范围！没问题！

```
@@names = ('AA000'..'ZZ999').to_a.shuffle 
```

Enter fullscreen mode Exit fullscreen mode

这个哭诉环节只持续了 7 分钟。

最后，一遍又一遍地修改这个巨大的列表。解决办法？*不要。*处理大型数据集的最佳方式是不要脱离它。它让 Ruby 为您工作，并使用一个**枚举器。**这将缓慢地产生每个元素。这类似于让指针指向您想要的元素，然后将指针移动到下一个元素，但是工作要少得多。

```
class Robot
  POSSIBLE_NAMES = 'AA000'..'ZZ999'

  @@names = POSSIBLE_NAMES.to_a.shuffle.each

  def self.forget
    @@names = POSSIBLE_NAMES.to_a.shuffle.each
  end

  attr_reader :name

  def initialize
    reset
  end

  def reset
    @name = @@names.next
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这样，您可以遍历这些名字，直到用完为止。很方便，当`@@names`枚举器用完名字时，调用`@@names.next`将引发`StopIteration`异常，告诉用户你用完了名字。如果您愿意，您也可以捕获该异常并引发自己的`RobotNamesDepletedError`。对`Robot.forget`的调用通过新的随机洗牌更新了名单。

你想到了什么？你试过用另一种语言做这个练习吗？结果如何？让我知道。

* * *

*原帖 [`assert_not magic?`](https://assertnotmagic.com/2018/10/27/ruby-a-better-way/)***