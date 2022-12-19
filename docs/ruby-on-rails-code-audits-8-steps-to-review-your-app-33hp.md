# Ruby on Rails 代码审核:审核应用程序的 8 个步骤

> 原文：<https://dev.to/planetargon/ruby-on-rails-code-audits-8-steps-to-review-your-app-33hp>

作为 Ruby on Rails 社区的一员已经超过 13 年了，我已经看到了很多应用程序代码库。从 2007 年开始，我们的团队开始向希望获得第三方投资评估的企业家提供 [Rails 代码审计](https://www.planetargon.com/services/ruby-on-rails-code-audit)。

在我们审计的每一个 Rails 应用程序中，我们都有一个不断发展的长长的项目列表需要评估。在代码审计结束时，我们的客户会收到一份文档，该文档会向他们介绍我们的发现，并列出建议的后续步骤。

在本文中，我将分享我们在开始审计代码库时检查的八个项目(及其背后的基本原理)。我的目的是帮助您提供一个需要检查的事项列表，并考虑将清理作为整理一些可能的技术债务和/或疏忽的一种方式。

事不宜迟。以下是您应该在代码库上检查的八件事情，这些事情不需要您了解或学习太多关于应用程序的业务逻辑。

如果这些听起来显而易见，那就太好了。你在正确的轨道上。继续努力。

## 1。有没有. ruby 版本的文件？

如果你正在使用像 [rbenv](https://github.com/rbenv/rbenv) 或 [rvm](https://rvm.io/) 这样的工具，`.ruby-version`是一个非常有用的方法，可以让一个开发团队确保他们都在使用同一个版本的 Ruby。偶尔，我们会遇到一个应用程序，它没有在应用程序的代码库中指定 Ruby 的版本，我们必须 a)要求开发人员让我们知道和/或 b)查看在生产环境中运行的是什么版本。

这些信息可以(也应该)存在于`Gemfile`中，稍后我们会进一步讨论。

如果该文件不存在，那么有可能您没有使用 rbenv 或 rvm，您可能需要考虑这一点。([我们更喜欢 rbenv](https://github.com/rbenv/rbenv/wiki/Why-rbenv%3F) 。)

## 2。gem 文件看起来怎么样？

你们中有多少人记得管理 Bundler 之前的 Rubygem 版本？UFTA。我敢打赌，自从我们对不包含`Gemfile`的 Ruby on Rails 应用程序进行代码审计以来，已经有五年多了。很高兴那些日子已经过去了。

话虽如此，这里有几件事我们会推荐给你的同事，让你的`Gemfile`保持良好的组织和记录。

### Ruby 的版本是否记录在顶部？

虽然我们建议在您的`.ruby-version`中包含这一点，但我们也鼓励您在`Gemfile`中指定这一点。

### Ruby on Rails 是否锁定了特定版本？

除非你正在开发一个全新的应用程序，否则你的`Gemfile`应该指定一个锁定版本的 rails。您最不想做的事情就是在部署过程中不经测试就意外升级了 Ruby on Rails 应用程序。

### 你已经指定了所有 gem 的版本了吗？

最近，我们继承了一个 Ruby on Rails 应用程序，它已经有将近六个月没有被开发了。它的自动化测试覆盖率也很差，而且`Gemfile`在大多数必需的 gem 上没有特定的版本。在我们的第一次更改中，我对应用程序的一部分做了一个小的复制更改，在开发中看起来还可以。当我部署到 staging 时，我们很快注意到他们应用程序的一个不相关但主要的处理文件上传的功能不工作了。它没有被有限的测试覆盖所捕获。

考虑到在将近半年的时间里没有发生重大变化，花了几个小时才找到它是如何断裂的。

我们发现当部署发生时，许多 Rubygems 已经被自动升级。因此，我们必须比较在我们的生产和登台服务器上安装了什么 Rubygems 来确定版本差异。我们一个接一个地检查了它们，在我们的`Gemfile`中详细说明了生产中已知的工作版本。通过这个，我们确定一个 gem ( `s3_direct_upload`)是罪魁祸首，因为他们在某个时候改变了一些配置语法。

这只是一个例子，说明这种类型的版本变化有一天会如何伤害你未来的自己(和/或另一个团队)。

作为一般规则，我们尝试用下面的语法将我们的 Rubygems 锁定到微发布。例如:`gem "zencoder", "~> 2.5"`

我们的开发团队同意我们想要手动更改主要版本，并允许 [Bundler](http://bundler.io/) 自动处理微版本更新。

### 你的宝石是分组摆放的吗？

在每个环境中安装所有的 gem 没有任何问题。但是，如果您将一些 gem 安装在试运行和/或生产环境中，它们可能只会 1)占用更多的磁盘空间，2)降低部署速度。例如，您可能不需要在您的生产服务器上安装`rpsec-rails`、`cucumber`、`timecop`、`database-cleaner`或`capybara-webkit`。

[了解更多关于`Gemfile`团体的](https://bundler.io/groups.html)。

### 这些宝石都是用来做什么的？

当你扫描你的 gem 依赖项时，你还记得它们的用途吗？他们中有谁看起来不熟悉吗？

例如，看着客户的`Gemfile`，我发现自己在问，*“发射宝石到底是什么？缺德的宝石是用来做什么的？”*这里没有文件表明它们的用途。我的解决方案是必须谷歌它们和/或搜索代码库来尝试和确定它。

作为最佳实践，我们的团队已经同意，如果您发现自己必须查找应用程序中提供了什么不常见的宝石，请在`Gemfile`中添加一个简短的注释以供将来参考。善待未来的自己和/或同龄人。

### 最后一点，Rubygems 是通过 SSL 安装的吗？

确保您的`Gemfile`已经:

`source 'https://rubygems.org'` ( `https://` vs `http://`)

## 3。bundler-audit 提出了哪些警告？

我推荐安装 [bundler-audit](https://github.com/rubysec/bundler-audit) ，它是一个 Ruby gem，可以快速列出你的 Gemfile.lock 中指定的 gem 版本中哪些已知的安全漏洞已经被修补。它还会为您提供升级到的建议版本。

运行`bundler-audit`会给你这样的反馈:

```
$ bundle-audit

{code}
bundle-audit
Name: actionpack
Version: 4.2.6
Advisory: CVE-2015-7576
Criticality: Unknown
URL: https://groups.google.com/forum/#!topic/rubyonrails-security/ANv0HDHEC3k
Title: Timing attack vulnerability in basic authentication in Action Controller.
Solution: upgrade to ~> 5.0.0.beta1.1, ~> 4.2.5.1, ~> 4.1.14.1, ~> 3.2.22.1

Name: actionpack
Version: 4.2.6
Advisory: CVE-2015-7581
Criticality: Unknown
URL: https://groups.google.com/forum/#!topic/rubyonrails-security/dthJ5wL69JE
Title: Object leak vulnerability for wildcard controller routes in Action Pack
Solution: upgrade to ~> 4.2.5.1, ~> 4.1.14.1

Name: actionpack
Version: 4.2.6
Advisory: CVE-2016-0751
Criticality: Unknown
URL: https://groups.google.com/forum/#!topic/rubyonrails-security/9oLY_FCzvoc
Title: Possible Object Leak and Denial of Service attack in Action Pack
Solution: upgrade to ~> 5.0.0.beta1.1, ~> 4.2.5.1, ~> 4.1.14.1, ~> 3.2.22.1

Name: actionview
Version: 4.2.6
Advisory: CVE-2016-0752
Criticality: Unknown
URL: https://groups.google.com/forum/#!topic/rubyonrails-security/335P1DcLG00
Title: Possible Information Leak Vulnerability in Action View
Solution: upgrade to ~> 5.0.0.beta1.1, ~> 4.2.5.1, ~> 4.1.14.1, ~> 3.2.22.1

Name: activemodel
Version: 4.2.6
Advisory: CVE-2016-0753
Criticality: Unknown
URL: https://groups.google.com/forum/#!topic/rubyonrails-security/6jQVC1geukQ
Title: Possible Input Validation Circumvention in Active Model
Solution: upgrade to ~> 5.0.0.beta1.1, ~> 4.2.5.1, ~> 4.1.14.1

Name: activerecord
Version: 4.2.6
Advisory: CVE-2015-7577
Criticality: Unknown
URL: https://groups.google.com/forum/#!topic/rubyonrails-security/cawsWcQ6c8g
Title: Nested attributes rejection proc bypass in Active Record
Solution: upgrade to ~> 5.0.0.beta1.1, ~> 4.2.5.1, ~> 4.1.14.1, ~> 3.2.22.1

Name: devise
Version: 3.4.1
Advisory: CVE-2015-8314
Criticality: Unknown
URL: http://blog.plataformatec.com.br/2016/01/improve-remember-me-cookie-expiration-in-devise/
Title: Devise Gem for Ruby Unauthorized Access Using Remember Me Cookie
Solution: upgrade to >= 3.5.4

Name: rack-attack
Version: 4.2.0
Advisory: 132234
Criticality: Unknown
URL: https://github.com/kickstarter/rack-attack/releases/tag/v4.3.1
Title: rack-attack Gem for Ruby missing normalization before request path
processing
Solution: upgrade to >= 4.3.1

..etc 
```

Enter fullscreen mode Exit fullscreen mode

## 4。自动化测试有多久没被接触过了？

在我们尝试运行测试之前——我们想知道自从开发人员在测试和/或规范目录中进行更改以来已经有多长时间了。让我们看看通过 git 的最后一次提交。

```
$ git log --pretty -1 spec/`   # and/or `test/`, too 
```

Enter fullscreen mode Exit fullscreen mode

这个日期是很久以前吗(与`app/`中完成的工作相比)？

这表明自动化测试还没有被优先考虑。有趣的是，当我们看到测试被忽略了很长一段时间，开发人员似乎落入了一个或多个下降的类别。

*   最初的开发人员没有多少测试；他们没有时间添加更多的内容。
*   他们升级了 Ruby 和/或 Rails，测试套件开始失败；他们没有时间去修理它们。
*   一个项目的独立开发人员，没有任何同事可以一起工作，当他们自己在浏览器中测试时，对自己的代码“工作”很有信心。
*   利益相关者没有太多的预算，所以开发人员一直试图保持快速和肮脏的更新——并将编写测试视为“额外”成本(我们认为他们仍然必须手动测试，无论如何)。

我一直对不时跳过编写自动化测试的步骤感到内疚。我明白了。但是，我们不能让这成为一种长期的坏习惯。当你独自做项目时，跳过这些要容易得多，因为你不用对队友负责。

不管怎样，Robby，省下一堆手动浏览器测试，写自动化测试吧。

## 5。是否有不应该在存储库中的安全密钥？

在 config/目录中查找。你看到 YAML 的文件了吗？仔细查看，您是否找到了试运行和/或生产凭据？(访问密钥、用户名、密码等。)

例如，`config/environments/production.rb` …你看到里面有多少密码/钥匙？

他们不应该。

这些仅属于服务器环境和密码管理器保险库中。我们对 Argon 星球的团队使用 1 密码，以便我们可以控制谁可以访问这些密码。

你如何解决这个问题？

*   将这些文件直接添加到您的服务器中。例如，您可以将这些 YAML 文件上传到应用程序的共享/目录中。如果是一个试运行环境，不要在其中包含生产环境的详细信息。(反之亦然)
*   注意:如果您使用的是 Heroku 这样的环境，您可以将它们移动到 [config 环境变量](https://devcenter.heroku.com/articles/config-vars)中。

从您的 git 存储库和历史中删除它们。[下面是如何](https://help.github.com/articles/removing-sensitive-data-from-a-repository/)。

另外，您现在应该更新所有这些凭证。我们建议每季度更新一次您的密钥和密码。

## 6。我们能植入一个本地数据库吗？

在本地运行应用程序之前，您可能需要将一些种子/测试数据加载到本地数据库中。Ruby on Rails 为您提供了一些关于如何通过一个`db/seeds.rb`文件加载的约定，您可以运行一个 rake 任务来加载该文件。[了解关于 Rails 中种子数据的更多信息](http://guides.rubyonrails.org/active_record_migrations.html#migrations-and-seed-data)。

`db/seeds.rb`里有什么东西存在吗？如果不是，你靠的是什么数据？您是否必须手动加载数据，或者您是否依赖生产数据库备份？

如果是前者，我们鼓励您花时间构建可以加载、删除和重新加载的种子数据。

如果是后者，你就是在玩火。您真的需要加载所有东西来处理代码库吗？很多时候，我们会看到缺乏测试覆盖率和没有种子数据的结合…而且开发人员一直在本地环境中进行生产。我们建议你只在极少数情况下这样做，不要让它成为常态。

## 7。有数据库索引吗？

当我们查看一个现有的数据库结构时，我们希望了解是否已经注意到了索引。例如，我们可以用下面的例子来比较外键的数量和索引的数量。

大约有多少个外键？

```
$ grep -v 'add_index' db/schema.rb | grep '_id' | wc -l
      99 
```

Enter fullscreen mode Exit fullscreen mode

有多少个索引？

```
$ grep 'add_index' db/schema.rb | wc -l
      95 
```

Enter fullscreen mode Exit fullscreen mode

当然，外键并不是唯一需要索引的东西…但是如果这两个数字相差很大，这就暗示着它们在过去被忽略了。

通常，一旦我们缓解了这种情况，我们就会看到相当不错的性能提升。请放心继续不使用索引，因为当我们向新客户展示我们让他们的应用程序运行得多快时，这让我们看起来很好。；-)

## 8。待办事项:还有什么要做的？

我想留给您一个更快捷的方法来了解应用程序中可能存在的技术缺陷。运行下面的命令，看看是否有有趣的东西返回:

```
$ grep -ir 'todo' app/* 
```

Enter fullscreen mode Exit fullscreen mode

这些注释通常是临时的提醒，以便在以后整理一些代码和/或做一些进一步的研究来找出一些东西是什么。

他们经常被遗忘。

这些是应该努力的事情吗？移除？

## 关闭思绪

不管您的代码库是只有 2k LOC 还是 100k LOC，这些都是您应该在 Ruby on Rails 应用程序中注意的事情。它们不会立即决定您的应用程序的成败，但是我们的团队已经发现遵循这些最佳实践具有长期价值。

它们也是我们在执行 Ruby on Rails 代码审计时提出的一些最常见的低挂水果建议。当然，我们还会深入应用程序中的一些底层细节，但这些只是一些示例，可以让您和您未来的队友受益。

你不同意我的任何建议吗？你有什么要补充的吗？在下面留下评论，让我知道。

[![](../Images/573a999745e03be77ffaf3174f84051a.png)T2】](http://planetargon.com/services/ruby-on-rails-code-audit?utm_campaign=PPC%20Code%20Audit&utm_source=dev.to&utm_medium=code%20audit%20post)