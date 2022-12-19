# Ruby 异常的沉默:一部 Rails/PostgreSQL 数据库事务惊悚片

> 原文：<https://dev.to/evilmartians/the-silence-of-the-ruby-exceptions-a-railspostgresql-database-transaction-thriller-5e30>

*这是一个关于为什么你应该* ***永远不要*** *沉默数据库事务中的错误的故事。学习如何正确地使用事务，以及当不能使用事务时该做什么。剧透:用 PostgreSQL 顾问锁！*

我正在做一个项目，在这个项目中，用户可以从外部服务向我们的应用程序中导入大量的实体(姑且称之为*产品*)。每个产品都从外部 API 引入了更多相关数据。用户导入数百个产品的情况并不少见，而且每个产品都有需要获取的依赖项，因此可以想象整个过程需要一段时间(每个产品需要 30 到 60 秒)。由于用户可能会厌倦等待，随时点击“取消”按钮，应用程序应该仍然只对那些创建它的记录可用。

下面是我们的“可中断导入”是如何实现的:在导入开始时，在一个单独的数据库表中为每个排队的产品创建一个临时记录。然后，对于这些记录中的每一个，产生一个后台作业，该作业获取所有外部信息，将其保存在正确的位置(如果需要，通过创建关联)，最后删除一个临时记录。如果在后台作业启动时未找到记录(当用户取消导入时，所有临时记录都将被删除)，则该作业不会执行任何操作，会自动退出。

不管导入是否中断——没有临时记录意味着我们完成了。

我们的设计看起来简单可靠，但并不总是按计划进行。一个常见的错误描述是:“取消导入后，用户会看到一个导入记录的列表。但是，下次刷新页面时，列表中的记录会比最初显示的多”。

原因很明显:由于后台作业需要一分钟才能完成，即使是取消的导入也有“余晖”。

设计没有问题，但它导致了混乱的用户体验，所以我们需要用两种可能的方法来解决它:要么以某种方式识别并取消已经在进行的作业，要么等待最后一次导入完成，然后确认整个过程实际上已经“取消”。我选择了后者。

## 事务锁来拯救！

