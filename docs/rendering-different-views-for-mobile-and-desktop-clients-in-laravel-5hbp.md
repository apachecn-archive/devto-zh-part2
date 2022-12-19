# 在 Laravel 中为移动和桌面客户端呈现不同的视图

> 原文：<https://dev.to/matthewbdaly/rendering-different-views-for-mobile-and-desktop-clients-in-laravel-5hbp>

这是一个有点奇怪的帖子。它开始解释了几年前我是如何在 CodeIgniter 网站上解决一个问题的，但后来被修改为在 Laravel 工作。在这个过程中，我意识到把它作为中间件来实现是有意义的，最后我把它放到了一个包中。然而，理解它背后的概念仍然是有用的，即使您更喜欢只安装完整的软件包，因为您的需求可能与我的略有不同。

在 web 开发论坛上，经常会看到以下问题的变体:

> 如何将移动设备上的用户重定向到网站的移动版本？

令人惊讶的是，这仍然是一个突然出现的问题。多年来，人们普遍认为这个问题的正确解决方案是响应式设计。然而，在某些情况下，这可能不足以满足某些应用。例如，您可能有一个应用程序，其中某些功能只在特定的上下文中有意义，或者您的用户界面可能需要针对特定的环境进行优化。

问题是，一个专用的移动网站也不是一个好主意。除此之外，这意味着用户不能轻松地在桌面和移动版本之间使用相同的书签，并可能导致至少一些服务器端逻辑被复制。

幸运的是，还有另一种方式——[动态服务](https://developers.google.com/search/mobile-sites/mobile-seo/dynamic-serving)允许你基于用户代理呈现不同的内容。如果用户的客户端没有被正确检测到或者他们更喜欢另一个版本，您也可以轻松地让用户自己在桌面和移动版本之间切换。我已经在几年前为一个 CodeIgniter 站点实现了这个。以下是您如何在 Laravel 中实现它，尽管如果您理解了它背后的原理，它应该很容易适用于任何其他框架。

不要试图自己实现移动用户代理检测。相反，找到一个积极维护的实现，并用 Composer 安装它。这样，您就可以合理地确定，随着新的移动设备进入市场，只要您保持更新，软件包就会正确地检测到它们。我倾向于选择经纪人，因为他有强大的支持。

我们可以使用代理来提供基于用户代理的不同内容。然而，用户代理字符串是出了名的不可靠——如果一个新的移动设备出现，但它没有正确地出现在代理中，用户可能会发现自己被迫使用错误的 UI。相反，我们需要在会话中检查一个标志，该标志指示会话是否是移动的。如果没有设置，我们根据用户代理来设置。这样，如果您需要提供覆盖检测到的会话类型的功能，您只需更新该会话变量，就可以在应用程序的其他地方进行更正。我倾向于在页脚使用一个按钮，发出 AJAX 请求来切换标志，然后重新加载页面。

您还需要设置 HTTP 响应头`Vary: User-Agent`来通知客户端(不仅包括搜索引擎，还包括连接两端的代理，如 Varnish 或 Squid)响应将因用户代理而异，以防止用户得到错误的版本。

中间件显然是做这件事的地方。这里有一个中间件来设置会话变量和适当的响应头:

```
<?php

namespace App\Http\Middleware;

use Closure;
use Jenssegers\Agent\Agent;
use Illuminate\Contracts\Session\Session;

class DetectMobile
{
    protected $agent;

    protected $session;

    public function __construct(Agent $agent, Session $session)
    {
        $this->agent = $agent;
        $this->session = $session;
    }

    /**
     * Handle an incoming request.
     *
     * @param \Illuminate\Http\Request $request
     * @param \Closure $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        if (!$this->session->exists('mobile')) {
            if ($this->agent->isMobile() || $this->agent->isTablet()) {
                $this->session->put('mobile', true);
            } else {
                $this->session->put('mobile', false);
            }
        }
        $response = $next($request);
        return $response->setVary('User-Agent');
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以直接使用会话来检索`mobile`标志，但是由于您可能正在视图中工作，因此为这个标志创建助手是有意义的:

```
<?php

if (!function_exists('is_mobile')) {
    function is_mobile()
    {
        $session = app()->make('Illuminate\Contracts\Session\Session');
        return $session->get('mobile') == true;
    }
}

if (!function_exists('is_desktop')) {
    function is_desktop()
    {
        $session = app()->make('Illuminate\Contracts\Session\Session');
        return $session->get('mobile') == false;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你想提供完全不同的视图，你可以在你的控制器中使用这些助手。相反，如果您希望基于用户代理有选择地显示和隐藏 UI 的某些部分，您可以在视图中使用它们来确定应该显示页面的哪些部分。

代理提供的功能不仅仅是检测用户代理是移动设备还是桌面设备，您可能会发现这对于开发用于检测机器人的中间件，或者根据用户的设备类型或操作系统向用户显示不同的内容非常有用。如果您只需要检测用户是移动客户端还是桌面客户端，这个中间件应该足够了。