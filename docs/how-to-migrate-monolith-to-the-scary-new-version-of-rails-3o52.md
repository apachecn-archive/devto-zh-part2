# 如何将 monolith 迁移到可怕的新版 Rails

> 原文：<https://dev.to/amplifr/how-to-migrate-monolith-to-the-scary-new-version-of-rails-3o52>

迁移到新版本的 Rails 看起来总是很吓人。考虑升级会带来许多问题，尤其是如果您的应用程序是一个旧的整体。最关键的点是:

*   在不阻碍功能开发的情况下升级
*   影响尽可能少的用户
*   确保所有逻辑在新版 Rails 上都能正常工作

在本文中，我们将回顾一些常见的技术和技巧，这些技术和技巧让 Amplifr 的团队可以轻松地升级到最新版本的 Rails。

❗The 的主要提示是**只增量升级到 Rails 的[最新补丁版本](https://rubygems.org/gems/rails/versions)一次**，因为大部分 bug 已经被解决了。这种选择最大限度地减少了每次升级所需的重大更改。

我们完成了三个升级步骤:`4.2.0 => 5.0.7`、`5.0.7 => 5.1.6`、`5.1.6 => 5.2.0`。

以下是一个 Rails 版本升级周期并将其交付到生产环境的标准步骤:

1.  **弃用:**清除所有阻碍下一个 Rails 版本更新的弃用
2.  **双重引导:**使应用程序可以在当前和下一个版本的 Rails 上运行
3.  **绿色测试:**通过两条轨道的测试(矩阵构建)
4.  **部署→监控→(恢复)→修复:**切换到 Rails 的新版本，监控应用程序的新故障和错误，如果错误很严重并且您需要时间来修复它们，则进行恢复
5.  继续做第四点，直到一切正常

重复上述操作，直到您使用顶部导轨版本😁

# 折旧

不赞成的观点认为，当第三方依赖关系得到更新时，某些东西可能会损坏。因为我们要升级 Rails——我们必须为下一个版本的 Rails 修复拦截器。

主要问题是先把目标版本屏蔽器和修复它们分开。通过使用这种危险而丑陋的手段来隐藏未来版本的反对意见对我们来说很有效:

```
 if Rails::VERSION::MAJOR == 5
      # temporary mute test params deprecations
      ActiveSupport::Deprecation.behavior = lambda do |msg, stack|
        unless msg =~ /Using positional arguments in functional tests has been deprecated/
          ActiveSupport::Deprecation::DEFAULT_BEHAVIORS[:stderr].call(msg, stack)
        end
      end
    end 
```

Enter fullscreen mode Exit fullscreen mode

❗:如果可能的话，最好一次修复所有的问题，这样可以节省很多时间

# 双开机

想法是同时支持两个版本的 Rails。让应用程序在当前的 Rails 版本上保持可运行性，可以保证如果出现问题，您能够在其上运行应用程序。让应用程序在下一个 Rails 版本上运行将允许您不断升级。

根据环境变量`RAILS_NEXT` :
让你的应用程序能够运行两个版本的 Rails，这个命令应该引导当前的 Rails 版本

```
 bundle exec rails c 
```

Enter fullscreen mode Exit fullscreen mode

这将引导应用程序使用新版本的 Rails:

```
 RAILS_NEXT=1 bundle exec rails c 
```

Enter fullscreen mode Exit fullscreen mode

方法是将这段代码放在`Gemfile` :
的开头，稍微修改一下`bundler`

```
module Bundler::SharedHelpers
  def default_lockfile=(path)
    @default_lockfile = path
  end

  def default_lockfile
    @default_lockfile ||= Pathname.new("#{default_gemfile}.lock")
  end
end

module ::Kernel
  def rails_next?
    ENV["RAILS_NEXT"] == '1'
  end
end

if rails_next?
  Bundler::SharedHelpers.default_lockfile =
    Pathname.new("#{Bundler::SharedHelpers.default_gemfile}_next.lock")

  class Bundler::Dsl
    unless method_defined?(:to_definition_unpatched)
      alias_method :to_definition_unpatched, 
                   :to_definition
    end

    def to_definition(_bad_lockfile, unlock)
      to_definition_unpatched(Bundler::SharedHelpers.default_lockfile, unlock)
    end
  end
end

gem 'rails' #relax dependency 
```

Enter fullscreen mode Exit fullscreen mode

这段代码做了几件事:

*   定义了`rails_next`？方法，所以这个方法可以在任何地方使用:在`Gemfile`中，在你的 ruby 应用程序的代码中，等等
*   定义 singleton-method 来保存当前绑定器的锁文件名
*   如果`rails_next?`被激活，将锁定文件名重置为`Gemfile_next.lock`

❗Note，我们**为每个版本的 Rails** 使用一个`Gemfile`和两个锁文件。维护起来就容易多了。

# 依赖关系

首先，放松`Gemfile`中的 Rails 依赖来启用更高版本的 Rails:
如果你有 Rails 版本被这样锁定:

```
gem 'rails', '~> 4.2.3' 
```

Enter fullscreen mode Exit fullscreen mode

打开它。

```
gem 'rails' 
```

Enter fullscreen mode Exit fullscreen mode

它允许`Gemfile`兼容所有版本的 Rails。当前和下一个 rails 版本将被锁定在相应的`lock`-文件中。

然后将`Gemfile.lock`复制到`Gemfile_next.lock`并尝试更新`rails_next` :
上的 rails

```
 $ cp Gemfile.lock Gemfile_next.lock
    # run bundle for fixing dependencies matedata in original Gemfile
    $ bundle
    # run bundle on rails_next to update rails
    $ RAILS_NEXT=1 bundle update rails 
```

Enter fullscreen mode Exit fullscreen mode

在这里，你会得到 bundler 努力工作，以解决依赖关系，并可能在中途失败。失败意味着某些依赖项不允许与较新版本的 Rails 一起工作。以下是一些场景:

*   Gem 的当前版本被锁定，不受支持。它有新的版本来支持新版本的 Rails。如果只有`rails_next?`起作用，我们必须更新 gem:

```
 if rails_next?
      gem 'devise'
    else
      gem 'devise', git: 'https://github.com/plataformatec/devise', branch: '3-stable'
    end 
```

Enter fullscreen mode Exit fullscreen mode

*   Gem 从上面锁定了 Rails 版本:`rails < 5.0`，但可能适用于更新的版本。你需要解开依赖，试一试。尝试从 GitHub 本地克隆这个 gem，在`gemspec`文件中放松对 Rails 的依赖，在本地插入 gem。如果有效-很好，转到下一个拦截器，如果无效-尝试其他解决方案。
*   由于不兼容，Gem 锁定了 Rails，并且不能与新版本一起使用。你需要解开依赖，理解 gem 如何工作，问题出在哪里，然后解决所有的冲突，并在这个 gem 中测试新版本的 rails。

处理最后的情况-你将不得不更新 gem 源代码。首先，您将修复您的 fork 中的问题。尽管这种方式看起来非常简单、快速和容易——但是有许多理由避免将它保存在 forks 中:

*   作者(或一组作者)以集中的方式维护他们的宝石。没有人有兴趣支持你的叉子😢你的叉子是你的问题！
*   分叉可以让你快速解决问题，不需要设计和测试，但是它只是推迟了问题的解决。将来从远程源升级 fork 时，您将会得到回报
*   以集中的方式解决问题(通过发送拉式请求)有助于其他开发人员处理相同的问题。请提交拉动式请求，自由投稿！

❗The 修复 gem 源代码的更好方法是**向 gem 的存储库**发送拉请求。

不幸的是，一些宝石因为许多原因没有人来维护。这是一个信号，让你检查你的依赖，为了更受欢迎的东西而抛弃那些不可维护的。

❗Note 说你必须**更新所有的 gem 两次来支持两个版本的 Rails** ，并提交`Gemfile.lock`和`Gemfile_next.lock`来保持它们相似。

```
 bundle update some-gem
    RAILS_NEXT=1 bundle update some-gem 
```

Enter fullscreen mode Exit fullscreen mode

# 可运行的应用

当所有的依赖项都没问题，并且您的 bundler 成功解析了`rails_next` -您就准备好进行下一步了-检查应用程序是否可运行。

您可以尝试运行 Rails 控制台或本地 web 服务器，但是最好从运行测试开始:

```
 RAILS_NEXT=1 bundle exec rspec 
```

Enter fullscreen mode Exit fullscreen mode

测试很有可能无法开始，甚至失败。这里的主要问题是你的代码或一些 gems 的代码与新的 Rails 版本不兼容。所以只有一个建议:深入并修复它。

❗我们使用的一个技巧是暂时使用`rails_next?`方法编写 **Rails 版本相关代码** :

```
 project.run_callbacks(:commit) if rails_next? 
```

Enter fullscreen mode Exit fullscreen mode

它有助于编写特定于版本的代码:

```
 if rails_next?
      redirect_to user_omniauth_authorize_path(:instagram)
    else
      redirect_to user_instagram_omniauth_authorize_path
    end 
```

Enter fullscreen mode Exit fullscreen mode

# 绿色测试

将额外的构建添加到您的 CI 中，以便使用下一个 Rails 版本运行测试。如果您在 TravisCI 上运行，它可能是这样的:

```
 matrix:
      include:
        - env: RAILS_NEXT=0
        - env: RAILS_NEXT=1 
```

Enter fullscreen mode Exit fullscreen mode

如果您的下一个 Rails 版本测试还没有通过——您可以像这样将它们标记为可选的:

```
 matrix:
      include:
        - env: RAILS_NEXT=0
        - env: RAILS_NEXT=1
      allow_failures:
        - env: RAILS_NEXT=1 
```

Enter fullscreen mode Exit fullscreen mode

这种方法将让您将矩阵构建合并到主分支，而不影响特性开发，并且您将能够并行地进行测试。

最后，您需要使您的测试对两个版本都是绿色的。

❗ **不经过测试就升级——不是一个好主意**🙂，因为不可能确保关键的应用程序部分在下一个版本中工作。

# 部署&班长

是时候展示了！

如果您有试运行——首先在那里部署，并手动检查主要功能。这将风险降至最低，并有可能抓住一些漏洞。

在生产中运行更有效。它让真实的用户在真实的案例中测试应用程序。主要的想法是“一点点”地部署新的 Rails 版本，并记住恢复策略，这将帮助您不影响用户太多或太长时间。

根据您的基础架构，您可能更喜欢使用其中一种推广策略，或者将它们结合使用:

1.  使用新的 Rails 版本应用程序实例为特定端点提供服务，从低负载到高负载开始，或者从非关键开始。
2.  上菜占总负荷的百分比，然后增加运行率。
3.  在低负载时间(如果有)部署应用程序，例如在晚上
4.  全力以赴，保持冷静🙂

我们决定用新的 Rails 版本推出 all Amplifr，因为这是最进步的方式，对我们来说，回滚到以前的 Rails 版本需要几分钟时间。

一旦你推出了应用程序(或它的一部分)并让真正的用户面对它，保持监控错误是很重要的。有两种了解问题的一般方法:

1.  使用一些工具自动监控，如`Honeybadger`、`Rollbar`、`Sentry`、`NewRelic`。**拥有它——是必须的！通过 Slack、电子邮件或其他方式在线通知他们问题和缺陷的原因是很有用的**
2.  真正的用户对于像 Amplifr 这样的小团队来说是至关重要的。无论你对应用程序进行了多少测试，测试覆盖率有多高，你都有可能漏掉一些错误，尤其是在业务逻辑方面。永远对你的用户友好和耐心，当你需要他们的帮助时，他们会以同样的方式表现。🙂

如果出现问题——通过改变环境变量`RAILS_NEXT`的值来恢复。具体方式取决于您拥有的基础设施:

```
 export RAILS_NEXT=0 
```

Enter fullscreen mode Exit fullscreen mode

❗ **回滚并不意味着失败**，所以不用担心。它让您有时间修复新发现的错误，同时应用程序可以在以前版本的 Rails 上稳定运行。**坚持下去！**

我们有几次回滚来修复 Amplifr 中的关键错误。

❗After 成功升级，让应用程序在新版 Rails 上长期工作，以最小化风险并修复所有 bug。

## 打扫卫生

升级成功后，我们已经清理了源代码，切断了对之前 Rails 的支持。

*   默认情况下，让应用程序运行新版本的 Rails。您必须复制下一个`lock`-文件内容并提交它:cat Gemfile _ next . lock>Gemfile . lock git commit...
*   部署应用并切换 RAILS_NEXT `1` → `0`
*   截掉`Gemfile_next.lock`:git RM Gemfile _ next . lock git commit
*   删除所有版本相关的代码；你很容易就能找到:grep -ir "rails_next\？。/
*   从`Gemfile`中移除`Bunder`的黑客

我们还没有删除双重引导代码，并在未更新的`lock`-文件上运行了几天。🤦‍♂️，小心点。🙂

❗If，你将继续升级，从下一个版本开始乞讨。

## 待在边缘

如果您使用的是最后一个稳定版本，您可能更愿意停止使用它。另一个选择是在 Rails 的下一个版本中继续升级`master`分支。

```
 gem 'rails', git: 'git@github.com/rails/rails' 
```

Enter fullscreen mode Exit fullscreen mode

这种方式不是单值的，但可能是渐进的。大多数 gem 不支持新版本，你必须在那里做出贡献。这可能很危险，会让您发现 Rails 代码中不稳定特性的 bug。但是从另一方面来说——你将继续关注 Rails 的新版本，使你的升级在未来变得轻松舒适，并且有助于 Ruby 的 gems 源代码。

# 结论

尽管升级 Rails 版本看起来工作量很大，但是使用最新版本是非常必要的，原因如下:

*   它更加稳定、快速
*   它有新的特点
*   它允许您使用最新版本的 gem

迭代地、一步一步地进行升级，并牢记恢复策略可以使您的升级不那么痛苦。

# 进一步阅读(观看):

*   Luke Francl 的文章
*   RailsConf 2017: [将大型应用升级到 Rails 5](https://www.youtube.com/watch?v=I-2Xy3RS1ns) 作者 Rafael Franç
*   [在 Shopify 的博客上将 Shopify 升级到 Rails 5](https://engineering.shopify.com/blogs/engineering/upgrading-shopify-to-rails-5-0)
*   [Shopify 博客上的 Shopify 弃用工具包使用案例](https://engineering.shopify.com/blogs/engineering/introducing-the-deprecation-toolkit)
*   [十年铁路升级](https://speakerdeck.com/jnraine/ten-years-of-rails-upgrades)Jordan Raine 的幻灯片
*   [我们如何在零停机时间内升级大型 Ruby on Rails monolith】](https://bytes.babbel.com/en/articles/2015-12-14-rails-4-upgrade.html)
*   [Kir Shatrov 在 Shopify 上谈论 Rails 升级(俄语)](https://www.youtube.com/watch?v=nqGxzUGtkNw)