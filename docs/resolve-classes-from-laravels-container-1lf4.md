# 从 Laravel 的容器中解析类

> 原文：<https://dev.to/wilburpowery/resolve-classes-from-laravels-container-1lf4>

[首次发布于我的网站](https://wilburpowery.co/posts/dependency-injection-with-laravel-container/)

# 拉勒维尔容器

Laravel 的容器无疑是框架提供的最强大的工具之一。如果您以前使用过 Laravel，那么您已经在不知不觉中使用了服务容器。在幕后，Laravel 从容器中解析所有的控制器类，这就是为什么你可以键入 hint any class 并很容易地得到一个实例。

## 从容器中解析一个类

用 Laravel 从容器中解析一个类非常容易，实际上有几种方法可以做到。选择你最喜欢的，并坚持下去。

```
// Using the App Facade
App::make('Slack\Api');

// Using the app helper function
app('Slack\Api');

// Using the resolve helper function
resolve('Slack\Client'); 
```

Enter fullscreen mode Exit fullscreen mode

```
<?php

namespace Slack;

class Api
{
    public function __construct(Class $dependency)
    {
        $this->dependency = $dependency;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 容器会注意到在我们的构造函数中有一个类型提示，它会尝试解析这个依赖关系并把它传递给类。

## 不是所有的依赖项都可以被容器解析

如果存在容器不能(或者不应该)解决的依赖怎么办？好吧，你可以传递一个数组作为第二个参数，其中包含任何应该发送给这个类的数据。

为了更好地理解这在哪里是必要的，让我们探索一个真正的开源项目。我们将使用我几周前发现的一个包， [Onboard](https://github.com/calebporzio/onboard) ，由 [@calebporzio](https://twitter.com/calebporzio) 创建。

让我们来看看`OnboardingManager`类，具体来说就是构造函数:

```
<?php

namespace Calebporzio\Onboard;

class OnboardingManager
{
    /**
    * Create the Onboarding Manager (should always be a singleton).
    *
    * @param mixed $user The parent app's user model.
    * @param \Calebporzio\Onboard\OnboardingSteps $onboardingSteps
    */
    public function __construct($user, OnboardingSteps $onboardingSteps)
    {
        $this->steps = $onboardingSteps->steps($user);
    }

    // rest of class method...
} 
```

Enter fullscreen mode Exit fullscreen mode

注意我们没有类型暗示`$user`依赖，所以容器不知道如何为我们获取这个依赖。相反，我们需要在解析容器中的类时传递它。

## 让我们从容器中解析这个类

这个包有一个可以在用户模型中使用的`GetsOnboarded`特征，它只有一个`onboarding`方法。

```
<?php

namespace Calebporzio\Onboard;

use Illuminate\Support\Facades\App;

/**
 * This is the trait to be added to the app's User class.
 */
trait GetsOnboarded
{
    /**
    * This provides a pathway for the package's API
    *
    * @return \Calebporzio\Onboard\OnboardingManager $onboarding
    */
    public function onboarding()
    {
        return App::make(OnboardingManager::class, ['user' => $this]);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意我们是如何使用`App` Facade 来解析容器中的类，以便传入类型暗示的`OnboardingSteps`依赖项，但是我们手动传入了`$user`依赖项的值。

为了可读性，Laravel 还提供了一个名为`makeWith`的`App::make`方法的别名，它只是在幕后调用`make`方法。

* * *

知道您可以从容器中开始解析类，并让它们的所有依赖项自动为您解析。你有很多方法来访问容器，使用带有`make`方法的`App` facade 或者`app`或者`resolve`辅助函数。当在服务提供商内部时，你甚至可以使用`$this->app`来访问容器。

我会继续写更多关于使用容器的内容，所以如果你感兴趣的话，在 Twitter 上关注我，一旦下一篇帖子发布，我就会得到提醒，我是 [@wilburpowery](https://twitter.com/wilburpowery) 。