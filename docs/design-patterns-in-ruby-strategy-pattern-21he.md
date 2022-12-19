# Ruby 中的设计模式:策略模式

> 原文：<https://dev.to/lccezinha/design-patterns-in-ruby-strategy-pattern-21he>

> 根据定义，策略模式定义了一系列的算法，并将它们封装在你自己的类中，这样策略就可以互换。

换句话说，该模式允许主算法独立于使用它的客户机而变化。

它非常类似于[模板方法](https://medium.com/@lccezinha/design-patterns-in-ruby-template-method-pattern-5fd2dcbe15ea)，但是它使用了不同的方法，它更喜欢使用[合成而不是继承](https://medium.com/humans-create-software/composition-over-inheritance-cb6f88070205)。

首先，假设有一个负责将文本解析成不同格式的`Parser`类，最初是`JSON`和`XML`。

```
class TextParser
  attr_accessor :text, :parser

  def initialize(text, parser)
    @text   = text
    @parser = parser
  end

  def parse
    if parser == :json
      "{ text: #{text} }"
    elsif parser == :xml
      "<text>#{text}</text>"
    end
  end
end

parser_xml = TextParser.new('My Text', :xml)
parser_xml.parse => "<text>My Text</text>"

puts "\n -- \n"

parser_json = TextParser.new('My Text', :json)
parser_json.parse => "{ text: My Text }" 
```

这个解决方案看起来不错，但是使用了一个`if`来定义哪个`strategy`来格式化文本。这不是一个好方法，原因和我在[模板方法](https://medium.com/@lccezinha/design-patterns-in-ruby-template-method-pattern-5fd2dcbe15ea)的文章中解释的一样。

我们是否需要添加一个新的(或许多其他的)解析器？我们要添加很多`ifs`来检查每个解析器吗？我不会缩放，不符合[开闭原则](https://robots.thoughtbot.com/back-to-basics-solid)

我们需要一种更好的方法，允许灵活地添加新的解析器，而不需要改变现有的代码。

## 使用正确的策略考虑许多解析器

`Template Method`，可以解决这个问题，但是使用继承的方法不是我们想要的。继承有某些问题:

*   使代码非常耦合
*   不支持封装

策略模式将为我们提供一个很好的方法，它将避免上述问题，并且将符合 SOLID 的开闭原则(我计划在以后的帖子中讨论)。

首先，为了解决这个问题并给代码更多的灵活性，`TextParser#parse`方法将只负责将对`parser`方法的调用委托给一个给定的类，这些具体的类(JSONParser 或 XMLParser)中的每一个都将包含它们自己的行为来实现`parse`方法，并将通过`TextParser`构造函数作为依赖项注入。

```
class BaseParser
  def parse(text)
    raise 'Must implement!'
  end
end

###

class XMLParser < BaseParser
  def parse(text)
    "<text>#{text}</text>"
  end
end

###

class JSONParser < BaseParser
  def parse(text)
    "{ text: #{text} }"
  end
end

###

class TextParser
  attr_reader :text, :parser

  def initialize(text, parser)
    @text = text
    @parser = parser
  end

  def parse
    puts(parser.parse(text))
  end
end

parser_xml = TextParser.new('My Text', XMLParser.new)
parser_xml.parse => "<text>My Text</text>"

puts "\n -- \n"

parser_json = TextParser.new('My Text', JSONParser.new)
parser_json.parse => "{ text: My Text }" 
```

首先，我用一个必须由子类实现的抽象方法创建了一个`BaseParser`类，这里`BaseParser`的主要意图是“模拟”一个`interface`，并保证所有子类都需要实现这些接口。

但是，由于 Ruby 是一种动态语言，它使用 Duck Typing 作为检查接收者是否必须执行给定消息的方法，所以我们可以去掉这些基类，只使用具体的解析器，结果一定是相同的。

```
class XMLParser
  def parse(text)
    "<text>#{text}</text>"
  end
end

###

class JSONParser
  def parse(text)
    "{ text: #{text} }"
  end
end

###

class TextParser
  attr_reader :text, :parser

  def initialize(text, parser)
    @text = text
    @parser = parser
  end

  def parse
    puts(parser.parse(text))
  end
end

parser_xml = TextParser.new('My Text', XMLParser.new)
parser_xml.parse => "<text>My Text</text>"

puts "\n -- \n"

parser_json = TextParser.new('My Text', JSONParser.new)
parser_json.parse => "{ text: My Text }" 
```

用这种方法添加新行为真的很简单，例如，如果我需要添加一个新的解析器，我只需要创建实现`parse`方法的新解析器代码，一切都会继续工作:

```
class ReverseParser
  def parse(text)
    text.reverse
  end
end

parser_xml = TextParser.new('My Text', ReverseParser.new)
parser_xml.parse => "txeT yM" 
```

此外，策略模式将倾向于使用组合而不是继承，代码将符合[开闭原则](https://robots.thoughtbot.com/back-to-basics-solid)。

好了，就这样，现在你可以创建你需要多少个解析器了。这就是如何在 Ruby 中使用策略模式。

查看完整的[源代码](https://github.com/lccezinha/random-stuff/tree/master/patterns/strategy-pattern)。

## 参考

*   [积分策略模式](http://reefpoints.dockyard.com/2013/07/25/design-patterns-strategy-pattern.html)
*   [固体的开/关原理](https://robots.thoughtbot.com/back-to-basics-solid)