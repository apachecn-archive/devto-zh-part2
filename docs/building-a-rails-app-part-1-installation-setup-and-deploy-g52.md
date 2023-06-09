# 构建 Rails 应用程序。第 1 部分:安装、设置和部署

> 原文：<https://dev.to/damcosset/building-a-rails-app-part-1-installation-setup-and-deploy-g52>

# 简介

在本系列文章中，我们将一步一步地创建一个功能完整的 web 应用程序来学习 Ruby on Rails。我总是发现在学习新东西的时候让一些东西变得“真实”更有帮助。本系列的目标就是要做到这一点:帮助我(以及您，如果您最终阅读了它)更快更好地学习 Rails。现在我们知道了我为什么要尝试这个，问题来了:我们创造了什么？

我选择了做网店。这个应用程序将销售运动鞋。过去，我几乎在不同的堆栈中创建过类似的东西，但最终错过了机会。这次只是为了学习如何使用 Rails。让我们直接开始吧！

# 安装

在我们开始写代码之前，我们需要确保所有的工具都在我们的掌握之中。首先，我们需要三样东西:*Ruby*(Rails 框架背后的语言)， *Rails* (显然...)和 *git* (部署我们的应用程序并跟踪我们的更改)。

如果您知道您已经拥有这些工具，您可以跳过这一步，转到**设置**部分。

## 安装 Ruby

### 窗口

