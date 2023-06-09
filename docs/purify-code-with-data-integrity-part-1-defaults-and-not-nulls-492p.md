# 用数据完整性净化代码。第 1 部分:默认值和非空值

> 原文：<https://dev.to/amplifr/purify-code-with-data-integrity-part-1-defaults-and-not-nulls-492p>

处理数据是任何应用程序的关键点。ActiveRecord 让 Rails 开发者更容易完成这个过程。它允许在不深入底层数据库的情况下管理数据。有时它会导致一些代码问题，如果我们更加注意数据库的结构和维护数据完整性，这些问题就不会出现了。

## 数据完整性？

[数据完整性](https://en.wikipedia.org/wiki/Data_integrity)是维护数据准确性和一致性的过程。

在`ActiveRecord`上下文中，我的意思是，`data integrity` -是通过更加关注其模式设计来保持底层数据库处于准确状态的*原则集。*

在本文中，我将回顾一些著名的**数据完整性技术**，它们对改进代码库非常有帮助，但有时由于某些原因被低估了。我们将看到它们是多么容易集成，以及它们如何帮助编写更清晰的代码。

## 默认值

你见过这样的代码吗？

```
# Example 1
order = Order.new(state: 'new')

# Example 2
class Order < ApplicationRecord
  validates :state, presence: true

  def before_validation
    self.state ||= 'new'
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

两个例子都有同样的问题:缺缺省值预置。它导致使用以下选项之一以非集中方式“手动”写入初始化:

1) 👎**设定值【到位】**(例一)。这个解决方案又快又脏。主要缺点是“正确的”缺省值的隐含性:不熟悉项目**的人不会知道将来设置**的建议缺省值，或者**期望该值应该已经存在**。这可能会导致错误。

第二点是，这样的“代码片段”**遍布整个代码库**，并且由于重复和分散，很难维护。

2) 👎**在【预保存】模式回调**中设置默认值
(从上例二)。但是这里我们也有隐含性。在哪里可以找到正确的初始值`before_save`、`before_commit`、`before_validation`？答案取决于作者的想象力😊。

第二个问题是新模型中没有初始化默认值(例二)，即使我们在保存前设置了默认值，在新模型对象中仍然没有初始化:

```
order = Order.new
order.state
=> nil
order.save
order.state
=> 'new' 
```

Enter fullscreen mode Exit fullscreen mode

👍最好的选择是在数据库模式中预设默认值，通过设计让 ActiveRecord 很好地工作:

```
create_table :orders do |t|
  ...
  t.integer :state, default:'new'
end

# default value work by-default :)
Order.new.state
=> 'new' 
```

Enter fullscreen mode Exit fullscreen mode

它允许一个新的对象具有默认的列设置。ActiveRecord 将读取您的表的元数据，并为任何新的`User`类型的模型预设默认值。

### 设置数据库

简单的例子是在创建新表时设置默认值。操作简单，无阻塞:

```
create_table :order do |t|
  ...
  t.integer :state, default: 'new'
end 
```

Enter fullscreen mode Exit fullscreen mode

否则，向现有表中添加一个带有默认值的列会更复杂，因为这将强制为所有行设置默认值。它会在高负载数据库中锁定一段时间的读/写操作。

如果你幸运地站在前沿，在 Postgres 11 上运行你的应用程序，事情会变得简单得多。PostgreSQL 11 不接触现有行，但[会‘延迟’更新它们](https://leopard.in.ua/2016/09/20/safe-and-unsafe-operations-postgresql#.W_Oy4nozZbW)。

如果你没有使用 Postgres 11，设置缺省值约束的安全方法包括两个简单的步骤:
1)创建可空的列而不设置缺省值
2)改变列的缺省值

```
 add_column :orders, :state, :text
  change_column_default :orders, :state, 'new' 
```

Enter fullscreen mode Exit fullscreen mode

此后，所有新行都将具有默认值，活动记录将使用这些元数据自动预设默认值。数据库中所有现有的行仍然有`NULL`值，需要“手动”填充它们来美化 ruby 代码。然后，您可以依赖每一行的列的存在。

❗The 的要点是保持兼容性:改变数据库预置第一次和船舶。然后，当列中不再有空值时，从 Ruby 代码中删除默认值初始化。

## 非空约束

你面对过这样的代码吗？

```
class ImageUploadService
  def call
    ...
    image.upload_tries_count ||= 0
    image.upload_tries_count += 1
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这个问题类似于缺少默认值。当一个列可为空时——web 不能确定没有`nil`值。每次我们必须使用列时，它强制写`nil`-检查逻辑。

如果我们保证该列没有`nil`值，代码将变得简单得多:

```
image.upload_tries_count += 1 
```

Enter fullscreen mode Exit fullscreen mode

解决方案如下:

> not-null 约束仅指定列不能采用 null 值。

### 创建一个新表并带有非空列

设置`default`选项，该列将具有默认设置的值:

```
create_table :user do |t|
  ...
  t.integer :points_count, null: false, default: 0
end 
```

Enter fullscreen mode Exit fullscreen mode

### 向现有表中添加非空列

[这篇文章](https://medium.com/klaxit-techblog/zero-downtime-migrations-with-activerecord-47528abe5136)详细描述了用 default 添加新列的安全方法，但这里是总结步骤:

*   用某个值填充数据库中的所有空值
*   将列设置为非空(通过迁移)
*   删除“就地”模型初始化代码

❗The 填充重载表的值的更好的方法是用批处理(事务中的每一个批处理)进行更新，并在时间上进行拉伸。

在 Ruby world 中，自动化的标准方式是`Rake` -tasks，这里是 rake-task 的示例，用批处理更新 users 表 users:

```
desc 'Fill user points count'
namespace :migrations do
  task fill_user_points_count: :environment do
    total_updated = 0
    loop do
      updated_rows = User
        .where(points_count: nil).limit(10_000)
        .update_all(points_count: 0).to_i
      break if updated_rows.zero?

      total_updated += updated_rows
      puts "updated #{total_updated} users"
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我推荐在迁移中使用 rake-task 而不是更新列，因为它
给了**更多的控制来在生产**上运行它(如果出现问题，您可以随时中断进程)。这里有一些技巧性的迁移来自动设置临时环境和其他开发人员机器的默认值:

```
class FillOrderSourceAndSetNotNull < ActiveRecord::Migration[5.0]
  disable_ddl_transaction!

  def up
    if Rails.env.production?
      puts "Run bundle exec migrations:fill_user_points_count in production"
    else
      Rake::Task['migrations:fill_user_points_count'].invoke
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

此迁移禁用迁移的事务模式，并以 10 000 行为一批更新用户。如果表在一个事务中更新，它允许没有大表锁。

经过这一步-我们可以很容易地设置 table not-null:

```
change_column_null :users, :points_count, false 
```

Enter fullscreen mode Exit fullscreen mode

并将更改部署到生产环境中。
最后一步是删除零校验代码，如下所示:

```
user.points_count ||= 0 
```

Enter fullscreen mode Exit fullscreen mode

## 总结起来

遵循这两个简单的规则:“设置默认值”和“设置列不为空”对于编写更清晰的代码和避免许多问题非常有用。

感谢阅读！下一个话题再见。

## 附加链接:

*   [官方文件中 PorstreSQL 约束的许多细节](https://www.postgresql.org/docs/10/ddl-constraints.html)
*   [大容量 PostgreSQL 的安全和不安全操作](https://leopard.in.ua/2016/09/20/safe-and-unsafe-operations-postgresql#.W-2GDzkSw0M)
*   [具有数据完整性的编码轨道](https://www.bignerdranch.com/blog/coding-rails-with-data-integrity/)
*   [使用 ActiveRecord 实现零停机迁移](https://medium.com/klaxit-techblog/zero-downtime-migrations-with-activerecord-47528abe5136)