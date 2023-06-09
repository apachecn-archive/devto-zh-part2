# 构建我的第一个基于 Sinatra 的 Ruby 应用程序:思考和总结

> 原文：<https://dev.to/morinoko/building-my-sinatra-based-ruby-app-thoughts-and-wrap-up-8fh>

我最近用 Sinatra 完成了我的第一个完整的 Ruby 应用程序！这个项目是我在 Flatiron 全栈开发者项目的课程中的一部分，但我不仅仅是为了开发实践而构建一些东西，我希望我的应用程序是实时可用的(希望有用！)对某人来说。

这篇文章是这个项目的基本总结，包括我是如何开始的，以及我学到和尝试的东西。

## 有了主意

有什么比找出一个你可以帮助别人解决的问题更好的方法来获得一个想法呢？听起来很容易，但我认为“找到一个要解决的问题”通常比看起来要难。人们的问题是复杂的，在这个时候，我没有能力独自开发一个超级复杂的应用程序，所以需要一些时间来找出一个合适的问题来解决。

我决定去问一个农民朋友，我能帮上什么忙。在一次关于她如何计划种植什么的谈话后，她发现自己很难记住什么时候种植什么蔬菜。她一直在每周的计划表上写下要种哪些种子，但是一旦翻到下一页，她会不可避免地忽略一些事情或者忘记种一些蔬菜。此外，每个季节她都必须回去重新查找许多蔬菜的确切种植月份，并再次重写所有内容。

