# 我如何创建我的第一个插件系统

> 原文：<https://dev.to/trendschau/how-i-created-my-first-plugin-system--400>

大家好，

我很确定你们中的大多数人已经为 CMS 或任何其他软件编写了插件。但你有没有想过为自己的软件项目创建一个自己的插件系统，让别人可以在不触及核心代码的情况下，插入你的软件并对其进行增强？这比制作一个简单的插件要复杂一些。但这比你想象的要容易得多。对我来说，它永远改变了我编写新项目代码的方式。

## 我以前是怎么编码项目的

我不是专业开发人员，而是产品负责人和历史学家(原本)。我编写项目只是为了好玩，为了分散注意力，为了治疗的原因。我 35 岁开始学 PHP，是从《面向孩子的 PHP》这本书上学的。心智上的成长，让我学会了如何用肮脏陈旧的意大利面条代码编写一个类似 tumblr 的应用。它已经以 MVC 的方式组织起来了，但是我仍然遇到了通常的混乱和不可维护的代码。为了解决这些问题，我转向了面向对象和命名空间，我开始使用 PHP-microframework Slim，并且熟悉了 GitHub 和 Composer。一切都被组织在模型、控制器、类、方法、配置文件和外部库中，所以在所有的混乱中有一丝秩序。但是这在某些情况下仍然没有帮助...

## 问正确的问题

