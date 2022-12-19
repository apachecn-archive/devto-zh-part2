# [EN]域驱动的 Rails 架构

> 原文：<https://dev.to/bpohoriletz/en-domain-driven-rails-architecture-2pla>

*   时间:30-40 分钟
*   级别:中级/高级
*   代码: [GitHub](https://github.com/bpohoriletz/bpohoriletz.github.io/tree/master/samples/domain_driven_rails_architecture_pattern)
*   参考资料:
    *   [模块化整体架构:Rails 架构——Dan Manges](https://medium.com/@dan_manges/the-modular-monolith-rails-architecture-fb1023826fc4)
    *   [违反直觉的铁路——伊万·涅米琴科](https://www.youtube.com/watch?v=KtD32fO_owU&index=4&list=PLqwEgoaqsYziFoM8UN2GmWc0BySnWUozK)
    *   [轨道零件–汤姆·罗斯](http://tomrothe.de/posts/rails_parts.html)
    *   使用生产力和教育测试来扩展团队——朱利安·纳多

下一篇文章将描述一个 Rails 应用程序的架构，它结合了参考文章中的思想以及一些监控代码质量的附加工具。该应用程序的主要要求是:

1.  视图(表示)和业务逻辑(您的领域)的分离
2.  依赖性分离(gems ),因此能够独立运行单元测试
3.  解决方案必须简单明了(Rails 很棒，我们不打算与之对抗)

TLDR - [Github repo](https://github.com/bpohoriletz/bpohoriletz.github.io/tree/master/samples/domain_driven_rails_architecture_pattern) 和一个 [commit](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/51220e7f4274e35cf6c071a0cf3ba683dd2af938) ，所有更改都应用到一个新的 Rails 应用程序

# **表象与领域的分离**

首先要做的是在文件夹结构中(以及在你的头脑中)清晰地分离表示和业务逻辑。为了实现这一点，我们将引入一个名为`representations`的新文件夹，并在其中放置我们需要的所有东西来表示域实体。在示例中，这些是:

*   `representations/`
    *   `assets/`
    *   `controllers/`
    *   `decorators/`
    *   `public/`
    *   `views/`
    *   `vendor/`
    *   `routes.rb`

> 我个人更喜欢使用 decorators 而不是 helpers，所以没有`helpers/`文件夹。

接下来，我们将为您的域实体和逻辑设置一个文件夹结构——这两者都不应该是表示层的一部分。为此，让我们创建一个新文件夹`domain/`，并将我们的模型和数据库配置移动到那里:

*   `domain/`
    *   `contexts/`
    *   `database.yml`

这里的名字`contexts/`是对 DDD 理论中的 [BoundedContext](https://martinfowler.com/bliki/BoundedContext.html) 模式的引用，你可以用不同的名字命名它，并且在它里面有任何文件夹结构。

现在我们需要让 Rails 知道文件夹结构的变化。这是在`config/application.rb`中使用 [Rails::Application](https://api.rubyonrails.org/classes/Rails/Application/Configuration.html#method-i-paths)
完成的

```
 # config/application.rb

 28 paths['app/assets'] = 'representations/assets'
 29 paths['app/views'] = 'representations/views'
 30 paths['config/routes.rb'] = 'representations/routes.rb'
 31 paths['config/database'] = 'domain/database.yml'
 32 paths['public'] = 'representations/public'
 33 paths['public/javascripts'] = 'representations/public/javascripts'
 34 paths['public/stylesheets'] = 'representations/public/stylesheets'
 35 paths['vendor'] = 'representations/vendor'
 36 paths['vendor/assets'] = 'representations/vendor/assets'
 37 # impacts where Rails will look for an ApplicationController and ApplicationRecord
 38 paths['app/controllers'] = 'representations/controllers'
 39 paths['app/models'] = 'domain/contexts'
 40
 41 %W[
 42 #{ File.expand_path( '../representations/concerns', __dir__ ) }
 43 #{ File.expand_path( '../representations/controllers', __dir__ ) }
 44 #{ File.expand_path( '../domain/concerns', __dir__ ) }
 45 #{ File.expand_path( '../domain/contexts', __dir__ ) }
 46 ].each do |path|
 47 config.autoload_paths << path
 48 config.eager_load_paths << path
 49 end 
```

Enter fullscreen mode Exit fullscreen mode

在这个改变之后，Rails 将使用新的布局工作，就好像原来的布局从来没有改变过一样——自动加载、快速加载、测试、资产编译——所有的功能都完全正常。

> 我个人认为将`ApplicationController`和`ApplicationRecord`作为`Concerns`提高了代码的灵活性，所以在提供的例子中，它们是关注点，并且有一个额外的`config/initializers/draper.rb`文件来使 Draper 工作

```
 # config/initializers/draper.rb

 3 DraperBaseController = Class.new( ActionController::Base )
 4 DraperBaseController.include( ApplicationController )
 5
 6 Draper.configure do |config|
 7 config.default_controller = DraperBaseController
 8 end 
```

Enter fullscreen mode Exit fullscreen mode

# **分离环境并构建独立的测试套件**

因为我们已经分离了表示和领域，为每一个都有单独的测试套件将是有益的——正确实现的套件将会更快、隔离和独立。让我们先为他们准备好环境:

1.  为表示和域引入单独的`Gemfile`和`Gemfile.lock`
2.  Make main `Gemfile`使用我们在上一步中添加的
3.  为`representations/`和`domain/`设置独立的测试环境

添加更多的`Gemfiles`很简单——只需创建新文件并从主`Gemfile`中提取依赖项。

让 main `Gemfile`意识到额外的依赖关系也很简单- `bundler`已经有了一个加载额外文件的方法，`bundler`会抱怨是否有任何问题，就好像`Gemfile`从未被拆分过一样。

```
 # Gemfile

 54 %w[representations/Gemfile domain/Gemfile].each do |custom_gemfile|
 55 eval_gemfile custom_gemfile
 56 end 
```

Enter fullscreen mode Exit fullscreen mode

建立特定于上下文的测试套件是最难的部分(随着应用程序的增长，它很可能会引入更多的问题)。作为第一步，我们在`representations/`和`domain/`中运行`rspec --init`，结果将创建新的`representations/spec`和`doman/spec`文件夹。

`spec/spec_helper.rb`文件也会自动添加，但`spec/rails_helper.rb`不会，我们必须手动添加和配置。

#### 域测试套件配置

首先，我们将把`spec/rails_helper.rb`复制到`domain/spec/rails_helper.rb`，并删除`RSpec.configure do |config|`之前的所有内容。这样做是为了不加载任何依赖项(我们将在以后加载我们需要的所有东西)。我们现在还不能进行测试，但这只是第一步

接下来，我们要确保只装载我们真正需要的东西:

*   装载`active_record`和`rspec-rails`

```
 # domain/spec/rails_helper.rb

  3 require 'active_record/railtie'
  4 require 'active_support'
  5 require 'rspec/rails' 
```

Enter fullscreen mode Exit fullscreen mode

*   负载测试套件依赖项

```
 7 ENV['RAILS_ENV'] ||= 'test'
  8 require 'spec_helper'
  9 require 'database_cleaner'
 10 require 'factory_bot'
 11 require 'pry-byebug' 
```

Enter fullscreen mode Exit fullscreen mode

*   创建一个应用程序让`rspec-rails`工作(不幸的是最脆弱的部分)

```
 13 ContextsTestApplication = Class.new( ::Rails::Application )
 14 ::Rails.application = ContextsTestApplication.new 
```

Enter fullscreen mode Exit fullscreen mode

*   连接到数据库

```
 16 database_configurations = YAML.load(
 17 ERB.new(
 18 File.read( File.expand_path( '../database.yml', __dir__ ) )
 19 ).result
 20 )
 21
 22 ActiveRecord::Base.establish_connection( database_configurations['test'] )
 23 
```

Enter fullscreen mode Exit fullscreen mode

*   加载域(由于没有自动加载机制，所以首先是共享问题)

```
 24 %w[concerns contexts].each do |folder|
 25 Dir[File.expand_path( "../#{folder}/**/*.rb", __dir__ )].each { |f| require f }
 26 end 
```

Enter fullscreen mode Exit fullscreen mode

*   加载初始化程序/支持文件

```
 28 Dir['./spec/support/*.rb'].each { |f| require f }
 29
 30 RSpec.configure do |config| 
```

Enter fullscreen mode Exit fullscreen mode

#### 表示测试套件配置

为表示设置测试套件非常相似，唯一的区别是我们加载的内容:

*   装载`action_controller`和`rspec-rails`

```
 # representations/spec/rails_helper.rb
  3 require 'action_controller/railtie'
  4 require 'active_support'
  5 require 'rspec/rails'
  6 require 'spec_helper' 
```

Enter fullscreen mode Exit fullscreen mode

*   为`rspec-rails`创建一个应用程序并加载路线

```
 8 RepresentationsTestApplication = Class.new( ::Rails::Application )
  9 ::Rails.application = RepresentationsTestApplication.new
 10 require_relative '../routes' 
```

Enter fullscreen mode Exit fullscreen mode

*   加载依赖项

```
 12 require 'pry-byebug'
 13 require 'uuid' 
```

Enter fullscreen mode Exit fullscreen mode

*   加载表示代码(首先共享关注点，因为没有自动加载机制)

```
 15 %w[concerns controllers decorators].each do |folder|
 16 Dir[File.expand_path( "../#{folder}/**/*.rb", __dir__ )].each { |f| require f }
 17 end 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有两个独立的测试套件:

*   可能只包括单元测试
*   强迫你呆在你的环境里
*   快速加载/重新加载环境(如果从主应用程序运行，加载文件需要 2.65 秒，如果独立运行，只需 0.96642 秒)

> 因为测试套件文件在`representations/`和`domain/`文件夹中，所以它们不能在`app/`文件夹中——Rails 会尝试在生产中加载这些文件夹中的所有文件

# **最终零件**

正如我在[上一篇](https://dev.to/bpohoriletz/modular-monolith-example-5dnd)文章中提到的，我认为顶层`spec/`和`test/`文件夹中的测试不应该是单元测试，而应该总是测试应用程序的多个组件。相反的情况适用于`repreentations/spec`和`domain/spec`中的测试——那些应该总是单元测试。

这种设置的一个问题是，为了能够在特定于上下文的环境中执行测试，您必须拥有单独的`Gemfile.lock`文件，这可能会导致在您单独运行测试和作为一个整体运行测试时使用不同的 gem 版本。让我们引入一个测试来确保在这种情况发生时我们会收到通知。

```
 # spec/sanity/gemfile_spec.rb

  5 RSpec.describe 'Gemfile' do
  6 context 'Domain Gemfile' do
  7 it 'have gems locked at the same version as a global Gemfile' do
  8 global_environment = Bundler::Dsl.evaluate( 'Gemfile', 'Gemfile.lock', {} )
  9 .resolve
 10 .to_hash
 11 local_environment = Bundler::Dsl.evaluate( 'domain/Gemfile', 'domain/Gemfile.lock', {} )
 12 .resolve
 13 .to_hash
 14
 15 diff = local_environment.reject do |gem, specifications|
 16 global_environment[gem].map( &:version ).uniq == specifications.map( &:version ).uniq
 17 end
 18
 19 expect( diff.keys ).to eq( [] )
 20 end
 21 end 
```

Enter fullscreen mode Exit fullscreen mode

示例[应用](https://github.com/bpohoriletz/bpohoriletz.github.io/tree/master/samples/domain_driven_rails_architecture_pattern)还包含 Git 挂钩，如果运行`./bin/setup`，这些挂钩将被安装到您的应用中，并在提交前后自动执行。

在 hook 对提交的修改运行 Rubocop 之前，hook 允许你对你的代码运行 rails_best_practices、reek、brakeman 和 mutant

# **总结**

我很喜欢这种架构提供的灵活性——如果需要，你可以隔离你代码的任何部分，把它作为一个独立的单元。同时，它使用 Rails API，所以并不反对它——相反，这是组织代码的另一种方式。我渴望用更复杂的遗留应用程序来尝试——在这两种情况下，引入新的架构都非常简单。

链接:

*   [应用](https://github.com/bpohoriletz/bpohoriletz.github.io/tree/master/samples/domain_driven_rails_architecture_pattern)
*   [提交](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/51220e7f4274e35cf6c071a0cf3ba683dd2af938)