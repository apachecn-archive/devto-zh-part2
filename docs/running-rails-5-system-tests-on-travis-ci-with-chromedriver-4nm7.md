# 使用 Chromedriver 在 Travis CI 上运行 Rails 5 系统测试

> 原文：<https://dev.to/aergonaut/running-rails-5-system-tests-on-travis-ci-with-chromedriver-4nm7>

Rails 5.1 引入了使用水豚和 Selenium 进行[系统测试](https://weblog.rubyonrails.org/2017/4/27/Rails-5-1-final/#system-tests)的概念。系统测试采用了许多 Rails 应用程序已经在做的“特性”测试实践，并将其纳入了 Rails 测试策略的正式部分。由于系统测试利用浏览器，它们可以执行 Javascript，这是 Rails 集成测试无法做到的。

系统测试在本地机器上开箱即可运行，但是让它们在远程服务器上运行就有点困难了。对于需要做什么，没有很多好的文档，很多解决方案必须通过堆栈溢出和 GitHub 注释线程来挖掘。

在这篇文章中，我将展示如何使用 headless Chrome 和 Chromedriver 设置系统测试以在 Travis CI 上运行。

## 铁轨 5.1.6

尽管 5.1 宣布了系统测试，但是您确实应该确保您使用的是 Rails 5.1.6 以获得最佳体验。这是因为在最初的发布之后，对框架进行了一些其他的小的生活质量改进，这是非常好的。

其中包括对 ActiveRecord 的更改，允许 Capybara 线程和测试服务器线程使用相同的数据库连接(参见 [rails/rails#28083](https://github.com/rails/rails/pull/28083) )。这在使用事务时很重要，因为否则两个线程将无法看到另一个线程所做的 DB 更改。

在过去，您必须通过关闭事务并添加数据库清理器或一些类似的 gem 来清理测试运行期间创建的数据来解决这个问题。但这意味着要么 Rails 需要引入 Database Cleaner 作为依赖项，要么任何想要使用系统测试的人都需要手动向他们的 Gemfile 添加另一个 gem。这两个都不利于未来系统测试的采用，所以 Rails 团队更新了 ActiveRecord 以允许共享连接。

## RSpec 3.7

我更喜欢使用 RSpec 作为我的测试框架。RSpec 3.7 在框架中增加了对系统测试的支持，取代了之前的特性规范。

如果您正在使用 RSpec，您将需要确保您正在使用版本 3.7 以便使用系统测试。

## 安装 Chromedriver

Chromedriver 允许 Chrome 由 Selenium 控制，这是我们在系统测试中使用 Chrome 作为浏览器所必需的。

可以从项目网站下载 Chromedriver，也可以用家酿木桶安装:

```
$ brew cask install chromedriver 
```

Enter fullscreen mode Exit fullscreen mode

### 移除 chromedriver-helper

如果你过去使用过 Chromedriver，你可能已经安装了 Ruby gem[chrome driver-helper](https://rubygems.org/gems/chromedriver-helper)。如果你想直接使用 Chromedriver，你需要从你的系统中删除 chromedriver-helper 及其可执行文件。

如果你使用的是 rbenv，你可以使用我用过的命令:

```
$ gem uninstall chromedriver-helper
$ ls ~/.rbenv/versions/*/bin/* | grep chromedriver | xargs rm
$ rbenv rehash 
```

Enter fullscreen mode Exit fullscreen mode

> ***注意**:移除 chromedriver-helper 对你来说可能是必要的，也可能不是，这取决于你的设置。我发现它给我带来了问题，所以我决定删除它。*

## 选择司机

系统测试使用`driven_by`方法抽象水豚的驱动程序。对于常规的系统测试，您将在您的`ApplicationSystemTestCase`中包含`driven_by`。然而，RSpec 不使用`ApplicationSystemTestCase`，所以您需要在`before`块中配置驱动程序。

最简单的方法是给`rails_helper.rb` :
添加一个全局`before(:each)`

```
RSpec.configure do |config|
  config.before(:each, type: :system) do
    driven_by :selenium, using: :chrome
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这将设置所有系统测试由使用 Chrome 的 Selenium 驱动。

`type: :system`表示此`before`块仅适用于标记为系统测试的测试。如果一个测试位于`spec/system`，RSpec 将推断它是一个系统测试，或者你可以手工标记规格。

现在运行`bundle exec rspec`应该会打开一个 Chrome 窗口并运行您的系统规格。酷！

## 不明不白

在本地机器上打开浏览器窗口很好，但是在远程服务器上就不行了，因为没有监视器来呈现窗口。相反，我们必须在无头模式下使用 Chrome。

为了使用无头浏览器，我们可以传递一些额外的参数给`driven_by` :

```
 RSpec.configure do |config|
   config.before(:each, type: :system) do
-    driven_by :selenium, using: :chrome +    driven_by :selenium, using: :chrome, options: { args: ["headless"] }
   end
 end 
```

Enter fullscreen mode Exit fullscreen mode

还有其他使用 headless 模式的方法，这种方法的缺点是它会导致 Selenium 抛出一个不赞成使用的警告。出于我们将在后面看到的原因，我们必须使用这种传递参数的方法。

现在运行`bundle exec rspec`应该**而不是**打开浏览器窗口。整洁！

## 运行在特拉维斯身上

对特拉维斯进行测试是最棘手的部分。对于我的构建，我想使用 Travis 的 Docker 基础设施，在我的`.travis.yml`文件中有`sudo: false`。这意味着我必须绕过几个限制。

### 安装 Chrome 和 Chromedriver

要在 Travis 上安装包，需要使用配置文件中的`addons`部分。虽然 Travis 有一个专门为 Chrome 设计的[插件，但我在使用这个插件时遇到了一些问题。相反，我使用了`apt`插件来安装 Chrome 和 Chromedriver:](https://docs.travis-ci.com/user/chrome) 

```
addons:
  apt:
    - google-chrome-stable
    - chromium-chromedriver 
```

Enter fullscreen mode Exit fullscreen mode

Selenium 期望在路径上找到 Chromedriver，但是 Travis 上的默认安装没有把二进制文件放在路径上。为了解决这个问题，我在`before_script`步骤中创建了一个符号链接:

```
before_script:
  - ln -s /usr/lib/chromium-browser/chromedriver ~/bin/chromedriver 
```

Enter fullscreen mode Exit fullscreen mode

现在 Travis 环境设置正确。

### Chrome 启动时崩溃

即使环境设置正确，我的测试仍然失败，因为 Chrome 会在启动时立即崩溃。经过一番挖掘，我找到了[这个故障排除页面](https://developers.google.com/web/tools/puppeteer/troubleshooting#running_puppeteer_on_travis_ci)。

有两个要点:

1.  我们需要用`--no-sandbox` 运行[Chrome，这样它才能在 Travis 上正常工作。](https://developers.google.com/web/tools/puppeteer/troubleshooting#running_puppeteer_on_travis_ci)
2.  我们需要[禁用`/dev/shm`](https://developers.google.com/web/tools/puppeteer/troubleshooting#tips) 的使用，因为 Docker 在设备中提供的空间太少，如果没有足够的空间，Chrome 就会崩溃。

为了做到这一点，我们需要能够向 Selenium 传递自定义参数，这就是我们使用现有的`driven_by`选项的原因。

```
 RSpec.configure do |config|
   config.before(:each, type: :system) do
-    driven_by :selenium, using: :chrome +    driven_by :selenium, using: :chrome, options: { args: ["headless", "disable-gpu", "no-sandbox", "disable-dev-shm-usage"] }
   end
 end 
```

Enter fullscreen mode Exit fullscreen mode

从[这页](https://developers.google.com/web/updates/2017/04/headless-chrome#cli)的推荐中可以看到`disable-gpu`的论点。

现在，您的系统测试应该可以在 Travis 上成功运行了！厉害！

## 额外学分:用元数据过滤系统测试

现在我们已经设置好了，无论何时我们运行完整的测试套件，RSpec 都会运行所有的系统测试。但是系统测试很慢，我们可能不想一直运行它们。幸运的是，RSpec 有办法根据规范的元数据从运行中过滤掉某些规范。

让我们用`browser`标签标记`spec/system`中的所有规格。为此，我们可以使用`define_derived_metadata` :

```
RSpec.configure do |config|
  config.define_derived_metadata(file_path: /spec\/system/) do |metadata|
    metadata[:browser] = true
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以用`filter_run_excluding`默认过滤`browser`规格:

```
RSpec.configure do |config|
  config.filter_run_excluding browser: true
end 
```

Enter fullscreen mode Exit fullscreen mode

现在运行`bundle exec rspec`时，系统测试会被过滤掉。为了运行系统测试，我们可以使用`-t`选项来**包含** `browser`规格。

```
$ bundle exec rspec -t browser 
```

Enter fullscreen mode Exit fullscreen mode

记得将您的`.travis.yml`改为使用`-t`来运行系统测试。将系统测试从测试套件的其余部分中分离出来，允许我并行运行缓慢的系统测试和套件的其余部分。

## 结论

系统测试是开始特性测试的一个非常简单的方法。Rails 附带的默认配置应该适用于大多数人，扩展它以使用不同的浏览器或传递不同的选项很简单。更难的是试图找出如何在 Travis 上使用系统测试，没有太多的文档或例子。

在这篇文章中，我收集了我在 Travis CI 上运行系统测试时学到的所有东西。希望有用！