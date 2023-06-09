# 用 Python 编写无阻塞 web 抓取器的 5 个策略

> 原文：<https://dev.to/kadnan/5-strategies-to-write-unblock-able-web-scrapers-in-python-354i>

[![HTML](img/ec4a7d6da405dc2887d0f9efbdcef556.png)T2】](http://blog.adnansiddiqi.me/wp-content/uploads/2017/02/HTML.jpg)

阅读我在 [scraping 系列](http://blog.adnansiddiqi.me/tag/scraping/)中的帖子的人经常联系我，想知道他们如何编写不会被阻塞的 scrapers。编写一个永远不会被阻塞的抓取器是非常困难的，但是是的，你可以通过实施一些策略来延长你的 web 抓取器的寿命。今天我将讨论它们。

## 用户代理

您需要做的第一件事是设置*用户代理。*用户代理是一个代表用户工作的工具，它告诉服务器用户正在使用哪个浏览器访问网站。如果没有设置*用户代理*，很多网站都不会让你查看内容。如果你正在使用`requests`库，你可以做如下的事情:

```
headers = { 'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10\_11\_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36', } r = requests.get('example.com',headers=headers) 
```

Enter fullscreen mode Exit fullscreen mode

你可以通过在谷歌搜索栏中输入*什么是我的用户代理*得到一个用户代理字符串，它会返回给你你自己的用户代理。

好吧，你已经设置了一个用户代理，但是如何让它更真实呢？最好的方法是从文本文件中随机选择一个用户代理，从数据库中选择一个 Python 或`list`。 [Udger](https://udger.com/resources/ua-list) 分享了一份 UAs w.r.t 浏览器的庞大列表。例如，Chrome 看起来像[这个](https://udger.com/resources/ua-list/browser-detail?browser=Chrome)，Firefox 看起来像[这个](https://udger.com/resources/ua-list/browser-detail?browser=FIREFOX)。现在让我们创建一个函数，它将返回一个您可以在`requests`
中使用的随机 UA

```
import numpy as np def get\_random\_ua(): random\_ua = '' ua\_file = 'ua\_file.txt' try: with open(ua\_file) as f: lines = f.readlines() if len(lines) \> 0: prng = np.random.RandomState() index = prng.permutation(len(lines) - 1) idx = np.asarray(index, dtype=np.integer)[0] random\_ua = lines[int(idx)] except Exception as ex: print('Exception in random\_ua') print(str(ex)) finally: return random\_ua 
```

Enter fullscreen mode Exit fullscreen mode

从我上面分享的网站来看，`ua_file.txt`每行包含一个 UA。函数`get_random_ua`将总是从文本文件中返回一个唯一的 UA。你现在可以像这样调用这个函数:

```
user\_agent = get\_random\_ua() headers = { 'user-agent': user\_agent, } r = requests.get('example.com',headers=headers) 
```

Enter fullscreen mode Exit fullscreen mode

## [参考文献](#referers)

接下来你要设置的是推荐人。一般的经验法则是，如果它是一个列表页面或主页，那么你可以设置该国家的谷歌主页 URL。例如，如果我正在抓取`olx.com.pk`，那么我将设置`https://google.com.pk`而不是`https://google.ca`

如果你要抓取单个产品页面，那么你可以在 referrer 中设置相关的类别 URL，或者找到你要抓取的域名的[反向链接](https://en.wikipedia.org/wiki/Backlink)。我通常使用 SEMRush 来达到这个目的。对于链接`https://www.olx.com.pk/furniture-home-decor/categories/`, SEM rush 返回如下内容:

[![SEM Rush for Backlinks](img/1e9f78a372eaf2b737b4e6b8a2242cd9.png)T2】](http://blog.adnansiddiqi.me/wp-content/uploads/2018/05/Screen-Shot-2018-05-02-at-1.47.55-PM.png)

如果你点击查看图像的大版本，你可以看到一些链接，指向我需要的类别。一旦你收集了所有这些真正的反向链接，你就可以通过复制`get_random_ua()` return random referrer 里面的逻辑来使用它们。`headers`现在将变成如下所示:

```
headers = { 'user-agent': user\_agent, 'referer':referer } 
```

Enter fullscreen mode Exit fullscreen mode

## 代理 IP

我怎么强调都不为过。看，如果你进入严重的业务，那么你必须使用多个代理 IP，以避免封锁。大多数网站基于服务器或主机提供商的静态 IP 来阻止爬虫。这些网站使用智能工具来找出某个 IP 或某个 IP 池的模式，并简单地阻止它们。这也是为什么建议买几个 IP，5o-100 最少避免堵。有各种各样的服务，但我对着色器很满意，现在叫做 [OxyLabs](https://oxylabs.io/pricing) 。它们很贵，但是服务质量也很好。请确保当您订购多个 IP 时，要求随机的 IP，或者至少它们不遵循某种模式，如`1.2.3.4`到`1.2.3.100.`，网站管理员将简单地设置一个规则来阻止所有 IP 所属的 IP。`1.2.3.*.`就这么简单。

如果您正在使用`requests`，您可以如下使用它:

`r = requests.get('example.com',headers=headers,proxies={'https': proxy_url})`

如果你正在使用 Selenium，并且想在 Selenium 上使用代理 IPs，那就有点棘手了。

```
r = requests.get('example.com',headers=headers,proxies={'https': proxy\_url}) proxy = get\_random\_proxy().replace('\n', '') service\_args = ['--proxy={0}'.format(proxy), '--proxy-type=http', '--proxy-auth=user:path'] print('Processing..' + url) driver = webdriver.PhantomJS(service\_args=service\_args) 
```

Enter fullscreen mode Exit fullscreen mode

不用说，`get_random_proxy()`是返回唯一随机代理的方法，就像上面你得到的唯一随机 UAs 和 Referer 一样。

你也可以想出一个系统，你可以设置一个 IP 每天或每小时访问网站的频率，如果超过这个频率，它就会被关进笼子，直到第二天。我工作的公司设计了一个系统，我不仅设置了 IP 的频率，还记录了哪个 IP 被阻止。最后，我只是请求代理服务提供商只替换那些代理。因为这已经超出了这篇文章的范围，所以我就不赘述了。

## 请求标题

到目前为止，你已经实现的东西是好的，但仍然有一些狡猾的网站要求你做更多的工作，当你访问页面时，他们会寻找特定的请求标题条目，如果没有找到特定的标题，他们会阻止内容或欺骗内容。模仿你将要在网站上提出的整个请求是非常容易的。例如，您将要访问某个 Craigslist URL，并想知道正在请求哪些标题。进入 Chrome/Firefox，检查正在访问的页面，你应该能看到如下内容: [![Craigslist Requests Headers](img/9a6ff20a60229c4842915c86f9fbf589.png)](http://blog.adnansiddiqi.me/wp-content/uploads/2018/05/Screen-Shot-2018-05-02-at-2.27.20-PM.png)

如果你点击图像和查看，你会发现除了裁判和用户代理的各种条目。您可以全部实现，也可以逐个实现并测试。不管我要访问哪个网站，我总是设置这些条目。确保你不只是在不同的站点间复制/粘贴，因为这些条目经常会因站点而异:

```
headers = { 'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10\_11\_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36', 'referrer': 'https://google.com', 'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,\*/\*;q=0.8', 'Accept-Encoding': 'gzip, deflate, br', 'Accept-Language': 'en-US,en;q=0.9', 'Pragma': 'no-cache', } 
```

Enter fullscreen mode Exit fullscreen mode

## 延误

在请求之间放置一些延迟总是好的。为此，我使用了`numpy.random.choice()`来传递我想要延迟服务的随机数列表:

```
delays = [7, 4, 6, 2, 10, 19] delay = np.random.choice(delays) time.sleep(delay) 
```

Enter fullscreen mode Exit fullscreen mode

如果您还没有使用`numpy`库，您也可以使用`random.choice`来达到同样的目的。

如果你真的很急，那么你可以并行执行 URL，我已经在这里解释了。

## 结论

网页抓取器阻塞的不确定性永远不会为零，但你总是可以采取一些措施来避免它。我讨论了一些你应该在你的网络爬虫中以某种方式实现的策略。

如果你知道其他的策略或技巧，请在评论中分享给我。一如既往，我们非常欢迎您的反馈。

编写 scrapers 是一个有趣的旅程，但如果网站屏蔽了你的 IP，你可能会碰壁。作为个人，你也买不起昂贵的代理。[*Scraper API*](https://www.scraperapi.com/pricing?fp_ref=adnan98)*为您提供了一个负担得起且易于使用的 API，让您轻松地抓取网站。你不需要担心被屏蔽，因为 Scraper API 默认使用代理访问网站。除此之外，您也不需要担心 Selenium，因为 Scraper API 也提供了无头浏览器的功能。我也写过* [*一帖*](http://blog.adnansiddiqi.me/create-your-first-web-scraper-with-scraper-api-and-python/?utm_source=blog_scrapers_post&utm_medium=blog_adnan&utm_campaign=c_blog_scrapers_post) *关于如何使用。*T19】

*点击此处报名我的* [*推荐链接*](https://www.scraperapi.com/pricing?fp_ref=adnan98) *或输入促销代码****scrap 156980****，即可享受九折优惠。如果你没有得到折扣，那么就在我的网站上通过电子邮件让我知道，我一定会帮助你。*