# Rails 的多态关联

> 原文：<https://dev.to/adjoa/rails-polymorphic-associations-511n>

在这篇文章中，我将概述多态关联在 Rails 中是如何工作的，以及如何将它们添加到您的项目中。

## 我是怎么来的

我现在在熨斗学校的最后一圈，完成我的最后一个项目。如果说过去 4 个项目建设周期教会了我什么，那就是考虑数据的重要性。*‘什么样的模型是合适的，它们在数据库中应该如何关联？’*

为了回答第一个问题，我想构建一个允许用户浏览即将发生的事件的应用程序。我还希望组织者能够登录并添加到列表中。所以，有两种型号:`Organizer`和`Event`。但是这两种模式都需要一个`Address`，对吗？3 款。但是，如何将它们联系起来呢？这个问题把我带入了一个陌生的领域:**多态关联**。

## 什么是多态性？

到目前为止，我一直在使用 Rails 的活动记录库，并逐渐理解了大多数可用的活动记录关联 : `belongs_to`、`has_many`、`has_many :through`，等等。但是在这种情况下，我需要一种方法将两个不同的模型与一个模型关联起来，同时保持事物干燥。具体来说，我需要一种方法来将组织者和事件与地址关联起来，而不需要为它们分别创建类似地址的模型。

对地址的多态关联被证明是实现这一点的完美工具，因为正如[文档](http://guides.rubyonrails.org/association_basics.html#polymorphic-associations)所解释的，使用这种类型的关联“一个模型可以在一个关联上属于多个其他模型”。

## 好的，但是我怎么实现这个呢？

我们可以把实现多态关联看作是在可重用模型和需要连接到它的模型之间创建一个接口。命名这种接口的传统方式是在模型名称 so、可寻址、可注释、可成像等的末尾添加一个“-able”。

```
class Address < ApplicationRecord
# name the interface to the Address model addressable
# establish that it is polymorphic
belongs_to :addressable, polymorphic: true
end

class Organizer < ApplicationRecord
# establish a relationship to the Address model 
# via the addressable interface
has_one :address, as: :addresssable
end

class Event < ApplicationRecord
# establish a relationship to the Address model
# via the addressable interface 
has_one :address, as: :addressable
end 
```

Enter fullscreen mode Exit fullscreen mode

我不会撒谎。当我看到那行字时，我的第一反应是，“酷。这么说在某个地方有一个`Addressable`类？。没有。那一行命名了我们多态模型上的接口。一旦多态模型和它的父模型之间的关系被创建，就可以像这样引用父模型:`@address.addressable`。我们还可以使用像`@organizer.address`和`@event.address`这样的语句。

要实现这一点，您需要在模型中声明一个**外键列**和一个**类型列**，它们将指向父模型`Organizer`或`Event`。

```
class CreateAddresses < ActiveRecord::Migration[5.2]
  def change
    create_table :address do |t|
    t.string :line1
    t.string :line2
    t.string :city
    t.string :state
    t.string :zip
    t.integer :addressable_id
    t.integer :addressable_type
    t.timestamps
    end

    add_index :addresses, [:addressable_type, :addressable_id]
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

使用`t.references`形式:
可以简化这种迁移

```
class CreateAddresses < ActiveRecord::Migration[5.2]
  def change
    create_table :address do |t|
    t.string :line1
    t.string :line2
    t.string :city
    t.string :state
    t.string :zip
    t.references :addressable, polymorphic: true, index: true
    t.timestamps
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

迁移可以这样生成:

```
rails g migration create_addresses line1:string line2:string city:string state:string zip:string addressable_id:integer addressable_type:string 
```

Enter fullscreen mode Exit fullscreen mode

或者，像这样:

```
rails g migration create_addresses line1:string line2:string city:string state:string zip:string addressable:references{polymorphic} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

多态关联允许一个模型属于许多模型，同时保持代码整洁。这种关联通过在多态或可重用模型上创建一个特殊的接口来支持这种关系，该接口允许其他模型与之交互。不相信这是你的模式？查看 Jared Carroll 的这篇[帖子](https://robots.thoughtbot.com/whats-the-deal-with-rails-polymorphic-associations)，他将引导读者更深入地探索这种方法。