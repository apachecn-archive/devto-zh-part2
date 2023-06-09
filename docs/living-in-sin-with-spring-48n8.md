# 生活在罪恶中(和春天在一起)

> 原文：<https://dev.to/evilmartians/living-in-sin-with-spring-48n8>

Spring 是 Rails 应用程序开发中非常流行的工具(特别是自从它的[被添加到 Rails 默认的 Gemfile 中](https://github.com/rails/rails/pull/12958))。

随着代码库和依赖项数量的增长，应用程序的启动时间也会增加。可能需要十几秒钟`rails c`才会有反应。

Spring 试图通过一次加载应用程序代码并在必要时重新加载来解决这个问题。为什么要“尝试”？因为有时它不能反映变化。

例如，Spring 除了代码变化之外什么也检测不到:环境变量、随机化种子等等..

让我分享一些使用 Spring 的技巧。

## 春天 vs .外部依赖

假设您将一些配置存储在一个名为`config/my_config.txt`的文件中。

如果你改变这个文件，Spring 不会重新加载应用程序，除非你明确地告诉它这样做:

```
# config/spring.rb
Spring.watch "config/my_config.txt" 
```

Enter fullscreen mode Exit fullscreen mode

简单。

如果您的配置依赖于某个*不可观察的*怎么办？考虑一个例子:

```
# development.rb
Rails.application.configure do
  # We want to dump the structure.sql only if there are any
  # uncommitted changes migrations (i.e. do not re-generate the dump when
  # you're running migrations written by someone else) 
  res = `git status db/migrate/ --porcelain`
  ActiveRecord::Base.dump_schema_after_migration = res.nil? || res.present?
end 
```

Enter fullscreen mode Exit fullscreen mode

初始加载时，`ActiveRecord::Base.dump_schema_after_migration`的值被填充一次。如果您稍后添加迁移会怎样？不会生成转储。

为了处理这种情况，我们必须使用`Spring.after_fork`回调:

```
Spring.after_fork do
  res = `git status db/migrate/ --porcelain`
  ActiveRecord::Base.dump_schema_after_migration = res.nil? || res.present?
end 
```

Enter fullscreen mode Exit fullscreen mode

酷！那有效！

但是如果你的团队中有人不使用 Spring 呢？我们应该好好照顾它:

```
if defined?(::Spring::Application)
  Spring.after_fork do
    res = `git status db/migrate/ --porcelain`
    ActiveRecord::Base.dump_schema_after_migration = res.nil? || res.present?
  end
else
  res = `git status db/migrate/ --porcelain`
  ActiveRecord::Base.dump_schema_after_migration = res.nil? || res.present?
end 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**你可能已经注意到我们正在检查`::Spring::Application`常量的存在，而不仅仅是`::Spring`。原来[有些宝石即使没有`spring`本身也会启动`::Spring`常数。](https://github.com/palkan/test-prof/issues/47#issuecomment-358310037)

## 春季 vs. RSpec

RSpec 有一个以随机顺序运行测试的特性。

它是如何工作的？

它在加载时生成一个随机种子**，稍后[使用它](https://github.com/rspec/rspec-core/blob/v3.7.1/lib/rspec/core/ordering.rb#L25-L30)来安排测试。**

当使用 Spring 运行测试时，这个种子值不会被重载——您的测试顺序总是相同的(注意:当 Spring 服务器运行时)！

希望我们已经知道如何解决这个问题:

```
if defined?(::Spring::Application)
  Spring.after_fork do
    RSpec.configure do |config|
      # Make sure that seed value is randomized for each test run
      config.seed = rand(0xFFFF)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，该片段应该很可能包含在[默认`rails_helper.rb`](https://github.com/rspec/rspec-rails/blob/master/lib/generators/rspec/install/templates/spec/rails_helper.rb) 中。

## [T1。产物](#offspring)

不幸的是，在使用 Spring 时，几乎不可能避免这样的警告。

[![](img/b967afdb126962bd80e102fe068bfd47.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7eFjiHi8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hs93c4yrksfk08d9dtsl.png)

* [“运行测试运行”在 RubyConfBy 2017](https://www.youtube.com/watch?v=q52n4p0wkIs)

现在我更喜欢完全禁用它(而依靠 [`bootsnap`](https://github.com/Shopify/bootsnap) )。

如果你团队里的其他开发人员还爱着 Spring 怎么办？我们需要一种方法在本地禁用它。

Spring 提供了一种现成的方法:设置`DISABLE_SPRING=1`环境变量，就大功告成了。

这种方法有一些缺点:

*   您不能禁用每个项目的 Spring
*   不清楚 is 的禁用与否；您必须运行一个命令来查看环境变量的值
*   它不太适合容器。

我发现了一个更好的(IMO)方法:根据项目根中是否存在`.offspring`文件来切换 Spring 状态。

你所需要的就是在你的`bin/spring` :
中添加一行

```
!/usr/bin/env ruby

ENV['DISABLE_SPRING'] = '1' if File.exists?(File.join(__dir__, '../.offspring'))

# ... 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以使用以下命令启用/禁用 Spring:

```
$ touch .offspring #=> disables Spring
$ rm .offspring #=> enables Spring 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！

* * *

阅读更多关于 https://evilmartians.com/chronicles 的文章！