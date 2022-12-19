# Ruby 中的设计模式:模板方法模式

> 原文：<https://dev.to/lccezinha/design-patterns-in-ruby-template-method-pattern-5ih>

> 根据定义，模式定义了算法的框架，但是将算法的某些部分留给子类来实现。这些子类可以覆盖主行为，而不需要改变基本算法的主结构。

换句话说，基类定义了一个框架，子类必须用自己的实现来填充这个框架。

## 处理不同的会员计划

假设您需要在应用程序中添加一个会员功能，会员有两种不同的计划，基本和高级。这些计划都有自己的标题、描述和一堆好处。

首先，您可以简单地添加一个 if 语句来检查哪个`type`计划将被创建，这似乎是一个好主意。

```
class Plan
  attr_reader :title, :description, :benefits

  def initialize(kind)
    if kind == :basic
      @title = "I'm the basic plan"
      @description = "My description of basic plan"
      @benefits = ['It is free', 'Access to one account', 'Basic features']
    else
      @title = "I'm the other plan"
      @description = "My description of other plan"
      @benefits = ['No benefits']
    end
  end

  def output
    output = <<-STRING Title: #{title} Description: #{description} Plan benefits: #{benefits.join(', ')}.
 STRING

    puts(output)
  end
end

####

basic = Plan.new(:basic)
basic.output

"""
Title: I'm the basic plan
Description: My description of basic plan
Plan benefits: It is free, Access to one account, Basic features.
"""

other_plan = Plan.new(:other)
other_plan.output

"""
Title: I'm the other plan
Description: My description of other plan
Plan benefits: No benefits.
""" 
```

但是，如果我们需要为一个名为`premium`的新计划添加一个检查，会发生什么呢？如果我们继续使用这种方法，代码将如下所示:

```
class Plan
  attr_reader :title, :description, :benefits

  def initialize(kind)
    if kind != :basic
      @title = "I'm the basic plan"
      @description = "My description of basic plan"
      @benefits = ['It is free', 'Access to one account', 'Basic features']
    elsif kind == :premium
      @title = "I'm the premium plan"
      @description = "My description of premium plan"
      @benefits = ['It is paid', 'Access to ten accounts', 'Premium features', 'Access to support']
    else
      @title = "I'm the other plan"
      @description = "My description of other plan"
      @benefits = ['No benefits']
    end
  end

  # ...
end 
```

如果我们需要添加一个名为`master premium`的新计划呢？是的，这种方法不能很好地扩展，并且会否认代码的灵活性和对许多计划的支持。

## 使用模板方法模式允许多个计划

前面说过，`Template Method`模式的主要定义是定义一个骨架，让子类用自己的实现填充这个骨架。

所以让我们定义基类:

```
class Base
  def title
    raise 'Must implement'
  end

  def description
    raise 'Must implement'
  end

  def benefits
    raise 'Must implement'
  end

  def output
    output = <<-STRING Title: #{title} Description: #{description} Plan benefits: #{benefits}.
 STRING

    puts(output)
  end
end 
```

这很基本，`Base`类只定义了 3 个方法`title`、`description`和`benefits`。`output`方法包含了子类将要实现的行为的框架算法。

`Base`类的重要性在于定义了一个所有子类都必须遵循和实现的接口。

现在让我们创建子类:

```
class BasicPlan < Base
  def title
    "I'm the basic plan"
  end

  def description
    "My description of basic plan"
  end

  def benefits
    ['It is free', 'Access to one account', 'Basic features'].join(', ')
  end
end

###

class PremiumPlan < Base
  def title
    "I'm the premium plan"
  end

  def description
    "My description of premium plan"
  end

  def benefits
    [
      'It will cost USD 10.00',
      'Access to ten accounts',
      'Premium features',
      'You will receive a gift on your birthday'
    ].join(', ')
  end
end 
```

每个子类为每个方法实现自己的行为，而不用担心`output`方法，你唯一关心的
是实现钩子方法，这将在`Base#output`中使用。

如果我们需要添加新的会员计划，会发生什么情况？很简单，我们只需要创建新的类并实现`Base`类方法。

```
class AwesomePlan < Base
  def title
    "I'm the awesome plan"
  end

  def description
    "My description of awesome plan"
  end

  def benefits
    [
      'It will cost USD 1,000.00',
      'Access to a hundread accounts',
      'Awesome features',
      'You will receive a gift on your birthday',
    ].join(', ')
  end
end

###

"""
Title: I'm the awesome plan
Description: My description of awesome plan
Plan benefits: It will cost USD 1,000.00, Access to a hundread accounts, Awesome features, You will receive a gift on your birthday.
""" 
```

好了，就是这样，这就是如何用 Ruby 使用模板方法模式。

### 参考

*   马科斯·布里泽诺
*   [船坞](https://dockyard.com/blog/ruby/2013/07/10/design-patterns-template-pattern)