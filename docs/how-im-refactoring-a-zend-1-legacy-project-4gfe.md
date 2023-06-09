# 我如何重构 Zend 1 遗留项目

> 原文：<https://dev.to/matthewbdaly/how-im-refactoring-a-zend-1-legacy-project-4gfe>

在我目前的工作中，一年中的大部分时间我都在维护和开发一个 Zend 1 遗留项目。不得不说，这是我见过的最糟糕的代码库，有许多反模式的教科书示例，意大利面条式的 jQuery，复制粘贴的代码和过于复杂的方法。这是一个相当典型的由缺乏经验的开发人员在旧的 MVC 框架上构建项目的例子(在我的 CodeIgniter 时代，我一直负责构建类似的东西)。

在这篇文章中，我将介绍我采取的一些步骤，以帮助控制这个遗留项目。在撰写本文时，它们并不都是完整的，但它们都有助于使这个明显蹩脚的项目变得更好。在从事这个遗留项目的过程中，我发现保罗·琼斯的书*在 PHP 中实现遗留应用程序的现代化*非常有用，如果你正在从事一个类似的遗留项目，我强烈建议购买一本。我还发现 [Sourcemaking](https://sourcemaking.com/) 在识别使用中的反模式、重构策略和适用的设计模式方面是一个有用的资源。

# 移动到 Git

当我第一次开始做这个项目时，这个库在 Subversion 中，而且绝对庞大——检查它花了两个小时！不用说，我的第一个动作是将其迁移到 Git。我用这篇文章作为指南，它非常简单，但是花了我第一天的时间。

# 添加迁移

下一项工作涉及到对数据库进行一些更改。不幸的是，Zend 1 不包括迁移，也没有人添加第三方解决方案。因此，我做了一些研究，最终偶然发现了 [Phinx](https://phinx.org/) ，这是一个带有命令行运行程序的独立迁移包。使用它，很容易开始添加迁移，对数据库结构和设备进行任何必要的更改。

# 将依赖项移动到编写器

该项目使用了 Composer，但程度有限——框架本身在`library/`文件夹中，其他几个依赖项也存储在这里。`vendor/`目录也被签入版本控制。因此，我从 Git 中取出了供应商文件夹，并添加了`zendframework/zendframework1`作为依赖项。这极大地减少了存储库的大小。

# 清理注释代码

有太多的注释代码。有些甚至被错误地注释掉了(PHP 代码用 HTML 注释注释掉了)。我的观点是，最好不加思索地删除带注释的代码，因为它可以从版本控制中检索到，而且它可能会令人困惑，所以我一直在删除我遇到的任何带注释的代码。

# 重构重复代码

代码库的最大问题之一是高度重复——大量代码，尤其是视图层的代码，被复制和粘贴。在存储库上运行 PHPCPD 显示，不包括视图，大约 12%的代码库被复制和粘贴，这是一个可怕的数字。因此，我开始积极地将重复代码重构为助手和特征。截至目前，不包括视图在内的重复量约为 2.6%，这显然是一个很大的改进。

# 将对象创建代码重构为持久化代码

有一些用于创建和更新各种对象的极其复杂的代码被塞进了控制器中，并且涉及大量重复的代码。我过去使用过专用的持久化类，效果很好，所以我把代码放到了持久化类中，以集中创建不同对象的逻辑。它仍然比我想要的要复杂得多，但至少现在它已经脱离了控制器，可以在某种程度上进行测试。

# 创建存储库

代码库最有问题的部分之一是模型。无论是谁负责它们，似乎都不能决定它们是代表单个域对象，还是代表获取这些对象的方法的容器，所以这两种责任被混合在同一个类中。这意味着你必须实例化一个对象，然后用它调用其中一个方法来获得该对象的另一个实例，如下例:

```
$media = new Application_Model_Media;
$media = $media->find(1); 
```

Enter fullscreen mode Exit fullscreen mode

因此，我决定将这些方法提取到单独的存储库类中，将模型作为纯领域对象。不幸的是，缺乏依赖注入使得实例化存储库成为问题。由于这个原因，现在存储库只实现静态方法——这并不理想，但比我们现在拥有的要好。

我首先为我想要迁移的方法创建接口，并让模型实现它们。然后，在从模型中移除接口之前，我将这些方法从模型中移到了存储库类中，并修改了对它们的所有引用。现在，一个典型的查找请求看起来像这样:

```
$media = App\Repository\Media::find(1); 
```

Enter fullscreen mode Exit fullscreen mode

还没有完成，但是已经迁移了一半以上。

一旦完成，我就可以考虑重构模型中的逻辑，使它们更容易使用——现在每个模型都有专用的 setters 和 getters(以及一些填充它们的可怕逻辑),我正在考虑修改它们，以允许通过`__get()`和`__set()`魔法方法访问属性。另一个选择是考虑将数据库层迁移到 Doctrine，因为这样我们可以重用 getters 和 setters，但是我还没有做出明确的决定。

# 添加测试

这个应用程序的糟糕设计使得它很难测试，所以现在覆盖率很低。我一直在使用 Behat 为一些最基本的功能生成一组基本的验收测试，但是它们很脆弱，可能会被数据库更改破坏。我还添加了一些(甚至更脆弱的)golden master 测试，使用的技术将在以后的博客文章中提到。我已经对我添加的三个 persister 类和一些实用程序类进行了单元测试，但是远没有达到我想要的水平。

# 重构代码出胖控制器

胖控制器是我见过的一种反模式，事实上，过去我也对自己负责，这个项目中就有胖控制器——在胖控制器上运行 PHP Mess Detector 相当令人清醒。绝大多数代码都集中在控制器中，将它重构到其他类中需要很长时间。

Zend 1 确实有控制器助手的概念，这对于删除一些重复代码很有用，同时更多的共享代码被重构为 traits。此外，我添加的实用工具包括一个 Laravel 风格的集合类，使用它我能够将许多非常复杂的数组处理重构为简单得多的链式集合处理。然而，这仍然需要很大的努力。

# 添加事件

当我被要求添加对用户何时查看某些资源的跟踪时，缺乏一个像样的事件系统导致了特别的问题，所以我使用了 PHP 联盟的事件包。我也开始将一些其他功能转移到事件监听器，但这是另一件需要很长时间的事情。

# 重构前端

像许多遗留项目一样，前端是一堆乱七八糟的 jQuery 代码，到处都有一些把手模板。它非常简单，如果有一个合适的前端框架会很有帮助，但是完全重写是不可能的。

最近有人要求我在管理界面中添加两个新的模态，我认为采用新的方法比添加更多的 jQuery 面条更有价值。Angular 1 正在过时，所以这不是一个选项，Angular 2+将需要使用 Typescript，这在遗留应用程序的上下文中可能会有问题，复杂性也是一个问题。Vue 是一种可能性，但我总是觉得 Vue 试图做得太多了。相反，我决定选择 React，因为:

*   我一直很喜欢使用 React，尽管我过去没有太多机会这样做。
*   我们使用 Laravel Mix 来处理 CSS 和 JS 文件(它可以用于非 Laravel 项目)，并且它有一个 React 的预置
*   React 非常适合被增量地添加到现有的项目中，而不需要完全重写(毕竟，它适用于脸书…)，所以用它做一个单一的模型是很简单的
*   这很容易测试——您可以使用快照测试来检查它是否保持一致，并且使用 Enzyme 可以很容易地为其他测试导航呈现的组件

这两种模式结果都很好，并于最近投入使用。第一个花了相当长的时间来编写，然后当我编写第二个时，我不得不花一些时间来使子组件更加通用，并将一些功能集成到更高阶的组件中，但现在已经完成了，应该可以直接编写更多的组件。

从长远来看，我计划迁移越来越多的管理员，以便随着时间的推移做出反应。前端也有一个新的主页，计划也是使用 React。一旦整个 UI 都使用 React，这将消除大部分(如果不是全部)视图层中的重复代码问题，并允许最终将应用程序转变为单页面 web 应用程序。

# 升级 PHP 版本并迁移到新服务器

当我开始这个项目时，它运行在一个运行 PHP 5.4 的旧服务器上，但是有计划迁移到一个运行 PHP 5.6 的新服务器上。测试的缺乏使得很难验证它在 5.6 中不会崩溃，但是使用 PHP 兼容性和 CodeSniffer 我能够找到大多数问题。开发期间，我在本地 PHP 5.6 上运行它，这样任何新的开发都可以在更现代的版本上完成。最后，到新服务器的迁移相当无缝。

我们将不得不考虑再次迁移到一个更新的 PHP 版本，因为从今年年底开始不再支持 5.6，但是现在来看这可能太冒险了。

# 命名空间代码

因为 Zend 1 早于 PHP 名称空间，所以代码没有名称空间。这是我计划要解决的问题——表单和模型类对名称空间应该是直接的，但是控制器有点问题。在我看这个之前，我正在等待库的完成。

# 添加 PSR-3 测井

现有的日志记录解决方案并没有那么好。它有几个不同的日志解决方案的驱动程序，但是没有什么非常现代的——其中一个是现在已经停产的 Firefox 的 Firebug 扩展。然而，它与 PSR-3 非常相似，所以要替换它并不困难。我安装了 Monolog，并修改了 bootstrap 文件，将其作为日志存储在 Zend 注册表中——这样，我们可以设置许多不同的处理程序。现在，当试运行或生产中出现错误时，我让它记录到一个专用的 Slack 通道中，这使得检测问题变得更加容易。这也使得设置许多其他日志处理程序变得容易，比如 ELK 堆栈。

# 调试

[发条](https://underground.works/clockwork/)是我常用的 PHP 调试解决方案，Zend 1 缺乏对它的支持使得它很难使用。幸运的是，按时实现自己的[数据源](https://underground.works/clockwork/extending-data-sources?#content)非常简单。我设置它使用前面提到的记录器作为数据源，以及 [Zend 1 profiler](https://framework.zend.com/manual/1.12/en/zend.db.profiler.html) 。我还为 events 实现添加了一个数据源，并添加了一个全局`clock()` helper 函数，以及一个用于 Symfony VarDumper 组件的函数。这些给了我相当好的调试体验。

# 添加控制台命令

我之前提到过，我最近一直在使用 Symfony 的控制台组件，这个项目就是为什么。Zend 1 没有附带任何类型的控制台任务运行器，我们需要一种简单的方法来执行某些任务，例如:

*   设置存储过程
*   使用 Faker 匿名化用户数据
*   重新生成音频和视频文件的持续时间

此外，我想要一个 Laravel Tinker 风格的交互式外壳。我可以用 PsySh 和控制台组件来完成这个任务。对于缺少控制台任务运行器的遗留项目，值得考虑添加一个。

# 配置

Zend 1 中的配置系统非常痛苦——它要求您在其中定义多个环境。我已经集成了 DotEnv，但是只有部分配置被迁移了，所以还有很多工作要做。

# 还剩下什么可做的

代码库的状态比以前好了很多，但仍有大量工作要做。显然，Zend 1 仍然适用于 PHP 7.1，但不适用于 7.2，因此在某种程度上，我们可能需要完全抛弃 Zend 1。这个过程已经从我们抛弃 Zend_Log 而使用 Monolog 开始，随着时间的推移，我计划用其他包替换 Zend 1 的各种组件，无论是来自 Zend Framework 新版本的包，还是其他地方的包。虽然有很多关于将 Zend 1 迁移到更高版本的文章，但实际上很少有文章深入探讨细节——当然没有什么比一步一步的指南更有用了。

数据库层尤其糟糕，将一些方法重构为存储库类只是控制这种情况的第一步。一旦完成，我将开始浏览模型，看看是否有更多的方法作为存储库中的静态方法更有意义，并可能重命名其中的一些方法。然后，我们可以考虑增量迁移到另一个数据库接口(新版本的 Zend DB 或 Doctrine)的可能性，或者通过使用神奇的方法而不是 getters 和 setters 来重构现有模型以减少样板文件。

依赖注入在某些时候是必须的，但现在还不实用——Zend 1 控制器实现了一个定义构造函数参数的接口，所以你不能传入任何额外的参数，所以需要等到控制器不再使用 Zend 1。我一直使用 Zend Registry 作为穷人的 DI 容器，因为它允许在整个应用程序中共享单个对象，但从长远来看，这不是一个好的解决方案。

路由也很麻烦——Zend 1 的路由都存储在引导文件中。我更喜欢使用类似于`league/route`的东西，它将允许使用不同的控制器方法来处理不同的 HTTP 方法到相同的路由，从而更容易区分 GET 和 POST 请求的处理。

我还希望在某个时候建立一个队列系统来处理视频和音频内容——目前它是通过从 PHP 运行一个 shell 命令来处理的，这意味着如果出现问题，您无法轻松获得反馈。将它迁移到一个队列系统，由 Redis 之类的东西支持，会有很大帮助。

# 分享你的故事

我很乐意听到任何关于重构遗留应用程序的类似故事——你是如何用那些遗留应用程序解决各种问题的(或者你是如何解决我遇到的问题的)，你使用过的工具，等等。欢迎在评论中提供详细信息。

像这样的遗留项目可能会非常令人沮丧，但在一段时间内将它置于控制之下也是非常值得的。我的经验是，你可以通过小而有规律的步骤获得最好的结果，随着时间的推移，你使用代码库的经验将会提高。