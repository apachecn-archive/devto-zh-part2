# 带 Rails 的 Postgres 枚举

> 原文：<https://dev.to/amplifr/postgres-enums-with-rails-4ld0>

## 简介

Enum -是用户定义的数据类型，表示一组命名值。一般来说，枚举的概念旨在提供两个主要目的:

*   为了使代码库更具可读性和人性化
*   通过限制枚举类型变量的可能赋值来增加额外的类型安全

枚举是基于整数类型的数据类型，这对性能肯定有好处。如果我们要处理大量的数据。

## 采用“老派”方式

使用“枚举”的最常见方式是某个对象的明确状态。尽管 pure Ruby 中没有枚举，但我们可以用常量或符号来模拟它们。下面是老式 Rails 3(遗留)数据模型源代码的示例:

```
 class Account < ActiveRecord::Base
      PAID_STATUS = 'paid'
      OVERDUE_STATUS = 'ovedue'
      BLOCKED_STATUS = 'blocked'

      STATUSES = [PAID_STATUS, OVERDUE_STATUS, BLOCKED_STATUS].freeze
      validates :status, presence: true, inclusion: {in: STATUSES}

      def paid?
        self.status == PAID_STATUS
      end
    end

    # everywhere we work with Account instance - it is much easier
    # to deal with helper predicate instead of comparing values "in place"
    if account.paid?
      # do something special for the user who pays
    end 
```

它为代码库提供了更好的可读性，并且对于新开发人员来说看起来更友好！但是它有太多的样板——常量定义和值检查。这种检查遍布整个代码库，使得维护更加困难。

## “铁轨之道”

