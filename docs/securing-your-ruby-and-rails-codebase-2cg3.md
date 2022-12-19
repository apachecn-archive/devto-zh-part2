# 保护您的 Ruby 和 Rails 代码库

> 原文：<https://dev.to/pxlpnk/securing-your-ruby-and-rails-codebase-2cg3>

编写软件时，您希望避免引入功能错误或安全问题。

对于功能性 bug，我们通常会写单元测试，尽量覆盖边缘情况。然后，集成测试会确保所有的东西都能协同工作。

在本文中，我们将介绍如何保护 Ruby、Rails 或基于 Rack 的应用程序。

无论它是一个新的代码库，还是我们将它添加到一个 10 年前的项目中，最终我们都会有一个像样的安全测试。

对于本文，我们将使用开源软件工具来保护我们的代码库。我们还将考虑一些托管的和已配置的替代方案。

**我们的工具箱**:

*   联邦审计
*   司闸员
*   rubocop(有一些安全规则)

这些工具可以分为静态应用安全测试(SAST)和软件组合分析(SCA)工具。

静态分析扫描您的代码库，并寻找某些类型的模式。它不会执行您的代码，也不能检测生成的代码中或运行时的问题。

结果通常会显示检测到问题的代码行。他们还会提醒您已识别的问题类型(带有严重性)，有时还会提供如何解决问题的建议。

相比之下，软件组合分析将检查您使用的依赖关系是否存在漏洞。

报告通常包括:

*   受影响的依赖项的包名。
*   严重程度取决于它有多糟糕。这通常从`none`持续到`critical`。
*   要升级到的下一个安全版本。
*   更多解释问题所在的参考资料和更多细节。
*   在某些情况下，是指向漏洞数据库的标识符。

要运行安全工具，您不需要 CI 管道，但通常建议您准备一个。

我们将在 ruby/rails 项目中安装这些工具。您还将学习如何启用 rake 任务并运行它们。

所有工具都有正常的可执行文件，可以一个一个地使用，或者在 shell 脚本中使用。

## Bundler 审计

