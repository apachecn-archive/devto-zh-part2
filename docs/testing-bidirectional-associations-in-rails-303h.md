# 测试 Rails 中的双向关联

> 原文：<https://dev.to/briankephart/testing-bidirectional-associations-in-rails-303h>

作为(1)自学者和(2)工作场所中唯一的开发者，我发现的一件事是，当我学习一项新技术时，我从来不知道它有多普遍。最近，我在和一个更有经验的开发人员讨论我写的一些测试，结果发现我使用的技术是他以前没有见过的。

## 一些背景

当我编写这些测试时，我正处于为我工作的音乐学院构建大型应用程序的早期阶段。此时，我们对我们想要的用户界面、屏幕、快乐之路等等知之甚少。然而，由于学校非常成熟，应用程序正在构建以支持我们当前的实践，我非常清楚我们需要建模什么数据，以及模型应该如何相互关联。这让我陷入了一个奇怪的境地，我不是自顶向下地构建特性，而是构建我们的数据模型。这导致我创建了几十个模型，这些模型还没有做很多事情，但包含了我们的数据结构，以支持未来的功能开发。

在这一点上，我还没有以任何形式使用测试驱动开发，所以我试图在创建这些模型时使用它。最特别的是，我想用它来确保我的模型之间的关联是正确的。我使用 Minitest 的默认 Rails 测试堆栈，没有 Rspec 或工厂。

## 问题

当创建这么多模型时，很容易意外地忽略一个模型上存在的关联，就像这样:

```
# app/models/parent.rb
has_many: :children

# app/models/child.rb
# OOPS! Nothing here. Should say has_one: :parent 
```

Enter fullscreen mode Exit fullscreen mode

我希望能够在我的测试中写下这些:

```
class ParentTest < ActiveSupport::TestCase
  test "Parent associations" do
    check_bidirectional_associations Parent
  end
end

class ChildTest < ActiveSupport::TestCase
  test "Child associations" do
    check_bidirectional_associations Child
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我发现了一个 ActiveRecord 方法可以帮我:`Model.reflect_on_all_associations` [(docs)](http://api.rubyonrails.org/classes/ActiveRecord/Reflection/ClassMethods.html#method-i-reflect_on_all_associations) 。该方法将返回在当前模型上定义的关联数组。因为我在测试中使用了 fixture，所以我可以从 fixture 文件中获得关联模型的第一个实例，然后检查它是否将反向关联定义为一个方法。

首先，我必须确保在夹具文件中有一个模型实例。在测试中，通过使用模型名称作为单一常量来调用模型。例子:`SomeModel.first`，不是`some_model.first`或者`SomeModels.first`T3】

```
def check_bidirectional_associations(model)
  associations = model.reflect_on_all_associations
  associations.each do |assoc|
    # attempt to assign the first instance of model to a variable. Must first convert assoc[:name] to singular constant.
    assert associated_object = assoc.name.to_s.singularize.camelize.constantize.first
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果没有夹具，测试将失败。但是，它需要一个错误消息。

```
assert associated_object = assoc.name.to_s.singularize.camelize.constantize.first,
       "#{assoc.name.to_s.camelize} not present in fixtures. Check test/fixtures/#{assoc.name.to_s.pluralize.underscore}.yml" 
```

Enter fullscreen mode Exit fullscreen mode

那更好。现在，如果 fixture 丢失了，测试会告诉我应该修复哪个文件。进入下一步，检查关联。首先，我需要模型名作为方法:

```
model_method = model.to_s.underscore 
```

Enter fullscreen mode Exit fullscreen mode

现在来看看关联对象是否响应模型方法:

```
assert associated_object.respond_to?(model_method) 
```

Enter fullscreen mode Exit fullscreen mode

当然，有些联想是复数。最好做成:

```
assert associated_object.respond_to?(model_method) || associated_object.respond_to?(model_method.pluralize) 
```

Enter fullscreen mode Exit fullscreen mode

好多了。现在来看看测试失败时的错误信息:

```
assert associated_object.respond_to?(model_method) || associated_object.respond_to?(model_method.pluralize),
       "#{associated_object.class.to_s.camelize} is not properly associated with #{model.to_s}. Check for the correct association in app/models/#{assoc.name.to_s.underscore}.rb." 
```

Enter fullscreen mode Exit fullscreen mode

厉害！现在我有一个测试，它将告诉我两个模型之间的关系何时只在其中一个模型上定义。下面是在 test_helper.rb:
中定义的方法

```
# test/test_helper.rb
class ActiveSupport::TestCase
  def check_bidirectional_associations(model)
    associations = model.reflect_on_all_associations
    associations.each do |assoc|
      assert associated_object = assoc.name.to_s.singularize.camelize.constantize.first,
             "#{assoc.name.to_s.camelize} not present in fixtures. Check test/fixtures/#{assoc.name.to_s.pluralize.underscore}.yml"
      model_method = model.to_s.underscore
      assert associated_object.respond_to?(model_method) || associated_object.respond_to?(model_method.pluralize),
             "#{associated_object.class.to_s.camelize} is not properly associated with #{model.to_s}. Check for the correct association in app/models/#{assoc.name.to_s.underscore}.rb."
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 现在事情变得复杂了

当然，ActiveRecord 有很多类型的关联。上面的测试并不适用于所有人。有些情况最好跳过:

```
associations.each do |assoc|

  # not necessarily bidirectional
  next if assoc.options[:through].present?

  # polymorphic has_many or has_one
  next if assoc.options[:as].present?

  # polymorphic belongs_to
  next if assoc.options[:polymorphic].present?

  # test it

  end
end 
```

Enter fullscreen mode Exit fullscreen mode

其他需要修改的:

```
associations.each do |assoc|

  # stuff from previous example

  # self join (model has an association to the same model)
  if assoc.klass == model
    assert assoc.options[:inverse_of].present?,
           "Only half of a self-join association is defined in app/models/#{model.to_s.underscore}.rb. The inverse of :#{assoc.name} is not defined."
    assert model.first.respond_to? assoc.options[:inverse_of],
           "The inverse of :#{assoc.name} is not functioning correctly. Check app/models/#{model.to_s.underscore}.rb."

  # Explicitly defined inverse association. Must have an inverse defined in both models.
  elsif assoc.options[:inverse_of].present?
    assert associated_object = assoc.klass.first,
           "#{assoc.name.to_s.camelize} not present in fixtures. Check test/fixtures/#{assoc.name.to_s.pluralize.underscore}.yml.\nAssociation name: #{assoc.name}\nAssociated object: #{assoc.klass}"
    model_method = assoc.options[:inverse_of].to_s
    assert associated_object.respond_to?(model_method) || associated_object.respond_to?(model_method.pluralize),
           "#{associated_object.class.to_s.camelize} is not properly associated with #{model.to_s} via :#{model_method}. Check for the correct association in app/models/#{model.to_s.underscore}.rb."

  # Using an alias to reference another model. Should probably have inverse relationships explicitly defined.
  elsif assoc.name != assoc.klass
    assert associated_object = assoc.klass.first,
           "#{assoc.name.to_s.camelize} not present in fixtures. Check test/fixtures/#{assoc.name.to_s.pluralize.underscore}.yml"
    model_method = model.to_s.underscore
    assert associated_object.respond_to?(model_method) || associated_object.respond_to?(model_method.pluralize),
           "#{associated_object.class.to_s.camelize} is not properly associated with #{model.to_s} using :#{model_method} as the inverse of :#{assoc.name}. Check for the correct associations and :inverse_of attributes in app/models/#{assoc.klass.to_s.underscore}.rb and app/models/#{model.to_s.underscore}.rb."

  # Normal bidirectional association
  else
    # do the stuff from earlier
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 好人

