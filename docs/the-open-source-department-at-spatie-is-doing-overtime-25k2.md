# Spatie 的开源部门在加班

> 原文：<https://dev.to/freekmurze/the-open-source-department-at-spatie-is-doing-overtime-25k2>

糟糕的标题，因为我们[不在](https://twitter.com/freekmurze/status/949299572689522688) [Spatie](https://spatie.be) 加班，但是我们的团队一直忙于推出新的开源东西。在过去的几周里，我们的团队发布了三个新的包。在这篇文章中，我想把他们也介绍给你。

## 床单

首先是由[塞巴斯蒂安](https://twitter.com/sebastiandedeyne)创作的 [spatie/sheets](https://github.com/spatie/sheets) 。这个包帮助你存储和检索保存在纯文本文件中的内容。如果你有一个使用 markdown 文件而不是数据库来存储内容的博客或网站，这是非常好的。Seb 已经在[的个人网站](https://github.com/sebastiandedeyne/sebastiandedeyne.com/blob/42fb228f87ce3a0ef69a53f68ad49ed0b0e0020b/composer.json#L26)上使用它，我们计划很快在[我们的指南网站](https://guidelines.spatie.be) [上使用它。](https://github.com/spatie/guidelines.spatie.be/pull/38)

## 打字

接下来是由[布兰特](https://twitter.com/brendt_gd)制作的 [spatie/typed](https://github.com/spatie/typed) 包。他喜欢 T4 式的系统和严格。PHP 的类型系统有很大的改进空间。也许这些改进应该在内核中完成，但是在用户空间中也可以做很多。这就是为什么 Brent 创建了 [spatie/typed](https://github.com/spatie/typed) 包，它给你提供了诸如类型化列表、泛型、元组、结构等等的东西。

## laravel-视图-组件

Seb 发布了另一个包，名为[spatie/laravel-view-components](https://github.com/spatie/laravel-view-components)。它基于[在 Laravel 中引入视图组件，这是由](https://laravel-news.com/introducing-view-components-on-laravel-an-alternative-to-view-composers) [Jeff Ochoa](https://twitter.com/@Jeffer_8a) 发布的视图作曲者的替代方案。它允许您构建可以在刀片视图中轻松呈现的组件类。这里有一个摘自自述文件的快速示例:

```
namespace App\Http\ViewComponents;

use Illuminate\Contracts\Support\Htmlable;
use Illuminate\Contracts\Auth\Guard;
use Spatie\Menu\Laravel\Menu;

class MainMenuComponent implements Htmlable
{
    /** @var \Illuminate\Contracts\Auth\Guard */
    private $guard;

    /** @var string */
    private $class;

    public function __construct(Guard $guard, string $class = null)
    {
        $this->guard = $guard;
        $this->class = $class;
    }

    public function toHtml(): string
    {
        $menu = Menu::new()
            ->addClass($this->class)
            ->url('/', 'Home')
            ->url('/projects', 'Projects');

        if ($this->guard->check()) {
            $menu->url('/admin', 'Adminland');
        }

        return $menu;
    }
}

@render('mainMenuComponent', ['class' => 'background-green']) 
```

我们现在在一个客户项目中使用这种方法，这真是一种很好的工作方式。

## laravel-事件-投影仪

最后，我正忙于创建一个更大的包，旨在成为在 Laravel 应用程序中开始事件源的最简单的方法。它还没有完全准备好进入黄金时段，但像往常一样，我正在公开构建它。你已经阅读了一些文档来学习如何使用这个包。你会在 GitHub 的[这个回购中找到源代码。我欢迎任何关于如何在回购的问题跟踪器上使这个包更好的问题和建议。我的目标是在六月的某个时候发布它。](https://github.com/spatie/laravel-event-projector)

还有更多的在管道中。保持与我们团队的同步是开源领域的工作，在 Twitter 上关注我们的成员。这里有一个方便的推特列表来关注他们。