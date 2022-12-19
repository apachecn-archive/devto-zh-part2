# 在 Rails 5 应用程序上设置 BDD 堆栈

> 原文：<https://dev.to/markoa/setting-up-a-bdd-stack-on-a-rails-5-application-4dn6>

*这篇文章改编自 [Rails 测试手册](http://bit.ly/2K6gaDZ)，这是一本关于用 BDD 构建可持续 Rails 应用的免费电子书。*

在本教程中，我们将引导您完成生成新的 Rails 5 应用程序的过程，使用所有必要的工具来建立一个[行为驱动开发(BDD)流程](https://semaphoreci.com/ebooks/rails-testing-handbook)。

我们将建立 5 个工具来代表 Rails 中 BDD 开发的基础:

*   用于编写单元测试，
*   [黄瓜](https://cucumber.io)用于编写验收测试、
*   [Shoulda Matchers](https://github.com/thoughtbot/shoulda-matchers) 用于增强模型测试，
*   [工厂机器人](https://github.com/thoughtbot/factory_bot)用于基于工厂的数据库夹具，以及
*   [数据库清理器](https://github.com/DatabaseCleaner/database_cleaner)用于在测试运行之间建立一个干净的环境。
*   [轨道控制器测试](https://github.com/rails/rails-controller-testing)用于扩展我们的控制测试库

当你在你的机器上完成项目设置后，我们将在[信号量](https://semaphoreci.com)上设置它，并建立一个全自动的[持续集成工作流程](https://semaphoreci.com/blog/2017/03/02/what-is-proper-continuous-integration.html)。

## 系统先决条件

要遵循我们的指南，您需要在基于 Unix 的机器上安装以下软件。

*   [去](https://git-scm.com)，
*   [Ruby 2.4.0](https://www.ruby-lang.org/en) 或更新版本，
*   [Node.js](https://nodejs.org/en) ，以及
*   [PostgreSQL 9.5+](https://www.postgresql.org) 。

## 引导 Rails 应用程序

让我们从在机器上安装 Rails 开始。在您的终端中运行以下命令:

```
gem install rails 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们准备生成一个新的 Rails 应用程序。

```
rails new bdd-app -d postgresql 
```

Enter fullscreen mode Exit fullscreen mode

`-d postgresql`指定我们的应用程序将使用 PostgreSQL 而不是 SQLite 作为我们的数据库管理系统，SQLite 是 Rails 的默认设置。

现在让我们切换到您的应用程序的目录:

```
cd bdd-app 
```

Enter fullscreen mode Exit fullscreen mode

安装 Rails 应用程序的依赖项:

```
bundle install --path vendor/bundle 
```

Enter fullscreen mode Exit fullscreen mode

通过传递`--path`参数，我们告诉`bundler`在`bdd-app/vendor/bundle`目录中安装 gems。如果您忽略该参数，gems 将被全局安装，如果您在开发机器上处理多个 Ruby 应用程序，这不是一个好的做法。

最后，设置你的数据库:

```
bundle exec rails db:create
bundle exec rails db:migrate 
```

Enter fullscreen mode Exit fullscreen mode

## 安装 RSpec

首先，我们将设置 RSpec 来编写单元规格。

从在`Gemfile`文件中添加`rspec`开始，在开发和测试组下:

```
group :development, :test do
  ...
  gem 'rspec-rails', '~> 3.5'
end 
```

Enter fullscreen mode Exit fullscreen mode

运行`bundle install`在本地机器上安装 gem。

要完成安装，调用 rails 生成器在应用程序中设置`spec`目录。

```
bundle exec rails generate rspec:install 
```

Enter fullscreen mode Exit fullscreen mode

当命令完成时，您应该在项目中有一个新的`spec`目录。这个目录是你所有单位规格的基础。

## 安装黄瓜

接下来，我们将继续设置 Cucumber，它是用于编写验收测试的工具。

将`cucumber-rails`宝石添加到您的宝石档案的`:development, :test`组:

```
group :development, :test do
  ...
  gem 'cucumber-rails', require: false
end 
```

Enter fullscreen mode Exit fullscreen mode

运行`bundle install`在本地机器上安装 gem。

要完成安装，调用 rails 生成器在您的应用程序中设置`features`目录:

```
$ bundle exec rails generate cucumber:install

Running via Spring preloader in process 2192
      create  config/cucumber.yml
      create  script/cucumber
       chmod script/cucumber
      create  features/step_definitions
      create  features/step_definitions/.gitkeep
      create  features/support
      create  features/support/env.rb
       exist  lib/tasks
      create  lib/tasks/cucumber.rake
        gsub  config/database.yml
        gsub  config/database.yml
       force  config/database.yml 
```

Enter fullscreen mode Exit fullscreen mode

当命令完成时，您应该在项目中有一个新的`features`目录。这个目录是所有验收测试的基础。

## 安装匹配器

[Shoulda-matchers](https://github.com/thoughtbot/shoulda-matchers) gem 通过使用通用 Rails 功能的包装器，如验证、关联和重定向，加快了我们的测试时间。

为了安装`shoulda-matchers`，我们需要将`shoulda-matchers` gem 添加到我们在`Gemfile` :
中的开发和测试组

```
group :development, :test do
  ...
  gem 'shoulda-matchers', git: 'https://github.com/thoughtbot/shoulda-matchers.git', branch: 'rails-5'
end 
```

Enter fullscreen mode Exit fullscreen mode

运行
安装 gem

```
bundle install 
```

Enter fullscreen mode Exit fullscreen mode

我们需要通过指定我们想要使用的测试框架和库来配置这个 gem。打开`spec/rails_helper.rb`并在末尾粘贴以下块:

```
Shoulda::Matchers.configure do |config|
  config.integrate do |with|
    with.test_framework :rspec

    with.library :active_record
    with.library :active_model
    with.library :action_controller
    with.library :rails
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 安装工厂机器人

[工厂机器人](https://github.com/thoughtbot/factory_bot_rails)是一种宝石，用于制作“假”物作为测试数据。它本质上是一个具有清晰定义语法的 fixtures 替代品。它允许您在测试中创建假对象，而无需为给定对象的每个属性提供值。如果您没有为对象的属性提供任何值，`factory_bot`使用先前在工厂定义中定义的默认值。

将下面一行添加到您的`Gemfile`的开发和测试组中:

```
group :development, :test do
  ...
  gem 'factory_bot_rails'
end 
```

Enter fullscreen mode Exit fullscreen mode

用
安装宝石

```
bundle install 
```

Enter fullscreen mode Exit fullscreen mode

安装完成后，您可以将数据库模型的工厂放在`spec/factories`目录中。

### 安装数据库清理器

我们希望确保每次运行测试时应用程序的状态是一致的。最好的方法是在每次测试运行之间清除数据库中的所有数据，并为每次测试构造一个新的清除状态。

数据库清理器 gem
是一套在测试运行之间清理数据库的策略。

首先，将宝石添加到您的`Gemfile` :
中的`:test`组

```
# Gemfile
group :test do
  gem 'database_cleaner'
end 
```

Enter fullscreen mode Exit fullscreen mode

运行`bundle install`安装宝石。

安装 gem 后，我们将为 RSpec 测试设置一个清理策略。打开`spec/rails_helper.rb`文件，并添加以下代码片段:

```
RSpec.configure do |config|

  config.before(:suite) do
    DatabaseCleaner.strategy = :transaction
    DatabaseCleaner.clean_with(:truncation)
  end

  config.around(:each) do |example|
    DatabaseCleaner.cleaning do
      example.run
    end
  end

end 
```

Enter fullscreen mode Exit fullscreen mode

我们不必对黄瓜做任何修改。cucumber-rails 生成器已经创建了所有必要的钩子来将自己与数据库清理器 gem 集成在一起。

### 设置 Rails 控制器测试 gem

[rails 控制器测试](https://github.com/rails/rails-controller-testing) gem 扩展了我们控制器规格的功能。它允许我们测试变量和模板分配。

将其添加到您的`test`组下的`Gemfile`:

```
gem 'rails-controller-testing' 
```

Enter fullscreen mode Exit fullscreen mode

运行`bundle install`安装宝石。

RSpec 自动与此 gem 集成。将 gem 添加到您的 gem 文件中足以让助手在您的控制器规格中工作。

### 设置存储库

我们将使用 GitHub 为这个项目建立您的存储库。

首先，让我们确保我们的存储库已经准备好发布，并且我们不会将任何不必要的文件或目录推送到 GitHub。

将`vendor/bundle`目录添加到您的`.gitignore`文件中，以防止将 gems 推送到 Github。

```
echo 'vendor/bundle' >> .gitignore 
```

Enter fullscreen mode Exit fullscreen mode

然后，让我们创建第一个提交。

```
git add .
git commit -m "Bootstrapping Rails BDD application" 
```

Enter fullscreen mode Exit fullscreen mode

访问 https://github.com/new，为你的项目创建一个新的资源库。GitHub 会告诉你如何连接你的本地库和远程库。

[![New GitHub repository](../Images/f7902984cdc0b192bf074d984374b7ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1KZFihpX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/semaphore-imagecloud/dunja-radulov/004-new-repository.png)

最后，添加新的遥控器，并将您的代码推送到 GitHub。

```
git remote add origin git@github.com:<YOUR-USERNAME>/<YOUR-REPO>.git
git push -u origin master 
```

Enter fullscreen mode Exit fullscreen mode

### 设置持续集成

作为本教程的最后一步，我们将确保我们的项目从一个干净的绿色建筑开始。这将是我们应用程序的第一个安全返回点，并将作为进一步开发的坚实基础。

第一步是[创建一个免费的信号量帐户](https://semaphoreci.com/users/sign_up)。

从列表中选择您的 GitHub 存储库:

[![Choose GitHub Repository](../Images/bcd7007bf2aa0b3a492d3109b220bb17.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NDWHZ8nj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/semaphore-imagecloud/dunja-radulov/004-choose-github-repository.png)

遵循安装指南。Semaphore 将识别出您有一个包含 Postgresql 的 Rails 项目，并将为成功的首次构建生成所有命令。

让我们调整命令列表，并添加我们的 RSpec 和 Cucumber 测试套件。

[![Semaphore Commands](../Images/b64fa5528cc500e819e2068bd67b74fa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--K1TQ_8nI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/semaphore-imagecloud/dunja-radulov/004-semaphore-commands.png)

点击“使用这些设置构建”开始您的项目的第一个构建。

[![First Green Build](../Images/34dd61f37325814263fe18db27c9fbd2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---gEZ_e99--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/semaphore-imagecloud/dunja-radulov/004-first-green-build.png)

## 结论

恭喜你！您已经用 Rails 5、RSpec 和 Cucumber 为行为驱动开发建立了一个很好的基础。您现在已经完全准备好在 BDD 周期中工作，并交付干净的、可工作的代码。

要获得更多实际操作的例子，请下载免费的 [Rails 测试手册](http://bit.ly/2K6gaDZ)，深入了解如何通过测试构建可持续发展的 Rails 应用。

感谢阅读，请在评论中告诉我你的想法。:)