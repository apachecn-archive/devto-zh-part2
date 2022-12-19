# 如何确定 Capybara 中的 window browser 的大小？让我们看看。

> 原文：<https://dev.to/yutna/-browser-window--capybara---imh>

你好！今天，我们来讨论一下在 [Capybara](http://teamcapybara.github.io/capybara) 中设置 window browser 的大小的设置问题。

通常情况下，使用 Capybara 编写系统测试时，朋友必须创建一个名为`capybara.rb`的文件，然后将其放在【12300】文件夹中。

```
require 'capybara/rails'
require 'capybara/rspec' 
```

Enter fullscreen mode Exit fullscreen mode

当然，在实际应用中，这是不够的，因为 Capybara 使用驱动程序调用 browser 来运行系统测试(在我的理解中)，这是由 default 提供的。

你有什么问题吗？

Capybara 支持以下几个驱动程序:

*   机架测试
*   硒
*   水豚-网络工具包
*   闹鬼

由 RackTest 本身是最受限制的驱动程序，其中之一就是它无法运行 JavaScript！因此，RackTest 不是一个很好的选择，一个很好的选择是，我们应该把我们要在 browser 上运行的 Web 应用程序，而不是在其中的选项。

对于 Capybara-webkit 和 Poltergeist，我不要在本文中继续讨论，因为我还没有尝试过，所以有可能喝醉。

好的，这次在我们开始部署硒之前，我们需要先在我们的机器上安装这些工具。

*   壁虎
*   chromedriver(铬驱动程序)

因为我主要使用 Mac 作为工作原理，所以我会告诉你如何只在 Mac 上安装这两个，对 Linux 和 windows b 来说。

安装`geckodriver`和`chromedriver`打开终端，然后按此打印命令。(读者朋友必须完成[homebe 的安装。](https://brew.sh)

```
$ brew install geckodriver
$ brew cask install chromedriver 
```

Enter fullscreen mode Exit fullscreen mode

完成安装后，这次我们来修改一下 Capybara 上的驱动程序的**【故障】**值，dodo 先生。

```
require 'capybara/rails'
require 'capybara/rspec'

Capybara.default_driver = :selenium 
```

Enter fullscreen mode Exit fullscreen mode

之后，朋友们试着运行系统测试，他们会发现 Firefox 浏览器被打开来运行我们的 tests，比如，点击这个，我们就会看到它。

当您需要编写一个包含调试在内的 t0㎡时，使用此`:selenium`非常合适，但它有一个缺点:它很慢，不适合用于运行。

因此，一旦你写完了 Tess，重申它已经完成，你希望它在 CI/CD 上运行，你需要改变**defaull 的值。**

你的`:selenium_chrome_headless`与众不同的是，它运行速度更快，运行时不显示 window 浏览器。

我在那工作的时候发现，如果我们不确定 window browser 的大小，Capybara 会使用他已经定义的 default 值，并且在

结果发现它在中等大小的 window size 上出现了一个 render。mobile 也不是一个很大的 desktop，也不是很特别。所以我不得不去想办法。

我发现我在 google 搜索的答案在办公室里找不到，写得很不详细，所以我得去 copy 的 gem 目录里找找

硒驱动程序的各种失败率都写在档案的末尾。`lib/capybara.rb`读者朋友们可以进去看看，其中的 capye

```
Capybara.register_driver :selenium_chrome_headless do |app|
  browser_options = ::Selenium::WebDriver::Chrome::Options.new()
  browser_options.args << '--headless'
  browser_options.args << '--disable-gpu'

  Capybara::Selenium::Driver.new(app, browser: :chrome, options: browser_options)
end 
```

Enter fullscreen mode Exit fullscreen mode

这一次，我们要确定 window browser 的大小，我们只需将此行添加到
中即可。

```
browser_options.args << '--window-size=<width>,<height>' 
```

Enter fullscreen mode Exit fullscreen mode

我们就可以通过将`<width>`和`<height>``<height>`替换为我们要确定的数字来获得所需的 window 浏览器的大小。

对任何想在 CI/CD 上运行的人，例如 Jenkins 等，请将此行添加到“
中。

```
browser_options.args << '--no-sandbox' 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们的配置会有这样的样子，先生。
。

```
require 'capybara/rails'
require 'capybara/rspec'

Capybara.register_driver :selenium_chrome_headless do |app|
  browser_options = ::Selenium::WebDriver::Chrome::Options.new

  browser_options.args << '--headless'
  browser_options.args << '--disable-gpu'
  browser_options.args << '--window-size=1440,900'
  browser_options.args << '--no-sandbox'

  Capybara::Selenium::Driver.new(app, browser: :chrome, options: browser_options)
end

Capybara.default_driver = :selenium_chrome_headless 
```

Enter fullscreen mode Exit fullscreen mode

你好吗？aram tram 来了，伸展一下。其实很简单，什么都没有。朋友，打开一篇文章，向下卷动，再来回移动，再来回移动，然后是 555+。