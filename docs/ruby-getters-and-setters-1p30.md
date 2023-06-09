# Ruby Getters 和 Setters

> 原文：<https://dev.to/k_penguin_sato/ruby-getters-and-setters-1p30>

# 目录

1.  [什么是 getter 方法](#what-is-a-getter-method)
2.  [什么是 setter 方法](#what-is-a-setter-method)
3.  什么是访问器？
4.  [参考文献](#references)

# 什么是 getter 方法？

**getter 方法**是*获取*一个实例变量的值的方法。
没有 getter 方法，你不能在实例变量被实例化的类之外获取实例变量的值。

这里有一个例子。

```
class Movie
  def initialize(name)
    @name = name
  end
end

obj1 = Movie.new('Forrest Gump')
p obj1.name #=> undefined method `name' for #<Movie:0x007fecd08cb288 @name="Forrest Gump"> (NoMethodError) 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，`obj1` ( `name`)的值不能在`Movie`类之外检索。如果你试图在没有 getter 方法的情况下获取实例变量的值，Ruby 会抛出[No method 错误](http://ruby-doc.org/core-2.5.0/NoMethodError.html)。

现在，让我们看看如何用 getter 方法在`Movie`类之外检索`obj1`的值。
在这里你要做的就是定义一个名为`name`的`getter method`。虽然 getter 方法的名称可以是任何名称，但是通常的做法是将 getter 方法命名为实例变量的名称。

```
 class Movie
  def initialize(name)
    @name = name
  end

  def name
    @name
  end
end

obj1 = Movie.new('Forrest Gump')
p obj1.name #=> "Forrest Gump" 
```

Enter fullscreen mode Exit fullscreen mode

# 什么是 setter 方法？

**setter**是*设置*一个实例变量的值的方法。
如果没有 setter 方法，你不能在类之外给一个实例变量赋值。
如果你试图在类外设置实例变量的值，Ruby 会抛出 [No Method Error](http://ruby-doc.org/core-2.5.0/NoMethodError.html) ，就像你试图在没有 getter 方法的情况下在类外获取实例变量的值一样。

```
class Movie
  def initialize(name)
    @name = name
  end

  def name #getter method
    @name
  end
end

obj1 = Movie.new('Forrest Gump')
p obj1.name #=> "Forrest Gump"
obj1.name = 'Fight Club' #=> undefined method `name=' for #<Movie:0x007f9937053368 @name="Forrest Gump"> (NoMethodError) 
```

Enter fullscreen mode Exit fullscreen mode

在类内部定义 setter 方法可以在类外部设置实例变量的值。你可以像下面的代码一样定义一个 setter 方法。

```
 class Movie
  def initialize(name)
    @name = name
  end

  def name #getter method
    @name
  end

  def name=(name) #setter method
    @name = name
  end
end

obj1 = Movie.new('Forrest Gump')
p obj1.name #=> "Forrest Gump"
obj1.name = 'Fight Club'
p obj1.name #=> "Fight Club" 
```

Enter fullscreen mode Exit fullscreen mode

通过使用`name=`，您现在可以给`obj1`分配一个新的值`Fight Club`。

# 什么是存取器？

是一种创建 getter 和 setter 方法的方法，无需在类中显式定义它们。Ruby 中有三种类型的 fo 访问器。

*   `attr_reader`为每个给定的属性自动生成一个 getter 方法。
*   `attr_writer`为每个给定的属性自动生成一个 setter 方法。
*   `attr_accessor`为每个给定的属性自动生成一个 getter 和 setter 方法。

首先，我们来看看`attr_reader`！
正如你在下面的代码中看到的，`name`和`year`在`Movie`类之外被检索，即使它们都没有 getter 方法。这是因为`attr_reader`为每个给定的属性生成一个 getter 方法。

```
class Movie
  attr_reader :name, :year

  def initialize(name, year)
    @name = name
    @year = year
  end
end
obj1 = Movie.new('Forrest Gump', 1994)
p obj1.name #=> Forrest Gump
p obj1.year #=> 1994 
```

Enter fullscreen mode Exit fullscreen mode

其次，我们来看看`attr_writer`是如何运作的！
正如我上面提到的，`attr_witer`为每个给定的属性生成一个 setter 方法。因此，您可以为`ob1`赋值，而无需为`name`和`year`显式编写 setter 方法！

```
class Movie
  attr_reader :name, :year 
  attr_writer :name, :year

  def initialize(name, year)
    @name = name
    @year = year
  end
end
obj1 = Movie.new('Forrest Gump', 1994)
obj1.name = 'Fight Club'
obj1.year = 1999
p obj1.name #=> "Fight Club"
p obj1.year #=> 1999 
```

Enter fullscreen mode Exit fullscreen mode

最后，同样重要的是，`attr_accessor`只用一行代码就完成了`attr_reader`和`attr_writer`所做的事情！它将为每个给定的属性自动生成一个 getter 和 setter 方法。

```
class Movie
  attr_accessor :name, :year

  def initialize(name, year)
    @name = name
    @year = year
  end
end
obj1 = Movie.new('Forrest Gump', 1994)
obj1.name = 'Fight Club'
obj1.year = 1999
p obj1.name #=> "Fight Club"
p obj1.year #=> 1999 
```

Enter fullscreen mode Exit fullscreen mode

# 参考文献

[Ruby getter 和 setter](https://k-blog0130.herokuapp.com/posts/2)
[Ruby 中 getter/setter 方法如何工作](https://medium.com/@rondwalker22/how-getter-setter-methods-work-in-ruby-c5f5da07f99)
[Ruby 中的 attr_accessor 是什么？](https://stackoverflow.com/questions/4370960/what-is-attr-accessor-in-ruby)
[rubylearning.com](http://rubylearning.com/satishtalim/ruby_syntactic_sugar.html)