这个方法对我定义数据结构非常有帮助。我能够快速地开发，因为我知道如果我没有得到正确的关联，我的测试会警告我，并给出有用的错误消息，告诉我应该检查哪些文件。我所要做的就是将`check_bidirectional_associations ModelName`添加到我的每个模型测试文件中。

## 越少越好

这是一个巨大的条件语句！不，真的，我甚至还没有把整个方法放上去。这里:

```
def check_bidirectional_associations(model)
  associations = model.reflect_on_all_associations
  associations.each do |assoc|
    # not necessarily bidirectional
    next if assoc.options[:through].present?

    # polymorphic has_many or has_one
    next if assoc.options[:as].present?

    # polymorphic belongs_to
    next if assoc.options[:polymorphic].present?

    # self join
    if assoc.klass == model
      assert assoc.options[:inverse_of].present?,
             "Only half of a self-join association is defined in app/models/#{model.to_s.underscore}.rb. The inverse of :#{assoc.name} is not defined."
      assert model.first.respond_to? assoc.options[:inverse_of],
             "The inverse of :#{assoc.name} is not functioning correctly. Check app/models/#{model.to_s.underscore}.rb."

    # Explicitly defined inverse association. Must have an inverse defined in both models.
    elsif assoc.options[:inverse_of].present?
      assert associated_object = assoc.klass.first,
             "#{assoc.name.to_s.camelize} not present in fixtures. Check test/fixtures/#{assoc.name.to_s.pluralize.underscore}.yml.\nAssociation name: #{assoc.name}\nAssociated object: #{assoc.klass}"
      model_method = assoc.options[:inverse_of].to_s
      assert associated_object.respond_to?(model_method) || associated_object.respond_to?(model_method.pluralize),
             "#{associated_object.class.to_s.camelize} is not properly associated with #{model.to_s} via :#{model_method}. Check for the correct association in app/models/#{model.to_s.underscore}.rb."

    # Using an alias to reference another model. Should probably have inverse relationships explicitly defined.
    elsif assoc.name != assoc.klass
      assert associated_object = assoc.klass.first,
             "#{assoc.name.to_s.camelize} not present in fixtures. Check test/fixtures/#{assoc.name.to_s.pluralize.underscore}.yml"
      model_method = model.to_s.underscore
      assert associated_object.respond_to?(model_method) || associated_object.respond_to?(model_method.pluralize),
             "#{associated_object.class.to_s.camelize} is not properly associated with #{model.to_s} using :#{model_method} as the inverse of :#{assoc.name}. Check for the correct associations and :inverse_of attributes in app/models/#{assoc.klass.to_s.underscore}.rb and app/models/#{model.to_s.underscore}.rb."

    # Normal bidirectional association
    else
      assert associated_object = assoc.name.to_s.singularize.camelize.constantize.first,
             "#{assoc.name.to_s.camelize} not present in fixtures. Check test/fixtures/#{assoc.name.to_s.pluralize.underscore}.yml"
      model_method = model.to_s.underscore
      assert associated_object.respond_to?(model_method) || associated_object.respond_to?(model_method.pluralize),
             "#{associated_object.class.to_s.camelize} is not properly associated with #{model.to_s}. Check for the correct association in app/models/#{assoc.name.to_s.underscore}.rb."
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

庞大的条件句一般被认为是代码味。不过，我在这里并不真正关心，因为这些测试是明确设计来支持快速开发的，而且他们做得非常好。一旦模型包含了被测试的业务逻辑，这种方法就不再有用了。任何导致该方法失败的事情都会导致其他测试失败。换句话说，这些是我不会重构的测试，如果它们以后变红的话。我会把它们删除，然后继续做我的其他测试。

这是好的做法吗？我认为一般不会。我怀疑许多开发人员需要制作几十个不包含任何重要逻辑的模型来测试。这是在特定发展阶段对特定问题的解决方案。不过，这是一次很棒的学习经历，它帮助我适应了 TDD 的一般实践。编写一个我能够在所有模型测试中重用的测试助手方法也是令人满意的，因为我投入的时间得到了数倍的回报。