虽然她不想为她的小农场使用成熟(复杂)的农场管理应用程序而烦恼，但有一种简单的方法来跟踪在哪个月种植哪些种子将是一个很大的帮助。由此，我的简单应用程序——[蔬菜追踪器](https://veggie-tracker.herokuapp.com/)——诞生了！

[![Veggie Tracker dashboard](img/e39ecc89f35fe28b747769d9230d5bb7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9KX64tGb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qme3xgoiqy2t1yu5k0f1.png)

## 入门

作为一个初学者，从头开始构建一个应用程序的经历，即使是一个简单的应用程序，也肯定会令人望而生畏。多亏了教程和/或入门代码(例如 Ruby on Rails 教程)，我已经能够构建更复杂的东西了，但最终，它并不像必须自己思考和计划每个小细节那样深入人心。

我做的第一件事不是编码，而是写下应用程序需要做什么的基本概述，或多或少是从用户的角度。归结到最核心的概念，我引用了我朋友的一段对话:

“我希望能够看到这个月我需要种什么蔬菜。”

我想象用户需要做些什么来实现这一点。首先，用户需要能够创建帐户并保存他们的数据。他们还需要能够添加他们想要跟踪的所有蔬菜。我想这可能是有用的，能够跟踪不同领域，农场，或花园地块的蔬菜，所以我决定用户应该能够创建多个农场。

这转化为三个 Ruby 类:用户、农场和蔬菜。接下来，我计划如何设置类关联和数据库。

我的第一次尝试以一种简单的方式连接了我的班级:

[![First attempt at database schema](img/f6ed62ae9109f19409add9e2cae41eef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HiluMp2P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u9kdzoqcfcuuvwdw1zno.png)

然而，在用户拥有多个农场，并且某些蔬菜种植在多个农场的情况下，这种数据库模式并不是很好，因为这些蔬菜只能属于一个农场。我必须在允许我处理这种情况的数据透视表中工作，所以我像这样重构了我的模式:

[![Refactor of database schema](img/9ff5197d82b3c89fed62a2f94dad4909.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WG8MM9wT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iuqp51uyxjm8fptz2v2n.png)

我真的想通过农场来保存与用户相关的蔬菜，但是在实现了数据透视表之后，我无法让它正常工作:/

无论如何，现在我已经有了一个从用户角度出发的计划和从技术角度出发的应用程序的基本布局，为应用程序编码的路径对我来说已经很清楚了。尽管如此，当我尝试一些我以前没有做过的新事物时，我遇到了一些挑战。

## 新挑战和新花样

### 使用测试

虽然我以前从未真正编写过自己的测试，但我在以前的工作中以及在 Flatiron 的课程中已经做了很多测试驱动开发的工作。我认为这是为我自己的应用程序进行测试的好时机。

弄清楚如何正确地连接和配置测试套件(Rspec)以使其实际工作有些困难，我不得不依靠其他代码示例中的代码，但最终，我弄清楚了。对我来说，最困难的部分是试图实现一个先写测试再写代码的流程。因为我还没有编写测试的经验，所以我尝试编写的测试一开始并没有真正工作，所以我发现自己必须首先编写应用程序代码，这样我才能找出测试代码中的问题。

这肯定不是理想的工作流程，并减慢了我的编码进度，但最终有了基本的测试来帮助确保在编码时实现的更改不会破坏代码，这确实让我捕捉到了几个偷偷摸摸的错误，否则我是不会注意到的。更好地理解如何编写好的测试以及如何实际使用 Rspec(或另一个测试套件)绝对是我的任务清单。

### I18n

因为我为其开发应用程序的朋友是日本人，所以该应用程序需要日语版本，但我也想要英语版本。我一直想学习如何使用 I18n 很久了，所以这是我的机会！我在以前的工作中有过使用 I18n 的基本经验，因为我们的应用程序有几种语言版本，但从未从头开始实现过。考虑到许多服务需要本地化为至少两种语言，我认为学习如何使用 I18n 是一个很好的主意。

尽管我知道我想让 Veggie Tracker 有两种语言版本，但我还是等到编写应用程序的一半时才实现它。坏主意。我在让本地化的路由工作时遇到了许多问题(我使用 slugs 来区分语言，例如`/ja`和`/en`)，并且不得不回去修复几乎所有的路由和链接。如果您计划使用 I18n，请在开始时设置它，以避免以后出现重大的调试问题！

### 使用 CGI 对 slugs 进行 URL 编码

我希望我的 URL 更加友好，而不是对我的所有路线都使用对象 id。比如`veggie-tracker.com/users/felice/farms/cattail-farm`比`veggie-tracker.com/users/12/farms/239`可读性更强，更直观。

每当使用日本用户名或农场名称时，这些漂亮、笨拙的路线会立即破坏我的应用程序，因为日本字符对 URL 不友好。幸运的是，经过一些研究，我了解到有一个简单的方法可以解决这个问题: [Ruby 的 CGI 类](https://ruby-doc.org/stdlib-1.9.3/libdoc/cgi/rdoc/CGI.html)。

CGI 类可以帮你很多，包括“检索 HTTP 请求参数、管理 cookies 和生成 HTML 输出。”出于我的目的，它的`#escape`方法可以用来使任何字符串(包括非英语字符)对 URL 友好。可以这么简单:

```
require 'cgi'

CGI.escape(slug) 
```

Enter fullscreen mode Exit fullscreen mode

### 在 Sinatra 中对不同的页面使用不同的布局

在 Sinatra 中，您可以通过在 views 文件夹中创建一个`layout.erb`文件来创建一个所有页面都将使用的布局模板。Sinatra 会自动检查您是否有一个名为`layout.erb`的文件，如果有，就会使用它。布局模板通常包括在每个页面上重复的代码，比如你的头、页眉、导航、页脚等。

如果您遇到某些页面需要不同布局的情况，该怎么办？你可以使用`:layout`选项告诉 Sinatra 在你的控制器中为任何路线使用不同的布局。例如，我想为某些页面选择一个狭窄的布局，只包括一个简单的登录或注册表单:

```
get '/login' do
  if logged_in?
    redirect to "/users/#{current_user.slug}"
  else
    erb :'sessions/login', :layout => :'narrow-layout'
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

以上代码是针对登录页面的。假设用户还没有登录，控制器告诉 Sinatra 使用位于使用`erb :'sessions/login'`代码的`sessions`目录中的`login.erb`文件。然后`:layout => :'narrow-layout'`代码告诉 Sinatra 使用窄模板，而不是默认的布局模板。这里假设在视图文件夹中有另一个名为`narrow-layout.erb`的模板文件。

### 在 Sinatra 中使用 partials 使您的视图文件更干净、更干燥

当你的 Sinatra 视图文件开始变得如此复杂，以至于难以阅读，或者你发现自己在多个地方复制和粘贴相同的 HTML 时，尝试将你的 HTML 块分成部分。

片段很方便，因为它们可以在多个地方重用，所以可以避免重复 HTML，这也意味着如果代码发生变化，您只需要在一个地方编辑它。

分离出 HTML 的各个部分可以使复杂的视图更容易管理和理解，因为你不再需要筛选代码行来找到你需要编辑的确切位置。例如，我将我的 navbar HTML 和 head HTML 分离到单独的部分文件中，因为这些部分很快就会变得又长又复杂。

如果你使用过 Rails，你可能已经使用过相同的技术，使用`render`将 HTML/erb 代码插入到你的视图中。在 Sinatra 中略有不同，因为没有`render`关键字。我的方法是在我的`views`目录中创建一个`partials`目录。然后可以使用 erb 将任何部分直接插入到 HTML 中，就像这样:

```
<header>
  <%= erb :'partials/navbar' %>
</header> 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码假设在一个`partials`目录中有一个`navbar.erb`文件。当然，你可以随意命名你的目录。我还发现这个技巧对于分离复杂的 HTML 代码很有用，这些代码根据一些条件而变化，比如用户是否登录:

```
<% if logged_in? %>
  <%= erb :'partials/welcome' %>
<% else %>
  <%= erb :'partials/onboarding' %>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

### 获取用户反馈

为真人制作蔬菜追踪器的好处是我可以得到真实的用户反馈。一旦我确信这个应用程序是“完美的”，我就把它展示给我的朋友。在短短几秒钟内，她指出了在可用性和功能方面需要改进的几个部分。在开发过程中，很难记住用户的观点，因为你只是试图让应用程序至少正常工作。

在最初的反馈之后，我不得不再花几个小时编写代码来修复一些东西，但我认为这确实是开发中令人兴奋的部分——使你的创作更加直观、易用，当然，对真实的人也有帮助！

不过，我总是尽量小心处理功能请求。用户会要求太阳底下的每一个小功能，但如果你试图实现它们，你的应用程序将很快失去控制，其真正的目的被混淆。这在我之前的工作中经常发生。即使我们实现了功能，用户最终也不会使用它们。添加东西很容易，但如果你意识到你不需要它后想把它拿走，那就要困难得多了。现在，我总是试着问自己，某个特性是否真的有助于解决我的应用程序试图解决的核心问题。

* * *

我还可以写很多其他的东西，但是我现在就在这里结束它。尽管 Veggie Tracker 应用程序可能只有一个用户，但我很高兴它能上线运行，并期待着一点一点地改进它。

查看 [Github](https://github.com/morinoko/veggie_tracker) 上的项目或者亲自尝试一下[蔬菜跟踪器【T3:)](https://veggie-tracker.herokuapp.com/)