这种情况出现在我的名为 [Typemill](https://github.com/trendschau/typemill) 的小项目中，一旦它成熟，应该会成为一个简单的面向作者的平面文件 cms。对于一个真正的 CMS，我必须找到一种方法，让其他编码人员用插件、附件、扩展或任何你想叫它的东西来增强我的系统。所以我问了一些搜索引擎...

...我很惊讶，所有这些搜索引擎根本不了解“插件系统”。糟糕的答案通常是错误问题的结果。所以我深入挖掘并发现，插件系统遵循特殊的编程模式(老实说，我以前并不太关心编程模式)。插件系统有几种模式(阅读 Anthony Ferraras [关于插件模式的文章](https://blog.ircmaxell.com/2012/03/handling-plugins-in-php.html))，但最常见的模式是“中介模式”。中介模式也用于“事件驱动编程”和“事件调度”。

我熟悉 JavaScript 中的“事件”,但我不知道在服务器端代码中也可以使用“事件”。事实上，不仅内容管理系统为它们的插件系统使用事件，而且像 Zend、Laravel 等大多数框架都提供了开箱即用的内置事件系统。

## 我是如何试图理解事件系统的

我试图通过与 IFTTT 比较来理解“事件系统”:如果发生这种情况，那就去做。事实上，您可以用一个简单的、人类可读的 IFTTT 逻辑来描述所有插件，如下所示:

> 如果应用程序生成了一个 html 页面，那么在页面上添加一个新闻订阅表单(哦，不，不要这样做，我们都讨厌它们！！！！)

你甚至可以更进一步，用这样一个简单的数组来描述逻辑:

```
['onHtmlLoaded' => 'addSubscriptionFormMethod'] 
```

Enter fullscreen mode Exit fullscreen mode

数组的键(左边)是事件的名称，数组的值(右边)是方法的名称。所以如果左边的事件发生了，那么应该调用右边的方法。如果这个比那个。很简单。

为了让这个简单的逻辑发挥作用，您需要创建一个特殊的脚本，通常称为“事件调度程序”。但是在你开始编码之前，考虑使用一个优秀的库。

## 使用事件调度程序

一个事件调度器并不太复杂，有一些非常[轻量级的实现](https://github.com/mapkyca/simple-event-dispatcher)，大约有 100 行代码。它们做的事情总是一样的:它们创建并管理一组事件名和事件侦听器，如上所示。事件监听器是可调用的(方法名)。因此，如果您知道事件名称，那么使用像`call_user_func()`或`call_user_func_array()`这样方便的 php 函数调用订阅的方法是没有问题的。这正是所有这些事件调度程序所做的。

最广泛使用的 EventDispatcher 库是 Symfony 事件调度器。它提供了这样的东西:

*   它管理描述的事件名称和可调用的数组。
*   它允许您向该数组添加新的“侦听器”或“订户”。“听众”或“订阅者”是你的插件。
*   它允许您在应用程序中“激发”(或“分派”)事件，以便调用该事件的所有订阅者(插件方法)。

你可以查看 Symfony 的 [dispatcher-class](https://github.com/symfony/event-dispatcher/blob/master/EventDispatcher.php) (只有 240 行)，你会惊讶于一切是多么简单。

## 发起调度员

我们现在将创建我们的小简讯插件，用一个真实的例子来演示 dispatcher。让我们用 GitHub 和 Composer 安装 Symfony EventDispatcher，然后在代码开头的某个地方启动它(例如在 index.php 文件的开头):

```
use Symfony\Component\EventDispatcher\EventDispatcher;

$dispatcher = new EventDispatcher(); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用 EventDispatcher 了。我们将按照以下步骤完成:

*   我们将定义一个名为“onHtmlLoaded”的新事件。
*   我们将在您的应用程序中触发该事件。
*   我们将编写一个订阅这个事件的时事通讯插件。
*   我们将使用插件获取、操作和返回数据。

## [定义一个事件](#define-an-event)

在 Symfony EventDispatcher 中使用您自己的事件之前，您必须定义它们。可以用一个空类来定义一个新事件，如下:

```
namespace Typemill\Events;

use Symfony\Component\EventDispatcher\Event;

class onHtmlLoaded extends Event
{

} 
```

Enter fullscreen mode Exit fullscreen mode

所有新事件都必须扩展 Symfony 的基本“事件”。稍后，我们将在这个类中定义一些有用的方法，但是现在一个空类就足够了。

为了使您的代码有条理，创建一个名为“events”的新文件夹以在一个中心位置收集所有事件定义是一个好主意。

## 火灾事件

让我们回到我们的应用程序，启动新的事件。这可以通过一行代码来完成。

对于我们的时事通讯插件，我们希望在 HTML 内容加载后立即触发事件。在 Typemill 中，我使用 parsedown 库将 markdown 文本转换成 html。所以我添加了这一行简单的代码:

```
$contentHTML = $parsedown->text($contentMarkdown);

$dispatcher->dispatch('onHtmlLoaded', new Typemill\Events\onHtmlLoaded()); 
```

Enter fullscreen mode Exit fullscreen mode

dispatch-method 接受两个参数:事件的名称和事件对象。仅此而已。现在，事件调度程序使用内部数组并调用订阅了该事件的所有插件方法。

但是等等，现在还没有插件。所以现在让我们创建我们的时事通讯插件。

## 时事通讯-插件:订阅事件

我为所有插件创建了一个新的“插件”文件夹，在那里我创建了一个名为“时事通讯”的文件夹。我们时事通讯插件的类看起来像这样:

```
namespace Plugins\Newsletter;

use Symfony\Component\EventDispatcher\EventSubscriberInterface;
use Typemill\Events\onHtmlReady;

class Newsletter
{
    public static function getSubscribedEvents()
    {
        return array('onHtmlLoaded' => 'myNewsletterMethod');
    }

    public function myNewsletterMethod()
    {
        die('I will add the newsletter to the html-page one day');
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后是我们在本文开头提到的阵列。这个数组看起来完全一样:事件的名称在左边，插件方法的名称在右边。如果这个比那个。

返回数组的方法总是`getSubscribedEvents()`，并且必须是公共静态方法。Symfony EventDispatcher 将简单地检查所有类是否存在该方法，并将数组添加到事件和侦听器或订阅者的中央数组中。就这么简单。

## 加载你的插件

最后一步仍然没有完成:我们必须初始化我们的插件类，并将它添加到 dispatcher，否则 dispatcher 不知道它的存在。我们应该在应用程序开始时加载所有插件，并像这样向调度程序添加一个插件:

```
$dispatcher = new EventDispatcher();

....
....

$dispatcher->addSubscriber(new \Plugins\Newsletter()); 
```

Enter fullscreen mode Exit fullscreen mode

你当然不想手动添加所有插件。对于 Typemill，我编写了一个单独的类，它扫描所有的插件文件夹，执行一些检查并返回完全限定的类名，因此您可以这样做:

```
$dispatcher = new EventDispatcher();

...

$plugins = new Typemill\Plugins();
$pluginClassnames = $plugins->load();

foreach($pluginClassnames as $pluginClassname)
{
  $dispatcher->addSubscriber(new $pluginClassname());
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧，我省略了很多细节，但这应该足够开始了。

让我们检查一下到目前为止我们有什么:如果我们现在访问 Typemill 中的一个页面，那么事件“onHtmlReady”被触发，订阅的时事通讯插件 get 被调用。现在，newsletter 方法只是简单地终止了应用程序，并打印出“有一天我会将时事通讯添加到 html 页面中”的字样。

它工作了。但是它确实不像是一个很酷的插件。所以让我们让它更有用一点。

## 用插件操纵数据

为了创建我们的时事通讯插件，我们需要获取 html 内容，添加时事通讯订阅表单，并再次将所有内容返回给应用程序。

为此，我们可以简单地向 event 类添加一些 getters 和 setters，这是我们之前定义的。这么办吧:

```
namespace Typemill\Events;

use Symfony\Component\EventDispatcher\Event;

class onHtmlLoaded extends Event
{

    protected $data;

    public function __construct($data)
    {
        $this->data = $data;
    }

    public function getData()
    {
        return $this->data;
    }

    public function setData($data)
    {
        $this->data = $data;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们在应用程序中触发一个事件时，我们可以像这样简单地传递一些数据:

```
$contentHTML = $parsedown->text($contentMarkdown);

$dispatcher->dispatch('onHtmlLoaded', new Typemill\Events\onHtmlLoaded($contentHTML)); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以像这样访问和返回时事通讯插件中的数据:

```
class Newsletter
{

    public static function getSubscribedEvents()

    {
        return array('onHtmlLoaded' => 'myNewsletterMethod');

    }

    public function myNewsletterMethod($data)
    {
        $content = $data->getData(); 

        $content .= '<p>I am the newsletter subscription form</p>';

        $data->setData($content);
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

我们已经在内容中添加了我们的时事通讯表单，并将所有内容返回给应用程序。最后一个技巧是，我们的应用程序从现在开始使用被操纵的数据。这可以这么简单地完成:

```
$contentHTML = $parsedown->text($contentMarkdown);

$contentHTML = $dispatcher->dispatch('onHtmlLoaded', new Typemill\Events\onhtmlLoaded($contentHTML))->getData(); 
```

Enter fullscreen mode Exit fullscreen mode

瞧，应用程序现在会将所有内容发送到 Twig 模板引擎，并向用户显示页面。我们在这个页面上添加了一个时事通讯表单，但没有触及应用程序的任何核心文件。我们的第一个插件完成了！

## 事件和你的应用程序的生命周期

在现实生活中，您希望为您的应用程序提供许多入口点，以便客户端编码人员可以在许多步骤中操作不同的数据。所以你的事件系统通常会跟随你的应用程序的生命周期，并在生命周期的每一步触发事件。对于 Typemill，它看起来像这样:

*   应用程序加载所有设置。
*   应用程序加载所有插件。
*   应用程序加载一个类似 Twig 的模板引擎。
*   应用程序路由到创建页面的控制器。
*   控制器加载所请求的内容文件。
*   控制器将降价内容转换成 HTML。
*   控制器收集一些其他数据(例如用于导航)。
*   控制器将所有数据发送到模板引擎。
*   模板引擎向用户显示页面。

步长越小，灵活性越大。

## 你刚刚创建了自己的插件系统

上面的代码相当粗糙，比你在 symfony 事件调度器本身的文档中所能读到的并不多。如果你想创建一个真正有用的插件系统，你必须添加许多细节:所有这些插件应该如何加载？用户如何配置你的插件？你应该把哪些有用的方法添加到一个基本插件类中？如何用插件给你的应用程序添加新的路线？

很多开放性的问题，但是基本都做好了。对我来说，找到这些资源和例子真的很有帮助。如果你想创建自己的插件系统，你可以查看一些现实生活中的实现(我使用了 Grav、Leafpub 和许多其他工具来找到自己的方法将插件系统集成到 Typemill 中)。

但是，即使你不想创建自己的插件系统，这种事件驱动的编程仍然是一种很好的方式来分离你的代码，并给混乱的新代码项目带来更多的秩序。