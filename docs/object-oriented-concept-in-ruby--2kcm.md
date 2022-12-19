# Ruby 中的面向对象概念

> 原文：<https://dev.to/sudhansubedi/object-oriented-concept-in-ruby--2kcm>

面向对象的概念是编程中流行的概念之一。我正在学习如何在 Ruby 中实现面向对象的概念，所以在这篇文章中，我将会讲述它。

## 1。几乎一切都是客体

```
 # a is an object 
  # a.object_id gives the identifier
  a = 10

  # similarly b is also object
  b = "Hello World !" 
```

Enter fullscreen mode Exit fullscreen mode

数组是对象，类是对象，模块是对象，范围也是对象，代码块可以是对象，nil 也是对象，它代表虚无，在 ruby 中几乎所有东西都是对象。

## 2。班级

用 ruby 编写类的语法非常简单，只需编写关键字 class 和 class name。类内部的方法是小型的，snake case 语法更受欢迎。ruby 中类的实例变量可以以@符号开头。

initilize 是创建对象时自动调用的类的特殊函数。要创建类的对象，我们可以使用 ClassName.new 语法。其中一个简单的例子如下。

```
require 'date'

class User
  def initialize(date_of_birth)
    days_since_birth = Date.today - Date.parse(date_of_birth)
    @age = days_since_birth / 365
  end
  def is_kid?
    @age <= 18
  end
end

john = User.new("2006-02-12")
puts john.is_kid? # Returns False 
```

Enter fullscreen mode Exit fullscreen mode

创建对象时会自动调用 Initilize 方法，上面的代码片段都是关于创建用户类的对象的。年龄是阶级变量。

## 3。方法

方法对 ruby 来说是非常基础的，方法只是以 def 符号开始，方法名没有或更多的参数。

```
 def another_method(first_arg, last_arg)
        "You passed in #{first_arg} and #{second_arg}"
    end 
```

Enter fullscreen mode Exit fullscreen mode

可以用大写字母创建方法名，但是请不要这样做。所以总是写方法小写后面用下划线连接。

```
 def ThisIsPossible
        # Please do not do this
    end 
```

Enter fullscreen mode Exit fullscreen mode

返回 true 或 false(布尔值)的方法总是以问号结尾。

```
 def is_not_boy?
      @gender != 'boy'
    end 
```

Enter fullscreen mode Exit fullscreen mode

感叹号！在 ruby mean lookout 方法的末尾，这意味着属性的原始引用值也发生了变化，所以要注意这一点。例如

```
 message = "Hello World"
    message.downcase! # This change message value to 'hello world' 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在 ruby 中定义可选参数也是可能的，可选参数应该总是方法中的最后一个参数。

```
 def greet(name, informal = false)
      if informal
        "hi #{name}"
      end
      else
        "Hello #{name}"
      end      
    end 
```

Enter fullscreen mode Exit fullscreen mode

假设有三个参数，其中两个是可选的。我们不需要发送默认值，但是如果第三个参数不同于默认值，那么我们应该在方法调用期间传递所有三个参数，对吗？让我们从示例
中清除

```
 def greet(name, informal = false, shout = false)
      greeting = if informal then "hi" else "hello" end
      message = "#{greeting} #{name}"
      if shout
        message.upcase
      else
        message
      end
    end

    greet("John", false, true) 
```

Enter fullscreen mode Exit fullscreen mode

要解决这个问题，我们可以这样做！这是在 ruby 方法中传递参数的更清晰的方式。

```
 def greet(name, informal: false, shout: false)
      greeting = if informal then "hi" else "hello" end
      message = "#{greeting} #{name}"
      if shout
        message.upcase
      else
        message
      end
    end

    greet("John", shout: true) 
```

Enter fullscreen mode Exit fullscreen mode

通常我们不需要返回方法中的值，方法的最后一个值由方法自动返回。但有时我们应该返回任何条件，然后我们可以通过使用 return 关键字返回值，就像这样。

```
 class MyClass
      def odd_or_even(num)
        return unless num.respond_to?(:odd?)
        if num.odd? then  "odd" else "even" end
      end
    end

    MyClass.new.odd_or_even(3) 
```

Enter fullscreen mode Exit fullscreen mode

## 4。遗产

Ruby 只支持单一继承，这意味着 ruby 中的每个类只有一个超类。我们可以通过写''来指定超类

```
 class User
      attr_reder :name
      def initilize(name)
        @name = name
      end    
    end

    # child class of User class
    class Admin < User
      def is_admin
        true
      end
    end

    user = Admin.new("Madhu Sudhan")
    user.name # will results 'Madhu Sudhan'
    user.is_admin? # will return true 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个类如何包含一个超类的例子

```
 Admin.superclass
    => User
    User.superclass
    => Object
    Object.superclass
    => BasicObject
    BasicObject.superclass
    => nil 
```

Enter fullscreen mode Exit fullscreen mode

## 5。模块

ruby 中的模块实际上只是方法的集合，类也是一种特殊类型的模块。我们可以用和类一样的方式定义模块。

```
require 'date'

module Employee
  attr_accessor :start_date

  def employment_length
    days = Date.today - start_date.to_date
    "#{days.to_i} days"
  end
end

class User
  include Employee
end

u = User.new
u.start_date = Date.today - 365
puts u.employment_length 
```

Enter fullscreen mode Exit fullscreen mode

Extend 是使用模块的另一种方式，而且更有趣，之前我们使用了模块在类中定义的 include 关键字，但是我们也可以使用 extend 关键字将方法告诉类的单个实例，

```
require 'date'

module Employee
  attr_accessor :start_date

  def employment_length
    days = Date.today - start_date.to_date
    "#{days.to_i} days"
  end
end

class User
end

u = User.new
u.extend(Employee)
u.start_date = Date.today - 365
puts u.employment_length 
```

Enter fullscreen mode Exit fullscreen mode

### 6。常数

ruby 中的常量与其他语言中的常量非常相似，它忽略了值的变化。看起来像变量，但以大写字母开始。按照惯例，常量的所有字母都应该大写。

```
 PI = 3.14 # is good

    Pi = 3.14 # is possible but not do it 
```

Enter fullscreen mode Exit fullscreen mode

使用类和模块中的常量可以这样访问

```
 module Geometry
      PI = 3.14
    end

    class Circle
      PI = 3.142
    end

    puts Geometry::PI == Circle::PI # false 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

学习 ruby 编程语言真的很有趣，而且也很容易。