对于经常与关系数据库打交道的人来说，答案很清楚:使用事务！根据 Rails 文档中的[描述](http://api.rubyonrails.org/classes/ActiveRecord/Transactions/ClassMethods.html)，它们是“*保护块，其中 SQL 语句只有在作为一个原子动作*成功时才是永久的”。正如文档中所述，“*每当有许多必须一起执行或根本不执行的语句时，就应该使用事务块。*

最好记住，在大多数 RDBMS 中，在事务内部被更新的记录将被*锁定*，并且在事务完成之前不能被其他进程修改。用 [`SELECT FOR UPDATE`](https://api.rubyonrails.org/classes/ActiveRecord/Locking/Pessimistic.html) 取记录也是如此。

正是我们的案子！我使用了一个数据库事务来包装单个产品的复杂导入任务，并锁定任务记录以防被更改或删除:

```
ActiveRecord::Base.transaction do
  task = Import::Task.lock.find_by(id: id) # SELECT … FOR UPDATE means “I want to change it later, keep it for me”
  return unless task # it was already deleted so we're free
  # do some heavy lifting
  task.destroy
end 
```

现在，无论何时取消整个导入，这段代码都应该等待所有单个任务完成:

```
user.import_tasks.delete_all # waits while all imports will be finished 
```

简单大方！我已经在本地和试运行环境中运行了所有东西，没有发现任何问题，并部署到生产环境中。

## 没那么快...

我对自己的工作很满意，但醒来时却惊讶地发现了无数的日志错误和同事的抱怨。许多产品根本没有进口。在某些情况下，取消导入后只会出现一种产品。

错误消息没有多大意义:`PG::InFailedSqlTransaction`来自执行无辜的`SELECT`的代码。发生了什么事？

经过一整天的调试，我发现了问题的三个根本原因:

1.  将冲突记录并发插入数据库。
2.  PostgreSQL 中的自动事务回滚。
3.  沉默的 Ruby 异常。

**第一个问题:冲突记录的并发插入**
由于每个产品的导入作业需要一分钟，并且每个用户有多个作业，因此我们并行运行它们以节省时间。这些作业运行时创建的相关记录可以重叠，这意味着不同的产品可以具有相同的相关性，首先创建，然后重用。

在应用程序级别上对这些情况进行检查，但是现在我们使用事务，它们就变得没有用了:如果事务 A 已经创建了一个依赖记录，但是还没有提交，那么事务 B 不会知道它，并且会尝试创建一个副本。

**第二个问题:数据库级错误时的事务回滚**
我们可以使用下面的 DDL 来防止在数据库级创建重复记录:

```
ALTER TABLE deps ADD UNIQUE (user_id, chars); 
```

现在，如果事务 A 插入了一条新记录，而并行运行的事务 B 试图插入同一条记录，我们将得到一个错误:

```
BEGIN;
INSERT INTO deps (user_id, chars) VALUES (1, '{"same": "value"}');
-- Now it will block until first transaction will be finished
ERROR:  duplicate key value violates unique constraint "deps_user_id_chars_key"
DETAIL:  Key (user_id, chars)=(1, {"same": "value"}) already exists.
-- And will throw an error when first transaction have commited and it is become clear that we have a conflict 
```

通常被忽略的一点是，在遇到错误时，事务 B 将完全回滚，导致错误的步骤序列将被浪费。然而，一个无效的事务，**将仍然打开**,直到里面的所有代码都运行完。每次尝试使用有问题的记录都会导致另一个错误:

```
SELECT * FROM products;
ERROR:  current transaction is aborted, commands ignored until end of transaction block 
```

不言而喻，任何信息(不管您是在错误之前还是之后处理它)都不会被持久化:

```
COMMIT;  -- If we even try to save what we've done
ROLLBACK -- RDBMS will reject our attempt 
```

第三个问题:沉默的异常
那时，很明显，在应用程序逻辑中添加数据库事务这样一个常见且健壮的概念有效地打破了它。我别无选择，只能钻研别人的代码。这是当我开始看到这样的片段:

```
def process_stuff(data)
  # Magic, lots of magic
rescue StandardError
  nil # Happy debugging
end 
```

作者在这里想说的是“我们尝试了失败的东西，但我们不在乎，所以我们继续做东西”。虽然这种方法背后的原因可能是合理的(有些事情您无法在应用程序级别上验证)，但它使基于事务的逻辑不可实现:静默异常永远不会冒泡到`transaction`块，也不会触发回滚(因为 ActiveRecord 捕获所有异常，回滚一个事务并重新引发它们)。

## 完美风暴

以下是这三个因素如何结合在一起，创造了一个完美的 ~~storm~~ bug:

*   事务试图在数据库中插入一个冲突行，并遇到 Postgres 的“重复键”错误。然而，这个错误从来不会导致事务回滚，因为它在 Ruby 中是沉默的。
*   事务变得无效，但继续运行。当应用程序出于某种原因试图访问数据库时(即使是无意的`SELECT`来列出产品)，它会触发“当前事务被中止”错误。但这种例外也可能被压制...
*   你明白了。当应用程序内部不断出错时，没有人会知道，直到错误出现在第一段没有使用`rescue`块的代码中。因为它可能离最初失败的代码很远，所以调试变成了一场噩梦。

## PostgreSQL 的内置替代事务级锁

在应用程序代码中寻找`rescues`并重写整个导入逻辑是不可行的，所以我需要快速取胜，Postgres 给了我一个！它有一个内置的锁定工具，可以用作锁定事务中记录的替代方法。满足[会话级咨询锁](https://www.postgresql.org/docs/current/static/explicit-locking.html#ADVISORY-LOCKS)。我是这样使用它们的:

首先，我删除了我首先添加的所有事务包装器。无论如何，在一个事务中进行 API 交互或任何其他来自代码的非数据库副作用都不是一个好主意，因为即使成功回滚了数据库更改，副作用也会被应用，这可能会使您的应用程序处于不希望的状态。看一看[隔离器](https://github.com/palkan/isolator)宝石，它将帮助你确保你的副作用被很好地隔离。

然后，我将每个导入任务与一个*共享的*锁相关联，该锁基于某个惟一的键(例如，从一个用户 ID 和一个操作名的散列中创建):

```
SELECT pg_advisory_lock_shared(42, user.id); 
```

共享锁可以由任意多个会话同时获得。

“取消导入”操作从数据库中删除所有未完成的任务，并尝试为同一个键获取一个*独占*锁。这将不得不等到所有的*共享*锁被释放:

```
SELECT pg_advisory_lock(42, user.id) 
```

就是这样！

现在，取消导入将自动等待所有正在运行的导入任务完成。
此外，我们可以使用一点小技巧来为我们的独占锁设置超时，因为我们不想等待太久，如果某个任务由于某种原因挂起，就会阻塞 web 服务器线程:

```
transaction do
  execute("SET LOCAL lock_timeout = '30s'")
  execute("SELECT pg_advisory_lock(42, user.id)")
rescue ActiveRecord::LockWaitTimeout
  nil # we are tired of waiting and give up (transaction is already rolled back at this point)
end 
```

拯救 a `transaction`块外的异常*是安全的，因为 [ActiveRecord 已经回滚](https://github.com/rails/rails/blob/v5.2.0/activerecord/lib/active_record/connection_adapters/abstract/transaction.rb#L228-L236)。*

## 如何处理相同记录的并发插入？

不幸的是，我不知道有什么解决方案可以和*并发*插入一起使用。下面是我所知道的所有方法，但是它们都将锁定并发插入，直到第一个事务提交:

*   使用`INSERT … ON CONFLICT UPDATE`(从 PostgreSQL 9.5 开始可用)等待，直到第一个事务提交并返回由它插入的记录。
*   在对新记录运行验证之前，锁定数据库中的一些公共记录:这将等到第一个事务插入的记录变得可见，并且验证将按预期工作。
*   在对新记录运行验证之前，使用一些常见的咨询锁。

如果你不怕处理 Postgres 错误，你也可以这样做:

```
def import_all_the_things
  # Start transaction here and at some point do
  Dep.create(user_id, chars)
rescue ActiveRecord::RecordNotUnique
  retry
end 
```

并确保这段代码没有被包装在`transaction`块中。

> 为什么会锁？
> 
> UNIQUE 和 EXCLUDE 约束可以防止同时编写潜在的冲突。例如，如果您有唯一的整数列，并且一个事务插入 5，这将防止其他事务同时插入 5，但不会干扰插入 6 的事务。由于 PostgreSQL 支持的最不严格的事务隔离级别是`READ COMMITED`，第二个事务不允许看到第一个事务尚未提交的结果。必须等到第一个事务提交(然后它将失败)或回滚(然后它将成功)。阅读更多来自`EXCLUDE`约束作者的[这篇文章](http://thoughts.davisjeff.com/2010/09/25/exclusion-constraints-are-generalized-sql-unique/)。

## 防止未来的浩劫

如果您知道由于上述原因某些代码不能在事务中使用，请确保它永远不能被包装在事务中。以下是如何检测打开的事务并快速失败的方法:

```
# Here's how you define it
module NoTransactionAllowed
  class InTransactionError < RuntimeError; end

  def call(*)
    return super unless in_transaction?
    raise InTransactionError,
          "#{self.class.name} doesn't work reliably within a DB transaction"
  end

  def in_transaction?
    connection = ApplicationRecord.connection
    # service transactions (tests and database_cleaner) are not joinable
    connection.transaction_open? && connection.current_transaction.joinable?
  end
end

# And here's how you use it
class Deps::Import < BaseService
  prepend NoTransactionAllowed

  def call
    do_import
  rescue ActiveRecord::RecordNotUnique
    retry
  end
end 
```

现在，无论何时任何人试图在事务中包装一个有风险的服务，他们都会得到一个异常(当然，除非他们已经将它静音)。

## 包装完毕

从这个故事中得到的一个教训是:小心例外。只抓那些你真正知道怎么处理的，仅此而已。除非你有绝对的把握，否则不要沉默异常。越早发现错误，调试就越容易。并且不要过度使用数据库事务！

我们的 gems [隔离器](https://github.com/palkan/isolator)和 [after_commit_everywhere](https://github.com/Envek/after_commit_everywhere/) 将使您的交易完全安全。

## ![GitHub logo](../Images/375dfcc32199b4dedf2b526645c27ff7.png) [帕尔坎](https://github.com/palkan) / [隔离器](https://github.com/palkan/isolator)

### 检测数据库事务中的非原子交互

<article class="markdown-body entry-content p-5" itemprop="text">

[![Cult Of Martians](../Images/4fb2de9c35a4e044062cdc8f80104034.png)](http://cultofmartians.com/tasks/isolator.html)[![Gem Version](../Images/f50f4b79473029beca062f3cbf44be1f.png)](https://badge.fury.io/rb/isolator)[![Build Status](../Images/103571a7c4e448091ef18f3c9ef3ea8f.png)](https://travis-ci.org/palkan/isolator)

# 隔音装置

检测数据库事务中的非原子交互。

示例:

```
# HTTP calls within transaction
User.transaction do
  user = User.new(user_params)
  user.save
  # HTTP API call
  PaymentsService.charge!(user)
end
#=> raises Isolator::HTTPError
# background job
User.transaction do
  user.update!(confirmed_at: Time.now)
  UserMailer.successful_confirmation(user).deliver_later
end

#=> raises Isolator::BackgroundJobError
```

当然，隔离器也可以检测*隐式*事务。考虑一下这种非常常见的不良做法——从`after_create`回调中对后台作业进行排队:

```
class Comment < ApplicationRecord
  # the good way is to use after_create_commit
  # (or not use callbacks at all)
  after_create :notify_author

  private

  def notify_author
    CommentMailer.comment_created(self).deliver_later
  end
end

Comment.create(text: "Mars is watching you!")
#=> raises Isolator::BackgroundJobError
```

隔离器应该用于测试和准备阶段。

## 装置

将这一行添加到应用程序的 Gemfile 中:

```
# We suppose that Isolator is used in development and test
# environments.
group :development, :test
```

…</article>

[View on GitHub](https://github.com/palkan/isolator)

## ![GitHub logo](../Images/375dfcc32199b4dedf2b526645c27ff7.png) [恩维克](https://github.com/Envek)/[after _ commit _ everywhere](https://github.com/Envek/after_commit_everywhere)

### 在模型之外使用 ActiveRecord 事务性回调，实际上是在应用程序的任何地方。

<article class="markdown-body entry-content p-5" itemprop="text">

[![Gem Version](../Images/1a8161136361d98f872648c8c41092ce.png) ](https://rubygems.org/gems/after_commit_everywhere) [ ![Build Status](../Images/2ee50cf1bf5839643a57b839efdc591a.png)](https://travis-ci.org/Envek/after_commit_everywhere)

# `after_commit`到处都是

允许在 ActiveRecord 模型之外使用 ActiveRecord 事务性回调，几乎可以在应用程序的任何地方使用。

受到这些文章的启发:

*   https://dev.to/evilmartians/rails-aftercommit-everywhere-4j9g
*   [https://blog . arkency . com/2015/10/run-it-in-background-job-after-commit/](https://blog.arkency.com/2015/10/run-it-in-background-job-after-commit/)

[![Sponsored by Evil Martians](../Images/14ebd19001df8446f427d5e629d6a791.png)T2】](https://evilmartians.com/?utm_source=after_commit_everywhere&utm_campaign=project_page)

## 装置

将这一行添加到应用程序的 Gemfile 中:

```
gem 'after_commit_everywhere'
```

然后执行:

```
$ bundle 
```

或者自己安装为:

```
$ gem install after_commit_everywhere 
```

## 使用

推荐的用法是将它包含到您的基本服务类或任何内容中:

```
class ServiceObjectBtw
  include AfterCommitEverywhere
  def call
    ActiveRecord::Base.transaction do
      after_commit { puts "We're all done!" }
    end
  end
end
```

或者在您需要时延长它:

```
extend AfterCommitEverywhere
ActiveRecord::Base.transaction do
  after_commit { puts "We're all done!" }
end
```

就是这样！

但是主要的好处是它可以与嵌套的`transaction`块一起工作(甚至可以分布在代码库中的许多文件中):

```
include AfterCommitEverywhere
ActiveRecord::Base.transaction do
  puts "We're in transaction now"
  ActiveRecord
```

…</article>

[View on GitHub](https://github.com/Envek/after_commit_everywhere)

## 进一步阅读

[*打赏红宝石*](http://exceptionalruby.com/) *by Avdi Grimm* 。这本书将教你如何使用现有的 Ruby 异常，以及如何设计自己的异常。

[*使用原子事务为幂等 API*](https://brandur.org/http-transactions) *作者@Brandur* 。他的博客中充满了关于软件可靠性、Ruby 和 PostgreSQL 的精彩文章。

* * *

阅读更多由[邪恶火星人](https://evilmartians.com/)在 [dev.to](https://dev.to/evilmartians) 和[火星人编年史](https://evilmartians.com/chronicles)上撰写的开发者文章。