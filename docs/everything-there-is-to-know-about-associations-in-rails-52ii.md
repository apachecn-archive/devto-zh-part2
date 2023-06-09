# Rails 关联关于 Rails 中的关联的所有知识

> 原文：<https://dev.to/neshaz/everything-there-is-to-know-about-associations-in-rails-52ii>

一个**关联**是两个**活动记录模型**之间的连接。这使得*在代码中对记录执行各种操作变得更加容易。我将协会分为四类:*

1.  一对一
2.  一对多
3.  多对多
4.  多态一对多

如果你是 Ruby on Rails 的新手，一定要准备好你的 [rails 项目](https://kolosek.com/ruby-programming-beginners-guide/)，并查看如何[在 Rails 中创建模型](https://kolosek.com/rails-scaffold/)。

## 一对一

当您建立一对一的关系时，您说的是一个记录正好包含另一个模型的一个实例。例如，如果您的应用程序中的每个用户只有一个概要文件，您可以将您的模型描述为:

```
class User < ApplicationRecord       class Profile < ApplicationRecord
  has_one :profile                     belongs_to :user
end                                  end 
```

给定关系中的一个模型将有`has_one`方法调用，另一个将有`belongs_to`。它用于描述哪个模型包含**一个外键**对另一个的引用，在您的例子中，它是概要文件模型。

[![one-to-one](img/8d5fa0e52a1ef003818cc48358baba6f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LNBZ2vh0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.kraken.io/kk8yWPxzXVfBD3654oMN/dd9c653b0c0005b92cdf4a6bdcc10a3e/one-to-one.png)

用 [RSpec 测试](https://kolosek.com/rails-rspec-setup/)覆盖你的关联，以确保一切都按照你想要的方式运行。

## 一对多

一个**一对多**关联是最常用的关系。这种关联表明模型 A 的每个实例可以有**零个或多个**另一个模型 B 的实例，而模型 B 只属于**一个**模型 A

让我们通过例子来验证一下。假设您想要创建一个用户可以编写多个故事的应用程序，您的模型应该是这样的:

```
class User < ApplicationRecord       class Story < ApplicationRecord
  has_many :stories                     belongs_to :user
end                                  end 
```

在这里，决定哪个模型将有`has_many`和哪个将有`belongs_to`比在**一对一**关系中更重要，因为它改变了你的应用程序的逻辑。在这两种情况下，第二个模型都以**外键**的形式包含一个对第一个模型的引用。

第二个模型不知道第一个模型与它的关系——它不知道第一个模型是否引用了不止一个模型，还是只引用了一个模型。

[![one-to-many](img/92cb3875448df711b2e6d5dc04769082.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--N_IgQ358--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.kraken.io/kk8yWPxzXVfBD3654oMN/ec6c6326b0948276a487af75a13ee4c9/one-to-many.png)

创建的关系越多，关联测试就越复杂。你应该看看如何创建你自己的[关联工厂](https://kolosek.com/factory-girl-associations/)来使你的测试生活更容易。

## 多对多

**多对多**关联稍微复杂一点，可以用两种方式处理，**“有且属于多个”**、**“有多个直通”**关系。

### 拥有并属于许多

一个`has_and_belongs_to_many`关联创建了与另一个模型的直接**多对多**连接。它比另一个更简单，因为它只需要从两个模型中调用`has_and_belongs_to_many`。

[![has-and-belongs-to-many](img/5cd7d255fa9117e939768fc28af3f0f4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C9eEMddp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.kraken.io/kk8yWPxzXVfBD3654oMN/6e59bce7bc11d74d4f3b42aa264f0db8/has-and-belongs-to-many.png)

*举例:*比如说，一个用户可以有许多不同的角色，同一个角色可以包含许多用户，你的模型应该是这样的:

```
class User < ApplicationRecord       class Role < ApplicationRecord
  has_and_belongs_to_many :roles       has_and_belongs_to_many :users
end                                  end 
```

您需要为这个关联创建一个**连接表**。这是一个连接两个不同模型的表。连接表是在一个单独的[迁移](https://kolosek.com/rake-db-commands/)中用 rails 函数`create_join_table :user, :role`创建的。

```
class CreateUserRoles < ActiveRecord::Migration
  def change
    create_table :user_roles, id: false do |t|
      t.references :user, index: true, foreign_key: true
      t.references :role, index: true, foreign_key: true
    end
  end
end 
```

这是一个非常简单的方法，但是*你不能直接访问相关对象*，你只能保存对两个模型的引用，除此之外别无其他。

### 有许多穿越

定义一个**多对多**关联的另一种方式是使用**有多个通过**的关联类型。这里你应该定义一个**独立模型**，来处理两个不同模型之间的连接。

[![has_many_through](img/f065cb0a96420e7576141eb5003439ac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wGc6RL15--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.kraken.io/kk8yWPxzXVfBD3654oMN/78cbb30deb837728cb6d6153b31c6fb3/has_many_through.png)

不要写下一个新的例子，你应该检查一下这个例子。它解释了你应该知道的关于这个协会的一切。

用`has_and_belongs_to_many`联想的例子，这次三个模型应该这样写:

```
class User < ApplicationRecord
  has_many :user_roles
  has_many :roles, through: :user_roles
end

class UserRoles < ApplicationRecord
  belongs_to :user
  belongs_to :role
end

class Role < ApplicationRecord
  has_many :user_roles
  has_many :users, through: :user_roles
end 
```

这个**关联**将使你能够做类似`user.role`的事情，并获得所有连接的第二模型实例的列表。它还将使您能够访问特定于第一个和第二个模型之间关系的数据。

## 多态

**多态联想**是我们可用的最高级联想。当您有一个模型可能属于单个关联上的许多不同模型时，您可以使用它。

[![polymorphic](img/099463999d5aabf305447bd4d9cfcb6c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AgiX5yeP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.kraken.io/kk8yWPxzXVfBD3654oMN/6058d06ae05bb5c5ea77ebbc9fa1aabf/polymorphic.png)

让我们想象你希望能够为用户和故事写评论。你希望两个模型都是可评论的。下面是如何声明的:

```
class Comment < ApplicationRecord
  belongs_to :commentable, polymorphic: true
end

class Employee < ApplicationRecord
  has_many :comment, as: :commentable
end

class Product < ApplicationRecord
  has_many :comment, as: :commentable
end 
```

你可以把多态的`belongs_to`声明想象成**建立一个任何其他模型都可以使用的接口**。要声明**多态接口**，您需要在模型中声明一个外键列和一个类型列。完成后，您应该[运行迁移](https://kolosek.com/rake-db-commands/)。

```
class CreateComments < ActiveRecord::Migration
  def change
    create_table :comments do |t|
      t.text :body
      t.integer :commentable_id
      t.string :commentable_type
      t.timestamps
    end

    add_index :comments, :commentable_id
  end
end 
```

这个迁移可以通过使用**引用** :
来简化

```
class CreateComments < ActiveRecord::Migration
  def change
    create_table :comments do |t|
      t.text :body
      t.references :commentable, polymorphic: true, index: true
      t.timestamps
    end
  end
end 
```

## 附加选项

在定义模型之间的**关系时，您可以使用一些额外的选项，我将介绍两个最常用的选项。**

### 类名

> *如果另一个模型的名称不能从关联名称中导出，可以使用`:class_name`选项提供模型名称。你可以在[铁路指南](http://guides.rubyonrails.org/association_basics.html#options-for-belongs-to-class-name)上了解更多。*

*示例:*您希望 belongs_to 关系调用一个**作者**，但是有两个问题:

1.  没有以这种方式调用的模型。
2.  故事表缺少一个`author_id`字段。

*轻松搞定！*要使这个工作并防止您的[测试失败](https://kolosek.com/rspec-controller-test/)，您只需指定`:foreign_key`和类的实际名称:

```
class Story < ApplicationRecord
  belongs_to :author, class_name: 'User', foreign_key: 'user_id'
end 
```

### 依赖

当您想要删除孤立记录时，您可以使用此选项，因为它们会导致各种问题。当您**删除或销毁**一个与模型 B 相关联的模型 A 时，就会创建孤立记录，但是模型 B 并没有在这个过程中被删除。

```
class User < ApplicationRecord
  has_many :stories, dependent: :destroy
end 
```

*举例:*假设，一个叫 Maria 的用户写了很多故事。然后，您从数据库中删除了 Maria，但是故事仍然将 user_id 列设置为 Maria 的 id。这些故事被称为**孤儿记录**。

感谢您的阅读！

本文原载于 [Kolosek 博客](https://kolosek.com/rails-associations/?utm_source=dvt)。