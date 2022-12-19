# Rails 中改变数据库列的多种方法

> 原文：<https://dev.to/neshaz/multiple-ways-to-change-database-column-in-rails-34m0>

在开发 Rails 应用程序时，我们都不得不以某种方式**改变数据库列**。您可以更改**列名**和**列类型**，以及使用**类型转换**来更改列。

## 更改列名

在更改列之前，必须先创建它。让我们从[为概要文件创建一个模型](https://kolosek.com/rails-join-table/)开始。

```
class CreateProfiles < ActiveRecord::Migration
  def change
    create_table :profiles do |t|
      t.references :user
      t.string :avatar
      t.string :name, null: false
    end
  end
end 
```

一旦您运行 [rake db:migrate](https://kolosek.com/rake-db-commands/) ，概要文件数据将被迁移到数据库中。然后，让我们更改**列的名称**。*你怎么能这样做？*

有三种不同的方法可以实现:

*   进行新的迁移。
*   修复现有的迁移。
*   进行迁移以便**改变**该表。

**确保在所有示例结束时运行`rake db:migrate`，这样更改将保存在数据库中。**

### 进行新的迁移以改变列名

首先，您需要创建一个**新迁移** :

```
rails g migration rename_profile_name_to_full_name 
```

然后，在新生成的文件中添加一个 **rename_column** ，指定要更改的表和列:

```
class RenameProfileNameToFullName < ActiveRecord::Migration
  def change
    rename_column :profiles, :name, :full_name
  end
end 
```

你完了！

### 修复现有迁移

在我们开始之前，您应该[回滚您想要修复的迁移](https://kolosek.com/rake-db-commands/)。根据**何时**进行迁移，您可以通过以下方式完成:

*   最后一次迁移:`rake db:rollback`。
*   调用迁移版本:`rake db:migrate:down VERSION=YOUR_MIGRATION_VERSION`。

回滚完成后，您可以自由编辑模型。

```
class CreateProfiles < ActiveRecord::Migration
  def change
    create_table :profiles do |t|
      t.references :user
      t.string :avatar
      t.string :full_name, null: false
    end
  end
end 
```

### 制作新的迁移改变表

*只有在一次向表格添加多个变更时，才应使用此选项。*

首先创建一个新的迁移:

```
rails g migration change_profiles 
```

文件创建后，通过在它的位置添加一个**重命名**类型:
来指定您想要更改的列

```
class ChangeProfiles < ActiveRecord::Migration
  def change
    change_table :profiles do |t|
       t.references :user
       t.string :avatar
       t.rename :name, :full_name   # t.string -> t.rename
    end
  end
end 
```

每次更改数据库后，总是运行您的 [RSpec 测试](https://kolosek.com/rails-rspec-setup/),以确保一切都按预期运行。此外，在重命名列时，请注意 Rails 或 SQL 中的保留名称。这里有一个[保留字](https://reservedwords.herokuapp.com/)的列表可以帮到你。

## 改变列类型

这是每个程序员都会遇到的一个常见变化——你从一个具有一种**类型**属性的**模型**开始，然后你意识到它需要成为另一种类型。起初，您可能认为您应该将一个**配置文件名**存储为一个文本，而实际上它应该是一个字符串。

不要担心！您可以通过生成一个**新迁移** :
来轻松解决这个问题

```
rails g migration change_name_to_be_string_in_profiles 
```

现在，打开**迁移文件**并以如下方式编辑它:

```
class ChangeNameToBeStringInProfiles < ActiveRecord::Migration
  def change
    change_column :profiles, :name, :string
  end
end 
```

注意 **change_column 是一个不可逆的迁移**。如果您尝试回滚，将会导致错误。为了防止这种情况，修改迁移中常用的更改方法，使用两个独立的 **up 和 down 方法**，如下所示:

```
class ChangeNameToBeStringInProfiles < ActiveRecord::Migration
  def up
    change_column :profiles, :name, :string
  end

  def down
    change_column :profiles, :name, :text
  end
end 
```

测试一下吧！首先创建一个[概要文件工厂](https://kolosek.com/factory-girl-associations/)，并将**名称类型**改为一个字符串。

## 改变列类型-错误

有时候，简单地更改列类型是行不通的。当您试图编写一个将字符串列转换为整数的迁移时:

```
change_column :profile, :age, :integer 
```

您的数据库将会抱怨并显示一个错误:

```
PG::DatatypeMismatch: ERROR:  column "age" cannot be cast automatically to type integer
HINT:  Specify a USING expression to perform the conversion. 
```

为了解决这个问题，我们将遵循提示并在迁移中指定数据应该如何转换。有两种方法可以做到这一点:

*   `change_column :profile, :age, 'integer USING CAST(age AS integer)'`，
*   `change_column :profile, :age, :integer, using: 'age::integer'`。

就是这样！希望这些步骤有助于您根据自己的喜好更改数据库列。

本文原载于 [Kolosek 博客](https://kolosek.com/rails-change-database-column/)。