windows 用户可以访问下面的[链接](https://rubyinstaller.org/)。点击下载按钮，选择第一个链接**下的 DEVKIT** 。下载，按照步骤操作，打开一个终端窗口检查你的 ruby 版本:

```
>ruby -v
ruby 2.5.3p105 (2018-10-18 revision 65156) [x64-mingw32] 
```

### MacOS

在 MacOS 上，根据你的电脑，你可能已经安装了 Ruby。用`ruby -v`登记你的终端。如果不是这样，在你的终端上安装[自制软件](https://brew.sh/)并运行`brew install ruby`。之后，运行`ruby -v`，你应该会看到类似这样的东西:

```
>ruby -v
ruby 2.5.1p57 (2018-03-29 revision 63029) [x86_64-darwin17] 
```

## 安装导轨

现在我们有了 Ruby，我们需要得到 Rails 框架。在 Ruby 中，我们称一个包为 *gem* 。要安装一个 gem，您可以使用命令*gem install[包名]* 。gem 命令由包管理器 *RubyGems* 提供。因此，在我们的例子中，打开一个终端窗口并运行: *gem install rails* 。就像以前使用 Ruby 一样，您可以通过检查 Rails 的版本来检查它是否已经安装:

```
rails -v
Rails 5.2.1 
```

*注意:对于本系列的其余部分，我将使用这个 Rails 版本*

## 安装 Git

最后，我们需要安装 *git* 。Git 是一个流行的版本控制系统(VCS)。VCS 使与他人协作、共享和备份代码变得容易。

### Windows 上的

对于 Windows 用户，你可以从这个[页面](https://git-scm.com/downloads)下载 Git。安装并遵循默认的步骤，一切应该是好的。之后，在您的终端中运行命令`git`，您应该会看到类似这样的内容:

```
 >git
usage: git [--version] [--help] [-C <path>] [-c <name>=<value>]
           [--exec-path[=<path>]] [--html-path] [--man-path] [--info-path]
           [-p | --paginate | -P | --no-pager] [--no-replace-objects] [--bare]
           [--git-dir=<path>] [--work-tree=<path>] [--namespace=<name>]
           <command> [<args>]

These are common Git commands used in various situations:

start a working area (see also: git help tutorial)
   clone      Clone a repository into a new directory
   init       Create an empty Git repository or reinitialize an existing one

work on the current change (see also: git help everyday)
   add        Add file contents to the index
   mv         Move or rename a file, a directory, or a symlink
   reset      Reset current HEAD to the specified state
   rm         Remove files from the working tree and from the index

examine the history and state (see also: git help revisions)
   bisect     Use binary search to find the commit that introduced a bug
   grep       Print lines matching a pattern
   log        Show commit logs
   show       Show various types of objects
   status     Show the working tree status

grow, mark and tweak your common history
   branch     List, create, or delete branches
   checkout   Switch branches or restore working tree files
   commit     Record changes to the repository
   diff       Show changes between commits, commit and working tree, etc
   merge      Join two or more development histories together
   rebase     Reapply commits on top of another base tip
   tag        Create, list, delete or verify a tag object signed with GPG

collaborate (see also: git help workflows)
   fetch      Download objects and refs from another repository
   pull       Fetch from and integrate with another repository or a local branch
   push       Update remote refs along with associated objects

'git help -a' and 'git help -g' list available subcommands and some
concept guides. See 'git help <command>' or 'git help <concept>'
to read about a specific subcommand or concept. 
```

### MacOS 上的

在 MacOS 上，我们可以使用*自制软件*来安装 Git。我们已经安装了它，因为我们需要它来安装 Ruby。因此，我们可以运行`brew install git`来安装 VCS。当您在终端命令中运行`git`时，您应该会看到上面相同的文本。

# 设置

现在我们已经有了所有需要的工具，我们可以开始设置我们的新应用程序了。在这一节中，我们将创建一个新的 bare Rails 应用程序，并快速探索我们必须处理的内容。为了创建一个新的 Rails 应用程序，我们使用了`rails new myAppName`命令。因此，移动到您想要创建应用程序的目录并运行`rails new sneakers_store`。在创建了一大堆新文件之后，您应该会看到类似于
的内容

```
 Bundle complete! 16 Gemfile dependencies, 77 gems now installed.
Use `bundle info [gemname]` to see where a bundled gem is installed. 
```

现在，您可以使用`cd sneakers_store`进入 Rails 应用程序目录，并在您最喜欢的编辑器中打开它来一探究竟！

## 探索应用

如你所见，那里有很多文件和文件夹。我们现在不会详细讨论每一个，因为没有必要。我们会在需要的时候解决它们。但是你已经有了一个功能性的应用程序(那还没做多少；) )让我看看我们有的一些文件和文件夹:

### Gemfile

您将在这个应用程序中找到一个名为 *Gemfile* 的文件。如果你来自 Javascript，这就像一个 package.json，它列出了这个应用程序的包需求。如你所见，我们的小应用程序已经需要相当多的*宝石*。你会注意到在文件 *~ >* 中有一个奇怪的操作数。它告诉捆绑者(负责加载所需的 gem)允许他加载哪些 gem 版本。例如，我有:

`gem 'rails', '~> 5.2.1'`

这意味着我的 app 被允许使用高于 5.2.1 但低于 5.3.0 的 Rails 版本。基本上，它只会得到这个宝石的小补丁。如果想要一个确切的版本，可以去掉 *~ >* 。

### app/目录

*app* 目录包含核心应用程序代码:模型、视图、控制器和助手。其中有一个 *assets/* 文件夹，包含应用程序的资产，如 CSS、图像或 Javascript。

### 配置/目录

*config* 目录包含应用程序配置。例如，这是您可以找到我们路由器的地方。

### 测试/目录

毫无疑问，这是我们的应用程序测试将被编写的地方。

## 捆绑者

Bundler 是管理你的应用程序的工具。之前，我解释过你的 gem 文件中的 *~ >* 语法是关于我们的应用程序中允许什么样的 gem 版本。嗯， *Bundler* 负责管理这些依赖关系，以确保我们的开发环境稳定。Bundler 将安装我们需要的 gem 版本，以确保我们的应用程序能够顺利运行。

当你运行 *rails new* 时，命令`bundle install`会自动运行。每次对 gem 文件进行更改时(添加新的 gem，更改 gem 的版本)，确保运行`bundle install`以将更改考虑在内。

为了便于讨论，我们姑且说我希望我的 rails 版本是**确切地说是** 5.2.1。我不想要任何小补丁更新。因此，我将替换我的 gem 文件中的以下行:

`gem 'rails', '~> 5.2.1'`

作者:

`gem 'rails', '5.2.1'`

你去掉了 *~ >* ，这表示你想要宝石的精确版本。您可能遇到的另一种语法如下:`gem 'rails', '>= 5.2.1'`。

*> =* 意思是:取这个 gem 的最新版本，只要这个版本大于等于提供的版本号。在我们的例子中，我们要求 bundle 获取最新的 rails gem 版本，只要 gem 版本大于或等于 5.2.1。

现在，运行`bundle install`。*捆绑包*会在必要时安装新的 gem 和/或更新 gem。有可能要求你先跑`bundle update`。如果是这种情况，运行`bundle update`，然后运行`bundle install`。

因此，每当我们对 Gemfile 进行更改时，一定要运行`bundle install`。

### rails 服务器

因为我们运行了`rails new`，我们已经有了一个可以运行的应用程序。我们用`rails server`运行一个 Rails 应用程序。类似这样的东西应该会出现在你的终端:

```
 > rails server
=> Booting Puma
=> Rails 5.2.1 application starting in development
=> Run `rails server -h` for more startup options
Puma starting in single mode...
* Version 3.12.0 (ruby 2.5.1-p57), codename: Llamas in Pajamas
* Min threads: 5, max threads: 5
* Environment: development
* Listening on tcp://0.0.0.0:3000
Use Ctrl-C to stop 
```

太好了！您的应用程序现在运行在端口 3000 上。如果您在浏览器中访问 [http://localhost:3000](http://localhost:3000) ，您将看到:

[![](img/ce8d1842669e4cba0dc6a3f3fbdaf847.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jqVsV0At--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0z6feea2y9x12ak2w5ke.png)

太棒了，一切都准备好了，我们可以开始编码了。但在此之前，我们将通过更多的高级理论来更好地理解 Rails。

## 一个 MVC 的故事

Rails 框架遵循模型-视图-控制器架构模式。*模型*负责应用程序的数据及其逻辑。模型代表了应用程序所处的世界。例如，它定义用户信息是什么，或者更新数据库。*视图*就是你在浏览器上看到的。视图使用模型并显示界面。最后，*控制器*充当视图和模型之间的中介。它处理用户的动作，改变模型和视图的数据。

当您加载 localhost:3000 时，会发送一个请求。该请求被发送到控制器。根据编写的代码，控制器可能会立即呈现一个视图，或者与模型进行交互。在与模型(可以从数据库中获取示例数据)进行交互之后，控制器会呈现一个包含所收集数据的视图。

这需要一个非常简单的示意图:

[![](img/7dc492c2c08371d49b34e76b065dd69c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9pQXh096--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y86puz7rxorjgph3a7gj.png)

如你所见，控制器位于所有东西的中间。如果您查看 tour app/ folder 内部，您会发现三种类型的模块(控制器/、模型/、视图/)之间的区别。让我们创建我们的第一个控制器。

## 我们的第一个控制器

为了遵循我们人民的古老传统，我们必须创建一个说“Hello World！”在某个时候。我们就这么做吧。在我们的控制器文件夹中，让我们创建一个名为*first controller _ controller . Rb*的新文件。让我们把下面的代码放进去:

```
class WelcomeController < ApplicationController
    protect_from_forgery with: :exception

    def greeting
        render html: "Hello World!"
    end
end 
```

**注意:**控制器文件名以 *_controllers.rb* 结尾，类以*控制器*结尾。

现在不要太担心语法。我们创建了一个名为 WelcomeController 的类，它继承自 ApplicationController。在控制器内部，您可以定义动作。动作是控制器上的公共方法，web 服务器可以自动访问它。在这里，我刚刚创建了一个名为*问候*的动作。因为 Ruby 读起来几乎像英语，所以您可以猜到这个动作的作用:它呈现一些 HTML:“Hello World！”。但是这还不足以将我们的 HTML 字符串呈现给视图。为此，我们需要修改路由器。

### 修改路由器

路由器可以在 *config/routes.rb* 中找到。打开它，用下面的内容替换文件的内容:

```
Rails.application.routes.draw do
    # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
    root "welcome#greeting"
end 
```

我们希望确保根路由使用我们的新控制器。如果您遵循上面的链接，您可以看到配置根路由的文档。语法如下:

`root controller_name#action_name`

我们的控制器名为*欢迎*，我们的动作名为*问候*。保存文件并刷新浏览器:

[![](img/6643b4b2eb0c466b73f0399252524793.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zgEX2jfW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vx8l2gn8jy7rfyj7nhky.png)

太好了！这是一个好的开始。现在，让我们部署我们的应用程序！

# 部署

我们需要做的第一件事是创建我们的 git 仓库。导航到运动鞋 _ 商店目录的根目录并运行`git init`。您应该会看到类似于
的内容

```
 ➜  sneakers_store git:(master) ✗ git init
Reinitialized existing Git repository in /Users/Damien/Desktop/ruby/sneakers_store/.git/ 
```

现在，运行`git add .`，然后运行`git commit -m 'first commit'`。第一个命令将把所有当前文件添加到您的存储库中，并把它们放在一个*暂存区*。这几乎是挂起的更改，第二个命令表明我们确定要保留这些更改，所以我们用一条消息提交它们。

我们将使用 Heroku 部署我们的应用程序。这一步是可选的，但我发现尽早和经常部署更容易。

## Heroku 设置

Heroku 使用 PostgresQL 数据库。所以，这意味着我们现在不能使用默认的 sqlite3。这意味着我们需要改变我们的 gem 文件。下面是它应该看起来的样子:

```
 source 'https://rubygems.org'
git_source(:github) { |repo| "https://github.com/#{repo}.git" }

ruby '2.5.1'

# Bundle edge Rails instead: gem 'rails', github: 'rails/rails'
gem 'rails', '~> 5.2.1'
# Use Puma as the app server
gem 'puma', '~> 3.11'
# Use SCSS for stylesheets
gem 'sass-rails', '~> 5.0'
# Use Uglifier as compressor for JavaScript assets
gem 'uglifier', '>= 1.3.0'
# See https://github.com/rails/execjs#readme for more supported runtimes
# gem 'mini_racer', platforms: :ruby

# Use CoffeeScript for .coffee assets and views
gem 'coffee-rails', '~> 4.2'
# Turbolinks makes navigating your web application faster. Read more: https://github.com/turbolinks/turbolinks
gem 'turbolinks', '~> 5'
# Build JSON APIs with ease. Read more: https://github.com/rails/jbuilder
gem 'jbuilder', '~> 2.5'
# Use Redis adapter to run Action Cable in production
# gem 'redis', '~> 4.0'
# Use ActiveModel has_secure_password
# gem 'bcrypt', '~> 3.1.7'

# Use ActiveStorage variant
# gem 'mini_magick', '~> 4.8'

# Use Capistrano for deployment
# gem 'capistrano-rails', group: :development

# Reduces boot times through caching; required in config/boot.rb
gem 'bootsnap', '>= 1.1.0', require: false

group :development, :test do
    gem 'sqlite3'
  # Call 'byebug' anywhere in the code to stop execution and get a debugger console
  gem 'byebug', platforms: [:mri, :mingw, :x64_mingw]
end

group :development do
  # Access an interactive console on exception pages or by calling 'console' anywhere in the code.
  gem 'web-console', '>= 3.3.0'
  gem 'listen', '>= 3.0.5', '< 3.2'
  # Spring speeds up development by keeping your application running in the background. Read more: https://github.com/rails/spring
  gem 'spring'
  gem 'spring-watcher-listen', '~> 2.0.0'
end

group :test do
  # Adds support for Capybara system testing and selenium driver
  gem 'capybara', '>= 2.15'
  gem 'selenium-webdriver'
  # Easy installation and use of chromedriver to run system tests with Chrome
  gem 'chromedriver-helper'
end

group :production do
 gem 'pg'
end

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby] 
```

注意，我们从第一个列表中删除了 sqlite3 gem，将其包含在*组:development，:test* 中。宝石 *pg* (用于 PostgresQL)包含在*:制作组*中。Rails 允许您指定不同环境所需的 gem。第一个列表是每个环境中总是加载的 gem。

现在，因为我们更改了 Gemfile，所以需要再次运行 Bundle。但是，这一次，我们将添加一个标志来防止在我们的本地环境中安装生产 gems:

`bundle install --without production`

这样不会安装 *pg* 宝石。

让我们重新运行`git add .`。然后运行`git commit -m 'Updated Gemfile for production'`。

## 英雄库 CLI

现在，你可以[为 Heroku 注册](https://signup.heroku.com/login)。接下来，下载 [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) 。完成后，通过在终端中运行`heroku --version`来检查是否安装了 Heroku。

```
 ➜  sneakers_store git:(master) ✗ heroku --version
heroku/7.0.52 darwin-x64 node-v10.1.0 
```

接下来，运行`heroku login`并输入您的凭证。然后，运行`heroku keys:add`。

现在，我们可以通过运行`heroku create`在 Heroku 上为我们的应用程序创建一个位置。

```
 ➜  sneakers_store git:(master) ✗ heroku create
Creating app... done, ⬢ aqueous-peak-82306
https://aqueous-peak-82306.herokuapp.com/ | https://git.heroku.com/aqueous-peak-82306.git 
```

这是您的应用程序的子域。当然，它会有一个不同于我的名字。

## 部署在 Heroku 上

要在 Heroku 上部署，运行`git push heroku master`。你会看到一堆东西出现在你的终端里。完成后，你可以进入你的 Heroku 账户仪表板，看到你的应用程序的子域名。点击它，然后点击右上角的*打开应用*。这是你将会看到的:

[![](img/3c74f1e90e6f7e673b8ae35e64919745.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t-qcgwgK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vxmdqniap6zhfwuo5yvp.png)

恭喜你！您刚刚在 Heroku 上部署了您的第一个 Rails 应用程序，并且学习了如何制作您的第一个控制器！