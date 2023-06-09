# CodeTip - Ruby:提供迭代集合的方法

> 原文：<https://dev.to/avraammavridis/ruby---provide-ways-to-iterate-over-collections-3al7>

有时我们有代表集合的类，例如一个类组可以代表成员的集合

```
class Group
  def initialize(members)
    @members = members.dup
  end
end 
```

当我们想要迭代集合的成员时，我们必须通过`attr_reader`
暴露它们

```
class Group
  attr_reader :members

  def initialize(members)
    @members = members.dup
  end
end

group1 = Group.new(["Nick", "Ana", "Petros"])

group1.members.each { |x| p |x| } 
```

这种方法的问题是，集合的消费者必须知道我们将变量命名为“members ”,并使用它来迭代。如果我们重命名这个变量，我们集合的消费者也必须重命名他们的调用。此外，我们无法控制枚举的顺序。我们能做些什么来解决这个问题？

我们可以使我们的集合成为可枚举的，并定义一个“each”方法

```
class Group
  include Enumerable

  def initialize(members)
    @members = members.dup
  end

  def iterate_members_alphabeticaly
    ite = @members.dup.sort
    while k = ite.shift()
      yield k
    end
  end

  def iterate_members_by_name_length
    ite = @members.dup.sort_by { |x| x.size }
    while k = ite.shift()
      yield k
    end
  end

  def each(&block)
    enum_for(:iterate_members_by_name_length).each(&block)
  end
end

group1 = Group.new(["Nick", "Petros", "Ana"])

group1.each {|x| p x } 
```

这样我们就不会暴露变量的名字，并且我们可以控制枚举的顺序，例如在前面的例子中我们可以用`:iterate_members_alphabeticaly`
来改变`:iterate_members_by_name_length`

```
def each(&block)
  enum_for(:iterate_members_by_name_length).each(&block)
end 
```