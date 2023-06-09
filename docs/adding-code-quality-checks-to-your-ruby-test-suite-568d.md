# 向 Ruby 测试套件添加代码质量检查

> 原文：<https://dev.to/briankephart/adding-code-quality-checks-to-your-ruby-test-suite-568d>

随着时间的推移，我已经将一些静态分析工具集成到我的工作流程中。这些对于维护高质量的代码非常有帮助，但是手动运行它们是很乏味的。对于许多项目来说，这将在某种 CI 管道中实现自动化。然而，对于我目前的项目，没有自动化的 CI 流程。这个应用程序直接从命令行部署到 Heroku。因此，我决定看看是否可以将这些工具集成到我的测试套件中，将手动质量检查减少到一个步骤。

## 1 号工具:刹车手

自从我开始学习 Rails 开发，我就一直使用 [Brakeman](https://brakemanscanner.org) 来扫描我的代码，寻找安全漏洞。该工具使用静态分析来检查攻击漏洞，如 SQL 注入和跨站点脚本。Brakeman 被设计为从命令行运行，但是稍微深入研究一下文档就会发现[它可以作为一个应用程序中的 Ruby 库](https://brakemanscanner.org/docs/brakeman_as_a_library/)运行。按照这些指示，我打开了一个撬控制台，然后运行:

```
require 'brakeman'
=> true
Brakeman.run Rails.root.to_s
=> #<Brakeman::Tracker:0x00007fde2d546da8 ... tons of output in this object 
```

Enter fullscreen mode Exit fullscreen mode

该对象的输出太多，无法读取。接下来，我运行相同的命令，但是将结果存储为变量...

```
result = Brakeman.run Rails.root.to_s
=> # same result as before 
```

Enter fullscreen mode Exit fullscreen mode

...所以我可以查询它的方法和属性:

```
result.methods.sort
=> [:warnings] # There were tons of other methods in this array
result.warnings
=> []

result.instance_variables.sort
=> [:@errors] # Lots more here too
result.errors
=> [{:error=>"/Users/briankephart/Sites/some_app/app/jobs/some_job.rb:14 :: parse error on value \"**\" (error)",
   :backtrace=>["Could not parse /Users/briankephart/Sites/some_app/app/jobs/some_job.rb"]}] 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我没有收到任何安全警告，但有一个错误是由于 Brakeman 无法解析一个`**`操作符。这有助于向我展示这种形式的错误是可读的。请注意，错误和警告都以数组的形式返回。

利用我在撬控制台中学到的知识，我编写了下面的测试:

```
class BrakemanTest < ActiveSupport::TestCase
  require 'brakeman'

  test 'no brakeman errors or warnings' do
    result = Brakeman.run Rails.root.to_s
    assert_equal [], result.errors
    assert_equal [], result.warnings
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这非常有效。在我的测试运行期间，我可以看到 Brakeman 的输出，并且测试正确地响应了上面看到的错误。

## 工具二:RuboCop

最近，我在更多的工作中使用了 [RuboCop](http://batsov.com/rubocop/) 来检查语法错误、良好的样式和总体可读性。和 Brakeman 一样，Rubocop 是作为命令行工具设计的。不像布雷克曼，这些文件没有帮助我以任何其他方式使用它。相反，我不得不钻研源代码。我发现最重要的一行是[，这里是](https://github.com/rubocop-hq/rubocop/blob/9b7e480d99723b7ab17039bb3e280637cc3a7fb6/exe/rubocop#L13)，在定义命令行可执行文件的文件中。重点:

```
require 'rubocop'
cli = RuboCop::CLI.new
result = cli.run 
```

Enter fullscreen mode Exit fullscreen mode

我需要做的是实例化一个`RuboCop::CLI`对象并调用它的`#run`方法。查看这些文件，我发现[`#initialize`方法不带参数](https://github.com/rubocop-hq/rubocop/blob/9b7e480d99723b7ab17039bb3e280637cc3a7fb6/lib/rubocop/cli.rb#L21)，而[`#run`方法将来自命令行](https://github.com/rubocop-hq/rubocop/blob/9b7e480d99723b7ab17039bb3e280637cc3a7fb6/lib/rubocop/cli.rb#L36)的所有参数作为一个数组(不过我通常在命令行不带参数地运行`rubocop`)。回到撬控制台:

```
require 'rubocop'
=> true

RuboCop::CLI.new.run
# Inspecting 397 files
# ...
#
# 397 files inspected, no offenses detected
=> 0 
```

Enter fullscreen mode Exit fullscreen mode

注意返回值是`0`。了解到这一点，我写了下面的测试:

```
# Do not use this code
class RubocopTest < ActiveSupport::TestCase
  require 'rubocop'

  test 'no rubocop offenses' do
    assert_equal 0, RuboCop::CLI.new.run
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

不过，我注意到一些奇怪的事情。当我运行完整的测试套件时，RuboCop 检查了我所有的文件，但是当我单独运行上面的测试时，RuboCop 只检查了那个测试文件。我调用测试的上下文变成了 RuboCop 的上下文。解决这个问题的方法是在 run 方法中添加一个 path 参数，就像 RuboCop 允许在命令行中使用 path 参数一样。

```
# Use this instead of the code above
class RubocopTest < ActiveSupport::TestCase
  require 'rubocop'

  test 'no rubocop offenses' do
    cop = RuboCop::CLI.new
    args = [Rails.root.to_s]
    assert_equal 0, cop.run(args)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 工具 3:捆绑器审计

Bundler Audit 会检查您的 Gemfile.lock 中是否有已知易受攻击的 gem 版本。这个工具非常依赖命令行，事实证明只运行系统命令是最简单的。

```
class RubocopTest < ActiveSupport::TestCase
  require 'bundler/audit/cli'

  test 'no vulns in bundle' do
    `bundle audit update -q` # Update vulnerability database
    result = `bundle audit`  # Run the audit
    code = `echo $?`.squish  # Returns '0' if successful, otherwise '1'

    # Print the scan result as the error message if it fails.
    assert_equal '0', code, result

    # If successful, output the success message
    puts "\nMessage from bundler-audit: #{result}"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已！有了这三个小测试，无论何时运行测试套件，我都可以检查我的代码风格和安全性，不需要额外的步骤。