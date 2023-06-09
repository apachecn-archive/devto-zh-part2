# 全自动自动化测试(毁灭战士版)

> 原文：<https://dev.to/skatkov/fully-automated-automated-testing-doom-guy-edition--42oc>

[![Woohoo, Doom guy in notifications!](img/f1d8a2b29e4a146b536374fb9cf58e86.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MaQgJDoQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/879q596j4z6igz2j52fc.png)

我讨厌充满激情的重复性工作，我尽可能地避免它。在浏览 twitter 时，我偶然发现了 Sam Saffron 的视频，他在视频中谈到了他对改进自己的工作流程和构建开发工具的热爱。

首先，Sam 提到了 **autospec** 并演示了它的工作原理。对于这一部分，我真的是百感交集。我感到欣慰的是，我的测试套件用了不到 5 分钟，但同时，我内心也有这种不安的感觉...

我的工作流程由以下阶段组成:

1.  做小的代码修改
2.  运行测试
3.  如果失败，回滚或修复测试
4.  继续执行步骤 1

很难相信，但是大多数开发者整天都是这样做的。有些人手动执行测试，但我只是手动运行自动化测试。在我的例子中，大约 500 个断言需要 20 秒的时间...感觉重复的真快。

因此...这是萨姆...他没有每天例行执行 testsuite 吗？不会选择特定的文件进行测试。是的，请给我同样的。但是当我了解到 [autospec](https://github.com/arnvald/autospec) 和 [minitest](https://github.com/seattlerb/minitest) 不兼容时，我感觉更糟。我不想去 rspec。

那么在这种情况下，其他 minitest 用户会怎么做呢？你会怎么做？如果你已经使用了[守卫](https://github.com/guard/guard)，你可能想选择[守卫-迷你测试](https://github.com/guard/guard-minitest)。但是它只是运行所有的测试，而不关注坏的测试。如果你同意，那就继续用吧。如果你想要更好的东西，你可以进一步阅读。

## 让我们使用 minitest-autotest

这很容易添加。只要打开你的**gem 文件**并添加这些行:

```
 group :test do
      gem "minitest-autotest”
    end 
```

Enter fullscreen mode Exit fullscreen mode

在项目的根目录下创建一个尽可能简单的配置文件，并将其命名为**。自动测试**

```
 require 'autotest/restart'

    Autotest.add_hook :initialize do |at|
      at.testlib = "minitest/autorun"
    end 
```

Enter fullscreen mode Exit fullscreen mode

你并不是真的需要它，没有它它“只是工作”(c)。但是我们以后会需要它，这里显然缺少了一些东西。我不想一直检查我的控制台，以确保我没有弄坏任何东西。

## 更好的通知来救援！

我们已经谈过警卫了。Guard wiki 包含了非常好的关于系统通知和库的摘要，我们可以使用。因为我使用 OSX -我的两个选项是咆哮和[终端通知](https://github.com/julienXX/terminal-notifier)。我真的不想为 growl 付费，所以终端通知程序看起来是一份工作的完美候选人。让我们添加更多的代码，好吗？

我们的 **Gemfile** 应该有一个额外的行:

```
 group :test do
      gem "minitest-autotest"
      gem "terminal-notifier"
    end 
```

Enter fullscreen mode Exit fullscreen mode

还有我们的**。自动测试**代码变得更有趣一点:

```
 require 'autotest/restart'
    require 'terminal-notifier'

    Autotest.add_hook :initialize do |at|
      at.testlib = "minitest/autorun"
    end

    Autotest.add_hook :ran_command do |at|
      if at.failures.count > 0
        TerminalNotifier.notify(failed_message(at.failures.count),
          :title => 'minitest-autotest'
         )
      end
    end

    def failed_message(count)
      count.eql?(1) ?  "#{count} test failed" :  "#{count} tests failed"
    end 
```

Enter fullscreen mode Exit fullscreen mode

这已经好得多了——现在每次测试失败时我们都会收到通知。但这听起来是个问题:

*   我们被通知轰炸。自动测试关注错误的测试，并无限期地运行它(每次都生成通知)
*   如果我们回滚代码，我们必须再次在控制台中验证规范是否通过。很好，如果我们不用这样做的话。

## 让我们让通知更智能(不用机器学习...)

```
 require 'autotest/restart'
    require 'terminal-notifier'

    SKIP_MESSAGES = 5

    Autotest.add_hook :initialize do |at|
      at.testlib = "minitest/autorun"
      @counter = 0
    end

    Autotest.add_hook :all_good do |at|
      if @counter > 0
        TerminalNotifier.notify("All fixed!",
          :title => 'minitest-autotest'
        )

        @counter = 0
      end
    end

    Autotest.add_hook :ran_command do |at|
      if at.failures.count > 0
        if @counter.eql?(0) || @counter.eql?(SKIP_MESSAGES+1)
          TerminalNotifier.notify(failed_message(at.failures.count),
            :title => 'minitest-autotest'
          )

          @counter = 1
        else
          @counter += 1
        end
      end
    end

    def failed_message(count)
      count.eql?(1) ? "#{count} test failed" : "#{count} tests failed"
    end 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们添加了一个简单的计数器。基于该计数器，我们可以确定:

*   如果我们已经显示了错误通知，并显示“哇，一切都好了”通知
*   我们可以跳过一些通知，以免过于烦人

[![Not a cool version of notifications](img/82d5a11ccc218856398ca8d9797b68f4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0TPyAZpw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dh1dhf89vdj1qp2uwxsp.png)

看起来这对于我的日常活动来说几乎是完美的。但缺乏冷静..

## 毁灭战士登场..

如果你到了我这个年纪，你将永远不会忘记名为《毁灭战士》的史诗游戏。这也可能是一个纯粹的爱沙尼亚问题，因为 Jarmo Pertman(他和我同龄，也和我一样是爱沙尼亚人)很久以前为旧版本的 autotest 编写了一个 [doom-guy-bleeding indicator 插件，现在已经不能用了。](https://github.com/jarmo/autotest-doom)

让我们利用他的工作- [偷一些他在](https://github.com/jarmo/autotest-doom/tree/master/images)那里的图像，并把它们放到`/vendor/doomguy/`文件夹中。并窃取他的一些代码(感谢上帝是开源的)。让我们把所有这些加起来:

```
 require 'autotest/restart'
    require 'terminal-notifier'

    SKIP_MESSAGES = 5
    IMAGE_PATH = File.dirname(__FILE__) + "/vendor/doomguy/"

    Autotest.add_hook :initialize do |at|
      at.testlib = "minitest/autorun"
      @counter = 0
    end

    Autotest.add_hook :all_good do |at|
      if @counter > 0
        TerminalNotifier.notify("All fixed!",
          :title => 'minitest-autotest',
          :appIcon => IMAGE_PATH + "pass.png"
        )

        @counter = 0
      end
    end

    Autotest.add_hook :ran_command do |at|
      if at.failures.count > 0
        if @counter.eql?(0) || @counter.eql?(SKIP_MESSAGES+1)
          count = [(9 + at.failures.count) / 10 * 10, 50].min

          TerminalNotifier.notify(failed_message(at.failures.count),
            :title => 'minitest-autotest',
            :appIcon => IMAGE_PATH + "fail#{count}.png"
          )

          @counter = 1
        else
          @counter += 1
        end
      end
    end

    def failed_message(count)
      count.eql?(1) ? "#{count} test failed" : "#{count} tests failed"
    end 
```

Enter fullscreen mode Exit fullscreen mode

所以，现在我对结果和效率非常满意！

[![Woohoo, Doom guy in notifications!](img/f1d8a2b29e4a146b536374fb9cf58e86.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MaQgJDoQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/879q596j4z6igz2j52fc.png)

测试愉快！