[Bundler Audit](https://github.com/rubysec/bundler-audit) 将检测您代码库中易受攻击的依赖项

首先，将 bundler-audit gem 添加到您的 gem 文件:

```
gem 'bundler-audit' 
```

并安装您的软件包。

现在您可以运行一个

```
bundle audit check --update 
```

这将首先更新本地数据库，然后检查项目中是否存在易受攻击的依赖项。

请记住，`bundle audit`不会自动更新依赖关系。我建议您在每次跑步前更新它们，以确保获得最佳和最新的结果。

要启用 rake 任务，请将以下内容添加到项目 Rakefile 中:

```
require 'bundler/audit/task'
Bundler::Audit::Task.new 
```

A `rake bundle:audit`现在将更新数据并执行检查。

如果您在这一点上发现了任何漏洞，请不要惊慌。您可以通过将版本更新到下一个安全的版本来修复它们。

如果你不能更新，你可以用`--ignore $vulnerability-id`暂时抑制它。

例如:

```
bundle audit check --update --ignore OSVDB-108664 
```

输出如下所示:

```
$ bundle audit check --update

Updating ruby-advisory-db ...
From https://github.com/rubysec/ruby-advisory-db
 * branch            master     -> FETCH_HEAD
Already up to date.
Updated ruby-advisory-db
ruby-advisory-db: 322 advisories
Name: omniauth-oauth2
Version: 1.0.2
Advisory: CVE-2012-6134
Criticality: High
URL: http://www.osvdb.org/show/osvdb/90264
Title: Ruby on Rails omniauth-oauth2 Gem CSRF vulnerability
Solution: upgrade to >= 1.1.1

Name: rubyzip
Version: 1.2.1
Advisory: CVE-2018-1000544
Criticality: Unknown
URL: https://github.com/rubyzip/rubyzip/issues/369
Title: Directory Traversal in rubyzip
Solution: upgrade to >= 1.2.2

Vulnerabilities found! 
```

## 司闸员

接下来，我们将添加 [Brakeman](https://github.com/presidentbeef/brakeman) 作为我们的第一个静态代码分析工具。
Brakeman 会在你的代码库中寻找已知的不安全模式和配置。它还会检查您是否使用了已知的易受攻击的 rails 版本。

Brakeman 不仅适用于 Rails，还适用于 Sinatra 和任何类型的机架应用。

首先，将依赖项添加到您的 Gemfile 中:

```
gem brakeman 
```

并安装它:

```
bundle install 
```

然后，您可以在您的 rails 根目录中第一次运行它。

```
brakeman 
```

这将调用 Brakeman 并输出一些冗长的报告。下面是一个 gist 附示例报告:[https://gist . github . com/pxl pnk/4a 829 b 6101 Abe 5 fc 0102d 358 d8dd 028d](https://gist.github.com/pxlpnk/4a829b6101abe5fc0102d358d8dd028d)

如果你的应用程序是安全的，你没有发现运行它，首先干得好！

但是之后你想看看它是什么样子的，那么你可以用 [OWASP/RailsGoat](https://github.com/OWASP/railsgoat) 试试。

如果你第一次在任何旧的 Rails 应用程序上运行 Brakeman，你可能会有很多发现。

为了使事情变得更简单，您希望从更严重的问题开始，然后逐步解决不太重要的问题。

为此，使用`brakeman -w3`，它将只显示高，`-w2`将显示高和中，`-w1`将显示所有其他结果。

一层一层地处理通常效果最好，效率也最高。

如果你打算使用 rake，你可以很容易地安装 RakeTask。

运转

`brakeman --rake`

将在`lib/tasks/brakeman.rake`中创建一个任务文件，该文件可以通过以下方式运行:

```
rake brakeman:run 
```

要自定义它并使用更多高级选项，请参见文档 [brakeman as a library/](https://brakemanscanner.org/docs/brakeman_as_a_library/) 。

## 机器人足球赛

虽然 rubocop 被称为 linter 和 formatter，但它有一些安全规则，并且可以通过一些社区扩展来扩展。

让我们先开始安装 RuboCop。将以下内容添加到您的 gem 文件中:

```
gem 'rubocop', require: false 
```

然后运行我们的老朋友

```
bundle install 
```

完成后，您可以执行`rubocop`命令。

如果你以前没有使用过 rubocop，你可能会在你的终端上看到很多输出和警告。

对于这篇文章，我们对林挺和格式规则不太感兴趣。相反，我们确保只运行与安全相关的规则。

我们将使用下面的`.rubocop.yml`配置开始。

```
AllCops:
  DisabledByDefault: true

Security/Eval:
  Enabled: true
Security/JSONLoad:
  Enabled: true
Security/MarshalLoad:
  Enabled: true
Security/Open:
  Enabled: true
Security/YAMLLoad:
  Enabled: true
Bundler/InsecureProtocolSource:
  Enabled: true
Rails/OutputSafety:
  Enabled: true 
```

我们正在禁用所有的样式和林挺规则，只启用与安全相关的规则。在这篇博文中，这只是为了简单起见。

现在运行`rubocop`命令应该没那么吵了。

这是它在 RailsGoat 存储库中的样子:

```
 $ bundlex exec rubocop
Inspecting 121 files
............C....................
Offenses:

app/controllers/password_resets_controller.rb:6:20: C: Security/MarshalLoad: Avoid using Marshal.load.
    user = Marshal.load(Base64.decode64(params[:user])) unless params[:user].nil?
                   ^^^^

121 files inspected, 1 offense detected 
```

还不太令人印象深刻，但我们已经得到了一些相关的安全信息。

现在我们将添加更多的安全规则。

将以下内容添加到您的 gem 文件中:`gem 'rubocop-gitlab-security'`并运行`bundle install again`。

现在将以下内容添加到配置文件`rubycop.yml`:

```
GitlabSecurity/DeepMunge:
  Enabled: true
GitlabSecurity/JsonSerialization:
  Enabled: true
GitlabSecurity/RedirectToParamsUpdate:
  Enabled: true
GitlabSecurity/SendFileParams:
  Enabled: true
GitlabSecurity/SqlInjection:
  Enabled: true
GitlabSecurity/SystemCommandInjection:
  Enabled: true 
```

这些由 GitLab 团队制定的规则为我们的安全设置增加了巨大的价值。

现在，在运行 RuboCop 时，我们需要新的模块:

或者通过运行:

```
 bundle exec rubocop --require rubocop-gitlab-security 
```

或者将此添加到`rubocop.yml`:

```
require: rubocop-gitlab-security 
```

我们现在可以在 RailsGoat 存储库中发现更多的安全问题。下面是一个 gist 的示例报告:[https://gist . github . com/pxl pnk/958 c 500 aab 22 f 90 b 54918 d6d 9573251d](https://gist.github.com/pxlpnk/958c500aab22f90b54918d6d9573251d)

要配置 RakeTasks，请添加:

```
require 'rubocop/rake_task'
RuboCop::RakeTask.new do |task|
  task.requires << 'rubocop-gitlab-security'
end 
```

到您的 RakeFile 并运行它:`rake rubocop`

## 修正调查结果

为了不被发现淹没，我建议您一次配置并启用一个扫描器。

从严重性高的发现开始，过滤掉其余的。

由于各种原因，一些结果会是假阳性或者根本没有危险。他们通常可以被忽略，并添加到一个列表中，以避免噪音。

一旦你修复或静音的结果，它的行为如你所愿，移动到下一个。

当您对您的设置感到满意时，启用这些工具来使您的 CI 构建失败，并在您提交的每次代码更改时运行它们。

现在，当某些东西不符合您的安全标准时，您将永远知道。

## 托管服务和其他替代方案

除了可用的开源工具之外，还有一组商业工具。

[GuardRails](https://www.guardrails.io) 运行本文中的所有工具。当服务检测到某些东西时，它会在你的 GitHub pull 请求上发表评论。它是一个零配置的安全工具，可以为您完成大量繁重的工作，并过滤掉所有的误报。

你也有像 Snyk 这样的公司，他们只关注你的依赖，当他们检测到新的漏洞时会提醒你。 [GitHub](https://github.com) 和 [GitLab](https://gitlab.com/) 也提供这个作为他们产品的一部分。

## 开源安全工具非常棒

ruby 社区为创建这些工具付出了巨大的努力。他们是免费给大家用的，应该支持！

如果你负担得起，给他们寄些钱或其他支持，这样他们就可以在将来继续改进这些工具。

你如何保护你的代码库？在[andy@occamslabs.com](mailto:andy@occamslabs.com)让我知道。