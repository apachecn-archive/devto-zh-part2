# 改进 Ruby 代码设计的技巧

> 原文：<https://dev.to/lcezermf/tips-to-improve-your-ruby-code-design-530j>

[![image](img/177cd6ce02d191b5bb5f95dc9e0de468.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1zegJyb7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t8js64ppcouwqpv1fs3i.jpg)

> 任何傻瓜都能写出计算机能理解的代码。优秀的程序员写人类能理解的代码。

### 为什么你应该关心你的代码的设计？

作为开发人员，我们有时倾向于认为高效与尽可能快地交付任务相关，但对我来说，这不是事实。

成为一名高效开发人员不仅仅是执行任务和部署代码，还与代码质量、在下一个版本中维护和发展该特性的难易程度有关。因此，认真对待你的代码是很重要的，即使是最小的变化。

想象一下，你为一家拥有大量源代码、太多特性、太多团队、不同方法的软件的公司工作。今天你在做一个确定的功能，下周你关注的是另一个，两周后你再回来做第一个功能...当你为一家大公司工作时，这种流动是正常的。

永远记住，有时候，你需要阻止你自己从你自己的遗产中走出来。

### 避开隐藏的结构

*   代码很难理解，也很难维护
*   您必须避免直接使用索引位置从数组中获取一些数据

要避免的例子:

```
class ObscuringReferences
  def initialize(data)
    @data = data
  end

  def calculate
    data.collect do |cell|
      cell[0] + (cell[1] * 2)
    end
  end

  private

  attr_reader :data
end

ObscuringReferences.new([[622, 20], [622, 23], [559, 30], [559, 40]]) 
```

Enter fullscreen mode Exit fullscreen mode

这段代码的主要问题是，它使用数组的索引位置来获取一些要在 de `calculate`方法中使用的信息，但是我们不能确定这些信息是什么，因为`0`和`1`没有什么意义。

#### 如何避开隐藏结构？

*   使用常数来赋值

```
class RevealingReferences
  HEIGTH  = 0
  WIDTH = 1

  def initialize(data)
    @data = data
  end

  def calculate
    data.collect do |value|
      value[HEIGTH] + (value[WIDTH] * 2)
    end
  end

  private

  attr_reader :data
end 
```

Enter fullscreen mode Exit fullscreen mode

在常量中指定索引位置将有助于改进代码，因为现在索引是有意义的。

*   使用结构创建类

```
Shape = Struct.new(:height, :width)

class RevealingReferences
  def initialize(data)
    @data = prepare(data)
  end

  def calculate
    data.collect do |shape|
      shape.height + (shape.width * 2)
    end
  end

  private

  attr_reader :data

  def prepare(data)
    data.collect do |value|
      Shape.new(*value)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

另一种方法是使用`Struct`数据结构来表示数据。

### 避免与正则表达式进行比较

*   取而代之的是，将这个正则表达式赋给一个有意义的变量名，并使用这个变量。

```
 # Instead of using directly the regular expression in the code...
  if string =~ /\d+/
    # ...CODE
  end

  # Assign it to a variable with a meaningful name, and use it.
  is_decimal = /\d+/
  if string =~ is_decimal
    # ...CODE
  end 
```

Enter fullscreen mode Exit fullscreen mode

这个简单的改变可以节省你理解正则表达式的时间。

### 避免直接依赖

*   宁愿使用[合成而不是继承](https://www.thoughtworks.com/insights/blog/composition-vs-inheritance-how-choose)
*   拥抱[坚实的原则](https://robots.thoughtbot.com/back-to-basics-solid)

### 遵循得墨忒耳定律

*   避免高耦合代码
*   不了解依赖关系中的依赖关系

```
class Customer
  def state
    address.state.name
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，`Customer#state`方法违反了德米特里定律，因为事实上，要访问`state.name`，我们首先需要通过`customer`联系你与`address`的关系，然后与`address`的关系将联系`state`。

所以我们可以得出这样的结论:类`Customer`对你的依赖关系知道得太多了。`State`类中的任何错误都可能破坏`Customer`，即使它们没有直接关系。

解决这个问题的方法是使用[委托人](https://ruby-doc.org/stdlib-2.1.0/libdoc/delegate/rdoc/Delegator.html)。

### 告诉不要问！

*   告诉对象要做什么，不要问他们能做什么
*   避免询问一个你当前状态的对象，根据这个答案去做一些事情

```
class SocialMediaPost
  def send
    SocialMediaPost::API.post(args)
  end

  def connected?
    connected
  end
end

post = SocialMediaPost.new(content: 'Some Message')

if post.connected?
  post.send
end 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们首先询问对象是否连接，然后基于此我们对`post.send`执行一个动作。

现在，假设您需要在代码库中的不同点执行这个操作，现在，除了当前的验证之外，您还需要添加另一个验证。此外，这段代码暴露了`SocialMediaPost`的内部细节，我们知道`connected?`必须为真才能执行其他方法。

*   不要不必要地暴露对象的细节

```
class SocialMediaPost
  def send
    SocialMediaPost::API.post(args) if connected?
  end

  private

  def connected?
    connected
  end
end

post = SocialMediaPost.new(content: 'Some Message')
post.send 
```

Enter fullscreen mode Exit fullscreen mode

这种方法不会暴露类的内部细节，并且遵循 Demeter 定律。

### 结论

*   童子军规则:永远让你的代码比你发现它时更好
*   更好的设计始于微小的改变
*   你需要为人类写代码，而不是为计算机