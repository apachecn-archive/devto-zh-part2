# 我的 Ruby 之旅:连接事物

> 原文：<https://dev.to/edisonywh/my-ruby-journey-hooking-things-up--feg>

# 我的 Ruby 之旅:勾东西

我最近才刚刚开始我的 Ruby/Rails 之旅(工作不到一年)，有一天我遇到了类似这样的事情。

```
class Product
  acts_as_paranoid
  acts_as_taggable
  has_paper_trail
  monetize :price_cents
end 
```

Enter fullscreen mode Exit fullscreen mode

我知道它们来自 gems，但我认为它看起来超级酷，并且真的很好奇它们是如何工作的，所以我后退一步，分解这个问题并逐一解决它。

## 停止 1:初步发现

我在这里发现了一些事情:

*   它们只是普通的类方法
*   当您将一个方法名放入类中时，每次您创建它的新实例时，该方法都会被调用。

例如:

```
class Example
  puts "This gets called whenever you initiate an instance of #{self}"
end

Example.new #=> "This gets called whenever you initiate an instance of Example 
```

Enter fullscreen mode Exit fullscreen mode

所以，现在我知道它们只是类方法，我决定下一步应该写一些类似的东西；可重用的类方法！(我还不想讨论 Rails 如何自动加载它)

我就这样开始了我的旅程，然后瞧，下一个障碍出现了！

## Stop 2:不能和模块共享类方法？

*(剧透:是的你可以，我只是一开始不知道！*)😛

起初，我认为这将是一个简单的`include Module`,因为我被告知可重用方法就是这样工作的，所以我在这里，天真地试图在模块上定义一个类方法，然后将它包含在类中——但是它不起作用！

例如:

```
module Reusable
  def self.awesome?
    puts "awesome!"
  end
end

class AwesomeObject
  include Reusable
end

AwesomeObject.awesome? #=> undefined method `awesome?' for AwesomeObject:Class (NoMethodError) 
```

Enter fullscreen mode Exit fullscreen mode

之所以这样，是因为 Ruby 的`include`不包括`class methods`。

通过`extend`、`include`的不太知名的兄弟姐妹(另一个是`prepend`)有一个解决方案。

完整的解释有点超出了这篇文章的范围，所以我不会在这里深入讨论，但是如果你想了解更多，这是由于 Ruby 中一个叫做`Singleton Class`的东西。这也将是我下一篇博文的主题，敬请关注！

## 停 3:但是我想让大家都感受到`included`并且上瘾！

由于`include`是`extend`和`prepend`更受欢迎的兄弟，我下定决心要确保最终用户只需要记住`include`一个模块，而不需要考虑-

“我是这个，`include`这个还是`prepend`这个，”

也希望避免这样的情况:你包含了一个模块，然后花了一整天去弄清楚为什么类方法不起作用。

*实际上，事后来看，告诉开发人员只是盲目地`extend`一个方法并不是一个坏主意。但是话说回来，如果我放弃了，就不会有现在的博客*😉

## 停 4:愿望达成！

经过大量研究，我发现 Ruby 有一个`included`、`extended`和`prepended`钩子可供我们(你猜对了)*钩住*。

> 来源:https://ruby-doc.org/core-2.2.0/Module.html

这些钩子允许我们在模块处于`included`、`extended`或`prepended`状态时执行代码，猜猜我们要做什么？

当一个模块正在进行`included`时，我们将对其进行`extend`(起初我觉得这听起来很有趣)

让我们看一个关于钩子如何工作的小例子:

例如:

```
module HookedModule
  def self.included(base)
    puts "#{self} is being included in #{base}"
  end
end

class BaseObject
  include HookedModule
end

BaseObject.new #=> HookedModule is being included in BaseObject 
```

Enter fullscreen mode Exit fullscreen mode

这里需要注意一些事情:

*   `included`需要是一个类方法，因为它是`Module`类上的方法。如果你不声明它是一个类方法，钩子就不起作用。
*   传递给`included`的`base`参数是包含模块的类，在我们的例子中是 BaseObject。

## 终点站:延长？包括？为什么不两者都要？

现在我对钩子的工作原理有了更好的理解，让我们回顾一下我的目标是什么:

> `Extend`一个模块当它被`included`

厉害！让我们试着在那里扩展类方法！(当需求确切地告诉你需要做什么时，你难道不喜欢吗？)

```
module HookedModule
  def self.included(base)
    base.extend ClassMethods
  end

  module ClassMethods
    def awesome?
      puts "Yes, #{self} is awesome, stop asking."
    end
  end
end

class BaseObject
  include HookedModule
end

BaseObject.awesome? #=> "Yes, BaseObject is awesome, stop asking. 
```

Enter fullscreen mode Exit fullscreen mode

这样做的原因是，当我们第一次挂钩到`included`时，我们通过`base`获得了对 BaseObject 的访问权。然后我们可以在我们的`BaseObject`类上调用`extend`(一个类方法)，它的工作方式和我们刚才做的一样:

```
class BaseObject
  extend HookedModule
end 
```

Enter fullscreen mode Exit fullscreen mode

起初，这看起来像是很多工作，但是如果你想重用实例方法和类方法，包含和扩展你的模块是没有意义的。

## 重述

因此，我从这篇文章开始，试图弄清楚这些 gem 是如何提供神奇的方法的——我仍然不完全理解在没有明确包含或排除模块的情况下，这些方法是如何被加载的(这可能是另一篇博文的主题！)，但我发现它们都只是普通的类方法，我可以将它与 Module 一起使用，使我的类方法可重用。

## 我的心得:

*   您可以在初始化类时运行方法。
    包含一个模块不包含类方法。

*   模块有钩子，我们可以通过钩子来简化我们的 API。

## 作者注

这是我写的第一篇博文！可能不是最先进的，也不是写得最好的帖子，但我仍然在学习，所以如果你有任何反馈，请留下评论，我真的很感谢他们！

我也把我的文章贴在 Medium 上了！查看这里:[https://medium . com/@ Edison ywh/my-ruby-journey-hooking-things-up-91d 757 E1 c 59 c](https://medium.com/@edisonywh/my-ruby-journey-hooking-things-up-91d757e1c59c)

原来如此！我的第一次红宝石之旅即将结束。我喜欢到处探索 Ruby 的小魔法，所以我希望以后能多写几篇博文。在那之前，祝你在自己的红宝石之地一路平安！