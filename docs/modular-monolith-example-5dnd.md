# 模块化整体示例

> 原文：<https://dev.to/bpohoriletz/modular-monolith-example-5dnd>

*   时间:20-30 分钟
*   级别:中级/高级
*   代码: [GitHub](https://github.com/bpohoriletz/bpohoriletz.github.io/tree/master/samples/modular_monolith_example)
*   参考资料:[模块化整体架构:Rails 架构–DanManges](https://medium.com/@dan_manges/the-modular-monolith-rails-architecture-fb1023826fc4)

参考文章是我去年读过的最好的文章之一。虽然我不同意那里陈述的所有内容，但描述的想法非常棒(这是我发现在 medium 中你可以给出的圈数是有限制的)。我已经尝试在我的宠物项目中应用它们，这个项目是用经典的 Rails 结构构建的——虽然提取不容易，但结果绝对值得。在这里，我从项目中提取了一个 gem 和一个引擎到一个全新的 Rails 应用程序中，这很容易，而且这个提取改进了引擎的设计。我将在下面介绍设置的重要部分，但是对于 TLDR 人来说，这里有一个 [Github Repo](https://github.com/bpohoriletz/bpohoriletz.github.io/tree/master/samples/modular_monolith_example) ，查看`seed.rb`文件以获得使用的创建者。

# 宝石概述

gem 允许您从 google 日历中获取事件数据

#### **分离依赖**

本地 gem 中的代码有自己的依赖项，但不依赖于主应用程序，这些依赖项从父应用程序的 gem 文件移动到 gem 的*中。gemspec

```
 # gems/google_calendar/google_calendar.gemspec

 32 spec.add_dependency 'activemodel'
 33 spec.add_dependency 'google-api-client', '~> 0.11'
 34 spec.add_dependency 'ice_cube'
 35 # Development
 36 spec.add_development_dependency 'pry-byebug'
 37 spec.add_development_dependency 'simplecov'
 38 spec.add_development_dependency 'rake'
 39 spec.add_development_dependency 'rspec'
 40 spec.add_development_dependency 'factory_bot' 
```

Enter fullscreen mode Exit fullscreen mode

并载入初始化器

```
 # gems/google_calendar/lib/google_calendar.rb
  1 require 'google/apis/calendar_v3'
  2 require 'google/api_client/client_secrets'
  3
  4 require 'google_calendar/version'
  5 require 'google_calendar/connection'
  6 require 'google_calendar/event' 
```

Enter fullscreen mode Exit fullscreen mode

#### **分离测试**

gem 的所有单元测试都被转移到 gem 的文件夹中，它们可以独立执行——导航到 gems 文件夹并运行`bundle exec rspec spec/`。为了运行测试，你需要在助手文件
中进行手动设置

```
 # gems/google_calendar/spec/spec_helper.rb
  1 require 'simplecov'
  2 SimpleCov.start
  3
  4 require 'google_calendar'
  5 require 'factory_bot'
  6 require 'factories/event_factories' 
```

Enter fullscreen mode Exit fullscreen mode

# 发动机概述

引擎使用 authlogic gem 提供身份验证

#### **分离依赖**

与 gems 中的模式相同，所有依赖项都存在于引擎中*。不要污染父应用程序的 gem 文件。

```
 # domains/customers/customers.gemspec
 19 s.add_dependency 'authlogic'
 20 s.add_dependency 'best_in_place', '~> 3.0.1'
 21 s.add_dependency 'draper'
 22 s.add_dependency 'google_calendar'
 23 s.add_dependency 'haml'
 24 s.add_dependency 'rails'
 25
 26 s.add_development_dependency 'rspec-rails'
 27 s.add_development_dependency 'factory_bot'
 28 s.add_development_dependency 'shoulda-matchers'
 29 s.add_development_dependency 'pry-byebug'
 30 s.add_development_dependency 'sqlite3' 
```

Enter fullscreen mode Exit fullscreen mode

并且被载入初始化器

```
 # domains/customers/lib/customers.rb
  1 require 'active_model/railtie'
  2 require 'active_record/railtie'
  3 require 'customers/engine'
  4 require 'haml'
  5 require 'best_in_place'
  6 require 'authlogic' 
```

Enter fullscreen mode Exit fullscreen mode

引擎也依赖于一个本地的`google_calendar` gem，它被直接加载到 Gemfile
中

```
 # domains/customers/Gemfile
  1 source 'https://rubygems.org'
  2
  3 gem 'google_calendar', path: '../../gems/google_calendar' 
```

Enter fullscreen mode Exit fullscreen mode

#### **分离测试**

引擎的所有单元测试都被转移到一个引擎的文件夹中，它们可以独立执行——导航到一个引擎的虚拟应用程序文件夹`domains/customers/spec/dummy/`并运行`bundle exec rspec spec/`为了让测试运行，你需要在助手文件
中手动设置环境

```
 # domains/customers/spec/dummy/spec/rails_helper.rb
  1 # Configure Rails Environment
  2 ENV['RAILS_ENV'] = 'test'
  3 require File.expand_path("../../config/environment.rb", __FILE__ )
  4 # TOFIX ActiveRecord::Migrator.migrations_paths = [File.expand_path("../../test/dummy/db/migrate", __FILE__ )]
  5 ActiveRecord::Migrator.migrations_paths << File.expand_path('../../db/migrate', __FILE__ )
  6
  7 require 'rspec/rails'
  8 # Add additional requires below this line. Rails is not loaded until this point!
  9 require 'spec_helper'
 10 require 'authlogic'
 11 require 'authlogic/test_case'
 12 require 'factory_bot'
 13 require 'shoulda-matchers'
 14 require 'pry'
 15
 16 FactoryBot.factories.clear
 17 FactoryBot.definition_file_paths = %W(spec/factories)
 18 FactoryBot.reload
 19 Dir[Rails.root.join('spec/support/**/*.rb')].each { |f| require f }
 20
 21 RSpec.configure do |config|
 22 config.include Authlogic::TestCase
 23 config.include FactoryBot::Syntax::Methods
 24 config.include Shoulda::Matchers::ActiveModel, type: :model
 25 config.include Shoulda::Matchers::ActiveRecord, type: :model
 26
 27 config.filter_rails_from_backtrace!
 28 end 
```

Enter fullscreen mode Exit fullscreen mode

#### **分离迁徙**

我认为迁移文件不应该复制到父应用程序，所需的配置在引擎初始化器
中指定

```
 # domains/customers/lib/customers/engine.rb
  1 module Customers
  2 class Engine < ::Rails::Engine
  3 isolate_namespace Customers
  4
  5 initializer :append_migrations do |app|
  6 # Migrations
  7 config.paths['db/migrate'].expanded.each do |expanded_path|
  8 app.config.paths['db/migrate'] << expanded_path
  9 end
          ...
 12 end
 13 end
 14 end 
```

Enter fullscreen mode Exit fullscreen mode

#### **分离译名**

引擎视图的翻译也存在于引擎中，配置在引擎初始化器中指定

```
 1 module Customers
  2 class Engine < ::Rails::Engine
  3 isolate_namespace Customers
  4
  5 initializer :append_migrations do |app|
          ...
 10 # Translations
 11 config.i18n.load_path += Dir["#{config.root}/config/locales/**/*.yml"]
 12 end
 13 end
 14 end 
```

Enter fullscreen mode Exit fullscreen mode

# 在主应用程序中启用认证

认证被提取为控制器的关注点，因此您需要将这个关注点添加到控制器中

```
 # app/controllers/application_controller.rb
  1 class ApplicationController < ActionController::Base
  2 include Customers::Authorization
  3 end 
```

Enter fullscreen mode Exit fullscreen mode

# 测试引擎/Gem 集成到主应用程序中

我相信位于 engines/gems 中的测试应该是单元测试——它们应该运行得很快，并消除任何外部依赖。在主要应用程序之外测试集成对我来说没有意义——系统测试是完成这项工作的好工具。一个基本的例子

```
# test/system/login_test.rb
  1 require 'application_system_test_case'
  2 require File.join(Rails.root.to_s, 'test', 'support', 'pages', 'accounts', 'login')
  3
  4 class UsersTest < ApplicationSystemTestCase
  5
  6 def test_login_is_functional
  7 load "#{Rails.root}/db/seeds.rb"
  8 ::Pages::Accounts::Login.new(test: self, url: customers.login_url ).instance_eval do
  9 visit
 10 # Validate content
 11 password_present?
 12 login_present?
 13 submit_present?
 14 # Log in
 15 login.set( Customers::Account.first.email )
 16 password.set( 'Test1234' )
 17 submit.click
 18 assert_text( 'Accounts' )
 19 end
 20 ensure
 21 Customers::Account.all.map(&:destroy!)
 22 end
 23 end 
```

Enter fullscreen mode Exit fullscreen mode

# 总结

提取一颗宝石很容易，提取一个引擎就有点麻烦了。模块化整体结构相对于传统应用的优势:

1.  代码分离——显著改进的应用程序设计
2.  依赖性分离——保持主应用程序更整洁
3.  测试分离——每个单元都有自己的套件，速度很快，可以独立运行

代码:

*   [储存库](https://github.com/bpohoriletz/bpohoriletz.github.io/tree/master/samples/modular_monolith_example)

# 引人深思

1.  如何处理共享布局？
2.  如何处理引擎之间共享的数据库表？
3.  来自 engines/gems 的 Gemfile.lock 应该存在于 Git 存储库中吗？