从[开始，Rails 4.1 活动记录提供了更简单的方式来使用枚举](https://apidock.com/rails/ActiveRecord/Enum) :

```
 class Conversation < ActiveRecord::Base
      enum status: [ :active, :archived ]
    end 
```

我们已经有了一些有用的变异方法、校验值方法和开箱即用的作用域，例如:

```
 conversation.active! # sets the active status for object
    conversation.active? # checks if the object have active status
    Conversation.action.find_each { |c| puts c.status } # provides scope for searching 
```

我们已经让源代码更具可读性和可维护性。
验证是现成的，如果没有定义值，它会引发`ArgumentError`。

```
 conversation.status = :unknown
    => ArgumentError: 'unknown' is not a valid channel 
```

默认行为假设使用 numeric 作为列的基础类型:

```
 create_table :conversations do |t|
      t.integer :status
    end 
```

并且活动记录将为您命名的常量赋值:

```
 [1] pry(main)> Conversation.statuses
    => {"active"=>0, "archived"=>1} 
```

一切都很好，直到我们需要给你的枚举增加价值(并且首先做到):

```
 class Conversation < ActiveRecord::Base
      enum status: [ :initial, :active, :archived ]
    end 
```

此更改将强制活动记录从零开始重新枚举值。和值将会混淆并使您的应用程序逻辑崩溃:

```
 > Conversation.statuses
    => {"initial"=>0, "active"=>1, "archived"=>2} 
```

## 用 Postgres 列举的“铁路方式”

我们得分着去做:

*   使用模型定义显式定义枚举值
*   引入 Postgres 枚举类型来声明底层数据库列数据类型

下面是具有枚举值的`channel`属性的模型:

```
 class UserNotification < ActiveRecord::Base
      # define rails enum and the underlying values to use for every enum-value
      enum channel: {
        email: 'email',
        webpush: 'webpush',
        telegram: 'telegram'
      }
    end 
```

这意味着我们将有`email`、`webpush`和`telegram` -作为列`channel`的值。

写迁移以创建 Postgres 枚举类型`user_notification_channel` :

```
 class CreateUserNotifications < ActiveRecord::Migration
      def up
        execute <<-DDL CREATE TYPE user_notification_channel AS ENUM (
            'email', 'webpush', 'telegram'
          ); DDL

        create_table :user_notifications do |t|
          ...
          t.column :channel, :user_notification_channel
        end
      end

      def down
        drop_column :channel, :user_notification_channel
        execute "DROP type user_notification_channel;"
      end
    end 
```

这个技巧让我们在 Ruby:
中有了“字符串”值

```
 notification.channel
    => "email" 
```

但是[在数据库行](https://www.postgresql.org/docs/10/static/datatype-enum.html#id-1.5.7.15.8)中只占 4 个字节。

这种复杂的“Rails&Postgres”方式的主要好处是:

*   可读和用户友好的源代码
*   Ruby 中枚举的字符串表示，在数据库中有最佳的数据存储
*   数据库级别的更多类型安全
*   脱离 Rails 应用程序上下文的有意义且清晰的数据库结构

## 如何举例子

### 将现有文本列迁移到 Postgres 枚举

这样做的原因是:

*   减小表的大小(固定的 4 字节枚举与字符串长度)
*   提高类型安全性和数据库数据一致性
*   使数据库值更有意义和友好(脱离模型上下文)

```
 class CreateUserNotifications < ActiveRecord::Migration
      def up
        execute <<-DDL ALTER TABLE user_notifications
          ALTER COLUMN channel TYPE user_notification_channel
          USING channel::user_notification_channel; DDL
      end

      def down
        # moves column back to have text data-type
        execute <<-DDL ALTER TABLE user_notifications
          ALTER COLUMN channel TYPE text
          USING channel::text; DDL
      end
    end 
```

这工作得很好，没有破坏任何变化！

向现有枚举迁移添加值:

```
 class ExpandValueOfUserNotificationChannels < ActiveRecord::Migration
      def up
        execute <<-DDL ALTER TYPE user_notification_channel ADD VALUE 'post_pigeon'; DDL
      end
    end 
```

### 从现有枚举中删除值

Postgres 不允许从现有枚举中删除值，但这里有一些删除值的技巧:

*   将列迁移到文本
*   重新创建新的枚举类型
*   迁移回新的枚举

```
 class RemoveTelegramFromUserNotificationChannel < ActiveRecord::Migration
      def up
        execute <<-DDL ALTER TABLE user_notifications 
          ALTER COLUMN channel TYPE text
          USING channel::text;

          DROP TYPE user_notification_channel;

          CREATE TYPE user_notification_channel AS ENUM (
            'email', 'webpush'
          );

          ALTER TABLE user_notifications
          ALTER COLUMN channel TYPE user_notification_channel
          USING channel::user_notification_channel;
        end
    end 
```

### 从数字 Rails 枚举迁移到 Postgres 枚举

假设我们有 Rails 枚举和数据库中的整数通道列:

```
 class UserNotification < ActiveRecord::Base
      enum channel: [:email, :webpush, :telegram]
    end 
```

每个 Rails 枚举值的数值可以通过下面的代码得到:

```
 [1] pry(main)> UserNotification.channels
    => {"email"=>0, "webpush"=>1, "telegram"=>3} 
```

只需通过改变类型来改变列，并将值从数字转换为枚举:

```
 class MigrateNotificationsChannelToPostgresEnum < ActiveRecord::Migration
      def up
        execute <<-DDL ALTER TABLE user_notifications
          ALTER COLUMN channel TYPE user_notification_channel
          USING CASE channel
                  WHEN 0 THEN 'email'::user_notification_channel
                  WHEN 1 THEN 'webpush'::user_notification_channel
                  WHEN 2 THEN 'telegram'::user_notification_channel
                  ELSE RAISE 'UNKNOWN VALUE'
          END DDL
      end
    end 
```

这种迁移会将数值转换为枚举。利润！🙂

## 结论

Postgres 枚举是伟大的和有益的！它们非常容易与 Rails 集成！
它们有助于确保一组确定值的数据一致性。增加 Rails 应用程序上下文之外的数据的可读性。
它们也有更好的性能(与文本值相比)。

感谢阅读。