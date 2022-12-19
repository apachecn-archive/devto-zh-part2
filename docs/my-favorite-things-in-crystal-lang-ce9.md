# 我在水晶郎最喜欢的东西

> 原文：<https://dev.to/jwoertink/my-favorite-things-in-crystal-lang-ce9>

在学习一门新语言的时候，会有一些瞬间真正让你欣赏那门语言。偶尔，有些时刻会让你质疑自己的理智。

在我学习 Crystal 的过程中，有一些事情我发现自己在说“我喜欢这个！”给了我 12 年前对 Ruby 同样的感觉。这个列表是 [Crystal Lang](https://crystal-lang.org/) 中的“开箱即用”特性、方法和构造，在我了解它之后，我不得不告诉其他人。

*注意:在你们中的一些人说出来之前，我知道这些不是原始的语言想法*

## 方法重载

这是一个类可以有多个同名方法的概念。编译器决定调用哪个方法的方式是基于传递(或不传递)的参数。

我喜欢的一点是，很多时候你希望一个方法被称为特定的东西，但你必须以几种不同的方式使用它。

```
class Pagination

  def initialize(opts = {} of String => String)
    initialize(opts["page"].to_i, opts["per"].to_i
  end

  def initialize(page : Int32, per : Int32)
    # setup the class
  end
end

Pagination.new({"page" => "1", "per" => "12"})
Pagination.new(1, 12) 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，您可以有多种方法来实例化一个对象，但是最好的是，它们都可以集中到实际的实现中。这只是让您能够为开发人员创建一个友好的 DSL，同时仍然保持您需要的实现细节。

这方面的其他应用程序可能会将您的方法分离出来，一个用于处理成功使用，另一个用于处理错误，甚至几个用于不同类型的错误！

```
def call(some_string : String)
  # do thing since a string was passed
end

def call(not_a_string : Nil)
  raise "some error instead because nil was passed"
end 
```

Enter fullscreen mode Exit fullscreen mode

## 实例变量

说到初始化...这太棒了。

```
# Setting instance variable values in crystal
class Spot
  def initialize(@x : Int32, @y : Int32)
  end
end

# Setting instance variable values in ruby
class Spot
  def initialize(x, y)
    @x = x
    @y = y
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

当你只有两个论点时，这没什么大不了的。无论如何，你通常希望减少参数的数量；但是，你有没有看过一些美术/游戏类型的设置？[看着你 gosu](https://www.rubydoc.info/github/gosu/gosu/Gosu/Image#draw_rot-instance_method) 。在定义方法时进行这些设置只会减少代码中的干扰。

## 元组

这些是已知大小和类型的对象集合。因为它们不存在于 Ruby、PHP 或 JavaScript 中，所以我从来不知道它们有什么用。

```
class Thing
  @x : Tuple(String, String)
end 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我知道实例变量`@x`将总是并且只包含两个类似`{"this", "that"}`的字符串。就是这样。我把它严格定义为 2 个字符串，你不能添加或删除它。你可以使用一个数组，并在其中放入 2 个元素，但据我所知(这可能是错误的),元组将有一个性能优势，因为编译器做的猜测工作要少得多。你仍然像使用数组`@x[0] == "this"` `@x[1] == "that"` `@x.each ...`一样使用元组。

## 地图快捷方式

你可以在 Ruby 中用类似于`things.map(&:to_s)`的块做一些花哨的速记，这些块将把`to_s`方法映射到每一件事情上。但是如果`things`是一个散列数组，并且你想取出一个特定键的值，你不能使用快捷方式。在水晶，你可以！

```
data = [{a: 1, b: 2}, {a: 3, b: 4}]
data.map(&.[:a]) # in crystal

data.map {|h| h[:a]} # in ruby 
```

Enter fullscreen mode Exit fullscreen mode

## 宏

宏是在编译应用程序之前运行的一些代码。宏代码被扩展成其他代码。如果你曾经使用某种模板语言(比如 ruby 中的 ERB)构建过一个 web 应用程序，那就差不多了。它们对我来说还是有点困惑，但这里有一个小例子。

```
macro run_tests
  {% for number in ["one", "two", "three"] %}
    def test_{{number.id}}
      puts "Testing {{number.id}}"
    end
  {% end %}
end
# actually run the macro
run_tests

# This would expand to:

def test_one
  puts "Testing one"
end
def test_two
  puts "Testing two"
end
def test_three
  puts "Testing three"
end 
```

Enter fullscreen mode Exit fullscreen mode

这个宏将扩展到正在定义的三个方法，但是只有在您实际调用`run_tests`方法的地方。这是一种在你的代码中加入一些元编程的方法，让事情变得简单一点。不过好的一面是，它会在程序编译之前(期间)展开。这意味着当你运行程序时，那些方法存在，并且已经被类型检查过了，等等...

说到宏...有一个工具可以让你在 crystal 的命令行中展开这些宏，看看它们到底是什么样子。

```
macro test
  [
  {% for x in [0,1,2] %}
    {% for y in [4,5,6] %}
    [{{x.id}}, {{y.id}}],
    {% end %}
  {% end %}
  ]
end

test 
```

Enter fullscreen mode Exit fullscreen mode

```
$ crystal tool expand -c test.cr:11:1 ./test.cr
1 expansion found
expansion 1:
   test

# expand macro 'test' (/Users/jeremywoertink/Development/crystal/test.cr:1:1)
~> [[0, 4], [0, 5], [0, 6], [1, 4], [1, 5], [1, 6], [2, 4], [2, 5], [2, 6]] 
```

Enter fullscreen mode Exit fullscreen mode

## T

常量`T`是这个漂亮特性的约定，不过，我确信您真的可以使用任何常量名称(这并不冲突)。假设你有一个模块做一些很酷的事情，但是这个模块需要一些特殊的类型。您可以设置该模块来获取这个匿名类型`T`，并将其作为您的占位符。

```
module Shared::PaginationComponent(T)
  private def render_pagination(pagination : T)
    # do pagination rendering
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这个模块接受 T，因为这个方法接受一个参数`pagination`，也就是类型`T`。在我的例子中，这个`T`实际上可以是一组`User`对象，或者`Video`对象，或者`Site`对象，等等...我可以做类似
的事情

```
def render_pagination(pagination : User::Collection | Video::Collection | Site::Collection | Whatever::Collection)
end 
```

Enter fullscreen mode Exit fullscreen mode

但是我必须最终找出是哪一个，然后输入 cast `.as(User::Collection)`。相反，我用这个做

```
class User::IndexPage
  include Shared::PaginationComponent(User::Collection) # see! It know exactly which to use here
end

class Site::IndexPage
  include Shared::PaginationComponent(Site::Collection)
end 
```

Enter fullscreen mode Exit fullscreen mode

## 使

好吧，所以 [Make](https://www.gnu.org/software/make/) 其实和水晶无关。我把这个加进去，因为我从来没有理由去学 Make。我过去看过一些粗糙的 Makefiles，它们总是让我害怕。自从开始使用 crystal，我学会了使用 Make，并编写自己的 Makefile 来构建我的应用程序。这让我觉得自己是一个“真正的”程序员。

```
OUT_DIR=bin
build: clean
    @mkdir -p $(OUT_DIR)
        @crystal build --release -o $(OUT_DIR)/app src/my_app.cr
clean:
    rm $(OUT_DIR)/app 
```

Enter fullscreen mode Exit fullscreen mode

这是一个简单的 Makefile 文件，您可以将它放在项目的根目录下，然后运行`make build`来构建您的 crystal 项目，并将其放入您的 bin 目录中。

## 更

我还可以说很多，但我想这已经够长了。随着我开始更好地理解一些东西，我可能会在以后的博客中添加更多的细节。