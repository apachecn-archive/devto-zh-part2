# 使用浏览器列表检测过时的浏览器

> 原文：<https://dev.to/amplifr/outdated-browser-detection-with-browserslist-10co>

用 Node.js 配置目标浏览器的标准方式是 [Browserslist](https://github.com/browserslist/browserslist) 。可以添加以下内容:

```
{  "browserslist":  [  "last 2 version",  "not dead"  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

到`package.json`或`.browserslistrc`配置文件:

```
# Browsers that we support
last 2 version
not dead 
```

Enter fullscreen mode Exit fullscreen mode

这两个相似的例子意味着目标浏览器是两个最新版本，并且浏览器没有死。

这个配置被很多前端工具使用，比如 [Autoprefixer](https://github.com/postcss/autoprefixer) 、 [Babel](https://github.com/babel/babel/tree/master/packages/babel-preset-env) 等等。

但是在这篇文章中，我将要写的是关于 [Browserslist Useragent](https://github.com/browserslist/browserslist-useragent) 前端工具，用于查找一个给定的用户代理字符串是否满足一个`Browserslist`浏览器:

安装`browserslist-useragent` :

```
npm install browserslist-useragent 
```

Enter fullscreen mode Exit fullscreen mode

并且您可以通过`User-Agent`字符串来确定您的浏览器是否匹配:

```
const { matchesUA } = require('browserslist-useragent')

matchesUA(userAgentString, options)

// with browserslist config inferred
matchesUA('Mozilla/5.0 (Windows NT 10.0; rv:54.0) Gecko/20100101 Firefox/54.0')
//returns boolean

// with explicit browserslist
matchesUA('Mozilla/5.0 (Windows NT 10.0; rv:54.0) Gecko/20100101 Firefox/54.0', { browsers: ['Firefox > 53']})
// returns true 
```

Enter fullscreen mode Exit fullscreen mode

成像我们有这样的`.browserslistrc`配置文件:

```
last 2 versions
not IE 11
not ExplorerMobile 11
not last 1 OperaMini version
not OperaMobile 12.1
not dead 
```

Enter fullscreen mode Exit fullscreen mode

在 browserslist:
的帮助下，我们可以得到详细的浏览器规则数组

```
const browserslist = require('browserslist')
const fs = require('fs')

fs.writeFileSync('./browsers.json', JSON.stringify(browserslist())) 
```

Enter fullscreen mode Exit fullscreen mode

对于上面的示例，它将生成带有
的 json 文件

```
[  "and_chr 67",  "and_ff 60",  "and_qq 1.2",  "and_uc 11.8",  "android 67",  "android 4.4.3-4.4.4",  "baidu 7.12",  "chrome 69",  "chrome 68",  "edge 17",  "edge 16",  "firefox 62",  "firefox 61",  "ios_saf 11.3-11.4",  "ios_saf 11.0-11.2",  "op_mob 46",  "opera 55",  "opera 54",  "safari 11.1",  "safari 11",  "samsung 7.2",  "samsung 6.2"  ] 
```

Enter fullscreen mode Exit fullscreen mode

这是确定浏览器与 Node.js 匹配的方法。

为什么我们需要检查两端的浏览器版本:后端和前端？
如果您的现代 javascript 前端无法在旧浏览器上加载，我们仍然可以使用后端渲染来编写一些 HTML 来通知用户这个问题:

[![The outdated browser html block sample](img/c7e9fea2778e3498aaba2c7f4ff99cab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--U1rohQXT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/arfb7qw3ely8pq2j20k5.png)

❗This HTML 块可以在任何浏览器中工作，不管它有多旧。

而如果你的后端是用 Ruby 编写的- use 仍然可以使用原来工具的端口到 Ruby-[browsers list-user agent gem](https://github.com/browserslist/browserslist-useragent-ruby)。它的工作方式与其 Node.js 版本相同——从`User-Agent`头字符串中识别系列和版本，并将其与`browserslist`匹配——由`Browserslists`工具产生的规则。

# 单项工程

用法很简单——只需要您先生成`browsers.json`文件。

```
 class ApplicationController
  def supported_browser?
    @browsers ||= JSON.parse(Rails.root.join("browsers.json").read)
    matcher = BrowserslistUseragent::Match.new(@browsers, request.user_agent)
    matcher.browser? && matcher.version?(allow_higher: true)
  end
  helper_method :supported_browser?
end 
```

Enter fullscreen mode Exit fullscreen mode

然后将这段代码添加到 Rails 应用程序布局模板:

```
- if !supported_browser?
  .div 
div( style: "position: fixed; bottom: 0; right: 0; padding: 8px 10px; background: #e9502f; color: white; width: 100%; z-index: 10; text-align: center;" )
    .div
      = t('unsupported_browser') 
```

Enter fullscreen mode Exit fullscreen mode

❗This 老式风格是故意选择的:“风格”——属性将在任何地方工作！

在这里。对于所有前端和后端作为一个[实体项目](https://evilmartians.com/chronicles/evil-front-part-1)生活在一起的 Rails 项目来说，它将会工作得很好。

## 分离前端和后端项目

如果您已经为 Ruby 后端和 Node.js 前端分离了项目，您将更喜欢通过 HTTP 获得 browsers.json。您需要执行以下操作:

*   通过将 browserslist 输出放入`public`文件夹，使用`/browsers.json`路径来呈现该输出:

```
fs.writeFileSync(
  path.join(__dirname, 'public', 'browsers.json'),
  JSON.stringify(browserslist(undefined, { path: path.join(__dirname, '..') }))
) 
```

Enter fullscreen mode Exit fullscreen mode

*   通过 HTTP 进入 ruby-backend 代码:

```
browsers = JSON.parse(Faraday.get('http://frontend-domain.local/browsers.json').body)
matcher = BrowserslistUseragent::Match.new(browsers, request.user_agent)
matcher.browser? && matcher.version?(allow_higher: true) 
```

Enter fullscreen mode Exit fullscreen mode

或者使用 [faraday-http-cache](https://github.com/plataformatec/faraday-http-cache) 缓存 http 请求的结果。它将强制每个 Rails 应用程序实例只发出一个请求:

```
# caches http response locally with etag
http_client = Faraday.new do |builder|
  builder.use Faraday::HttpCache, store: Rails.cache
  builder.adapter Faraday.default_adapter
end

browsers = JSON.parse(
  http_client.get('http://frontend-domain.local/browsers.json').body
)
... 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。该解决方案将在前端存储库中使用一个`browserslist.rc`配置，该配置将在后端自动共享。

更多关于`browserslist_useragent`宝石的细节，你可以在这里找到[。](https://github.com/browserslist/browserslist-useragent-ruby)

感谢阅读！