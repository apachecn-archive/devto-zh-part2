# 如何正确使用 rake db 命令

> 原文：<https://dev.to/neshaz/how-to-use-rake-db-commands-in-the-correct-way--50o2>

Rake 是内置在 T2 Ruby 和 Rails T3 中的一个工具，它提供了一种管理数据库变化的有效方式。*只需使用命令行，你就可以轻松地将数据库更改迁移到服务器上！*

在应用程序开发过程中，您可能会问自己:

*   *使用 **rake 数据库命令**时*会发生什么？
*   我应该在什么时候使用它们？

让我们看看在开发应用程序时如何使用这些命令来更改数据库！

## 创建

`$ rake db:create`
当您**第一次创建**您的 Rails 应用程序时，它还没有数据库。为了启动它，您需要确保数据库已经启动并正在运行。

就像建议为每个环境使用[不同的宝石](https://kolosek.com/rails-bundle-install-and-gemfile/)一样，您也应该创建三个数据库，分别用于**开发**、**测试**和**生产**环境。您可以在您的`config/database.yml`文件中配置它们。

```
default: &default
  adapter: postgresql
  encoding: unicode
  username: username
  password: password
  host: localhost
  port: 5432

development:
  <<: *default
  database: story_dev

test:
  <<: *default
  database: story_test

production:
  <<: *default
  database: story 
```

## 迁徙

`rake db:migrate`
**迁移**在数据库中设置表格。当您运行迁移命令时，它会在`db/migrate/`中查找任何 ruby 文件，并从最旧的开始执行。每个迁移文件名的开头都有一个**时间戳**。

> *每次您迁移数据库或对其进行任何更改，如添加行或列、添加表格或更改数据类型，您都必须运行此命令以使更改反映在您的数据库中。[Rails 应用中的数据库 Rake 任务列表](https://rails.devcamp.com/learn-ruby-on-rails-from-scratch/building-your-first-rails-application/list-of-database-rake-tasks-in-a-rails-application)*

当您运行像 [rails generate scaffold](https://kolosek.com/rails-scaffold/) 、`rails generate model`或`rails generate migration`这样的命令时，就会创建迁移。

这里有一个例子，当[上传图片](https://kolosek.com/carrierwave-upload-multiple-images/)时，你如何使用`rake db:migrate`。确保不要在迁移文件中创建数据！

## 正在初始化

`rake db:schema:load`
与`rake db:migrate`运行尚未运行的迁移不同，`rake db:schema:load`将`db/schema.rb`中已经生成的模式加载到数据库中。

在以下情况下，请始终使用此命令:

*   您在第一次运行应用程序**。**
*   当您**删除数据库**并需要再次创建它时。

小心！如果您在生产服务器上运行`rake db:schema:load`,您最终会删除所有的生产数据。

## 播种

我们总是有默认数据，我们希望在我们的应用程序中有这些数据用于测试目的。**种子命令**的存在是为了自动化这个过程。

*示例*:创建一个管理员用户，并将其数据存储在`db/seed.rb`文件中。当您运行`rake db:seed`时，它会将所有管理数据加载到您的应用程序中。

```
Admin.create!(email: 'admin@kolosek.com', 
              password: 'password', 
              password_confirmation: 'password') 
```

Rails seeding 通常用于**开发**和/或**阶段**环境，在生产中只有少数用途。*你不希望你的生产应用程序播种虚拟用户！*

## 回滚

`rake db:rollback`
您是在不想迁移的情况下创建了迁移，还是只是改变了想法？*不要害怕！*当您运行此命令时，它将查看上次创建的迁移并撤消它！

*示例*:让我们从用`:role`作为整数创建一个**新迁移**和**运行迁移**开始。然后我们决定把它变成一个字符串。因此，我们将编辑新创建的迁移并再次运行它，但是什么也没有发生，您的[测试和工厂](https://kolosek.com/factory-girl-associations/)将会失败。

```
class CreateRoles < ActiveRecord::Migration
  def change
    create_table :roles do |t|
      t.integer :role # we will change this to t.string :role
      t.references :user
      t.timestamps
    end
  end
end 
```

**为什么这不起作用？**
只有最后创建的迁移才使用`rake db:migrate`命令运行。这意味着编辑现有迁移不会带来任何变化。为了使这个工作，您将需要运行`rake db:rollback`来代替。这将告诉 Rails 做两件事:

1.  撤消您刚才对数据库所做的最后更改。
2.  更新迁移时间戳。##删除`rake db:drop`有时我们想删除所有的数据和表格，从头开始。这就是`rake db:drop`的作用。*如果你想保留你已经拥有的数据，一定要在运行这个命令之前备份它。*

删除数据库也将删除任何**模式冲突**或**坏数据**。一旦数据库被删除，您将希望通过重新创建数据库、运行迁移和播种数据来重新开始这个过程。在重建数据库之后，确保您的 [RSpec 测试](https://kolosek.com/rails-rspec-setup/)通过！

确保你没有连接到数据库，否则它不会掉线。

## 复位

`rake db:reset`
有时你可能需要删除本地数据库，重新开始从`db/seeds.rb`加载数据。这是一个有用的命令，当您还在弄清楚您的模式，并且经常需要向现有模型添加字段时。

一旦使用复位命令，它将执行以下操作:

1.  删除数据库:`rake db:drop`
2.  加载模式:`rake db:schema:load`
3.  播种数据:`rake db:seed`

**为什么是 db:schema:load 而不是 db:migrate？**
`rake db:schema:load`比`rake db:migrate`快得多，因为它加载我们已经从`db/schema.rb`生成的模式，而不是再次经历所有的迁移。

希望这有助于你理解 rake 数据库命令之间的主要区别！

本文原载于 [Kolosek 博客](https://kolosek.com/rake-db-commands/)。