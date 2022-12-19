# 使用 checkoutpage.co 给 Hugo 网站添加一个普通的 JavaScript 付费墙

> 原文：<https://dev.to/hugo__df/add-a-vanilla-javascript-paywall-to-a-hugo-site-using-checkoutpage-co-2jcn>

我是如何使用 checkoutpage.co 和少量普通的 JavaScript 为 ES6 备忘单创建购买机制的。

严格来说,*不是*付费墙，它完全是客户端的，不会进行太多检查。

原则上，你可以用一点 CSS 魔法来显示所有的东西。您还可以读取源代码，并对适用于该页面的查询参数进行逆向工程。

你也可以伪装成谷歌机器人(或其他爬虫),跳过付费覆盖。如果有人共享了包含“付费”查询参数的链接，那么他们共享的任何人都可以免费看到它。

我不认为人们会花力气去做上面的逆向工程，所以我将向所有愿意听我如何实现它的人展示一下。

如果你喜欢免费获取备忘单，但也想支持我的工作，你可以[给我买杯咖啡](https://checkoutpage.co/checkout/hugo/consulting-fee)或者干脆[订阅我的邮件列表](https://buttondown.email/hugo)。

目录:

## 设置内容隐藏

我使用下面的 CSS:

```
.site-wrapper.js-site-wrapper--no-scroll {
  top: 0;
  left: 0;
  width: 100%;
  z-index: 1;
  position: fixed;
}

.js-site-wrapper--no-scroll .post-full__table-of-contents {
  height: 100%;
  overflow: hidden;
} 
```

如果你的内容已经在一个`relative`、`absolute`或`fixed`位置容器中，这就不起作用了，但是对我来说这很有效🤷‍♀️.

相应的 JS 代码非常简单:

```
<script>
  (function (document) {
    const SITE_WRAPPER_SELECTOR = '.js-site-wrapper';
    const SITE_WRAPPER_NO_SCROLL_CLASS = 'js-site-wrapper--no-scroll';

    $siteWrapper = document.querySelector(SITE_WRAPPER_SELECTOR);
    function didBuy() {
      return false;
    }
    function init() {
      if (!didBuy()) {
        // disable scroll/hide all the things
        $siteWrapper.classList.add(SITE_WRAPPER_NO_SCROLL_CLASS);
      }
    }
    init();
  })(document);
</script> 
```

一切都被包装在一个 IIFE(立即调用函数表达式)中，以显得既漂亮又老派(并且不污染全局名称空间)。我们将文档传递给 life(`})(document);`)，并将其定义为一个参数(`function (document)`)。

我喜欢将选择器和类设置为常量，并用`$`如`$siteWrapper`作为元素前缀，这又有点像 jQuery 时代的倒退。

## 爬虫或旧浏览器时只显示一切

这是受 [Swizec 的 ES6 cheatsheet 来源](https://github.com/Swizec/es6-cheatsheet/blob/master/src/pages/index.js#L27-L59)的启发。

我们想显示一切(没有`no-scroll`类)爬虫。

我们从添加一个来自 observablehq.com/@hugodf/crawler-regex 的爬虫正则表达式模式开始，它从 github.com/monperrus/crawler-user-agents 的 T2 获取并连接各个模式。

我们把它变成一个完全成熟的 RegEx 对象，不关心大小写(因此有了`'i'`标志)。

在`didBuy`中的新代码仍然不是最复杂的:如果我们没有一个`navigator`对象，只需要 bail(即。假装这个人买了它)，因为它可能是一个旧的浏览器。如果我们走到这一步，检查用户代理对爬虫正则表达式，显示一切，如果它是谷歌机器人或另一个爬虫。

```
<script>
  (function (document, navigator) {
    // Get from https://beta.observablehq.com/@hugodf/crawler-regex
    const crawlerPattern = "REPLACE_ME";
    const crawlerRegEx = new RegExp(crawlerPattern, 'i');
    const SITE_WRAPPER_SELECTOR = '.js-site-wrapper';
    const SITE_WRAPPER_NO_SCROLL_CLASS = 'js-site-wrapper--no-scroll';

    $siteWrapper = document.querySelector(SITE_WRAPPER_SELECTOR);

    function isCrawler(userAgent) {
      return crawlerRegEx.test(userAgent);
    }

    function didBuy() {
      if (typeof navigator === "undefined" || typeof window === "undefined") {
        return true;
      }
      if (isCrawler(navigator.userAgent)) {
        return true;
      }
      return false;
    }
    function init() {
      if (!didBuy()) {
        // disable scroll/hide all the things
        $siteWrapper.classList.add(SITE_WRAPPER_NO_SCROLL_CLASS);
      }
    }
    init();
  })(document, navigator);
</script> 
```

## 设置结账页面

[checkoutpage.co](https://checkoutpage.co):“出售你的产品和服务的结帐页面和表格。分分钟安装，无需编码”。这是在你的网站上添加付费页面的一个简单而又棒的方法。它只是与你的 Stripe 帐户集成，并给你提供指向的 URL。

让我们设置它，创建一个帐户，将其链接到 Stripe，创建一个新页面。填写你觉得需要的任何字段。为了让[checkoutpage.co](https://checkoutpage.co)使用我们的客户端付费墙代码，我们必须做几件事。

“返回 URL”(页面→设置→返回 URL)应该有一个查询参数，这将使我们知道有人已经支付，就像在 URL 后面添加`?key=has-paid`一样简单。

您的完整 URL 应该采用以下形式:`https://your-domain.tld/path/to/cheatsheet/page?key=has-paid`。

我们应该添加一个电子邮件收集字段，以便我们可以发送一封确认电子邮件。让我们在页面→字段→添加具有以下数据的字段:

*   标签:电子邮件
*   数据类型:客户电子邮件
*   必填:真

如果我们希望收集电子邮件地址添加到邮件列表中，并且我们希望符合 GDPR 标准，我们需要一个选择加入字段。

为此，请转到页面→字段→添加字段，并使用以下选项创建一个新字段:-标签:“我希望接收 Hugo 时事通讯的代码”-数据类型:未选中(自由文本字段)-占位符:“否”，这很重要，并使其成为可选字段-必填:false

设置一个自定义的“确认消息”电子邮件，这样用户可以一次性付款，并继续访问备忘单(例如，从不同的设备)。在页面→设置→确认信息。

您可以使用相同的查询参数`?key=has-paid`或不同的参数。这是我的确认邮件:

```
Hey there 👋,

Thanks for buying the Sequelize ES6 cheat sheet.

This email confirms your payment. 

You can access the Sequelize ES6 cheat sheet using this link: https://codewithhugo.com/guides/sequelize-cheatsheet?key=list.

Payment details

Paid for: Sequelize ES6 Cheatsheet 
Paid to: Hugo 
Amount: $1.99

If you've opted in, you'll be added to the Code with Hugo newsletter. You can easily unsubscribe from the mailing list by clicking the unsubscribe link at the bottom of each email. 
```

当确认邮件发送后，Checkoutpage 将在我们的消息后添加一些额外的文本。

## 添加指向 checkoutpage.co 的按钮

我把这个作为实现细节，因为 Sander 已经用他的[购买按钮](https://checkoutpage.co/buy-buttons/)做了很棒的工作。

**TL；你实际上只需要指向你的 checkoutpage 页面的链接🙂**

## 检查查询中允许的键值集

现在我们要设置一些值，这些值将是有效的“付费”参数和当前页面的查询参数。

这意味着创建一个`getQueryObject`函数，它接受`window.location.search`，移除`?`，接受每个`&key=value`，将它们转换成一个类似`{ key: "value" }`的对象。

然后，我们将这个`queryObject`与我们的`PAID_QUERY_PARAMS`进行匹配。如果对于一个`PAID_QUERY_PARAMS`对象，所有的查询关键字都匹配这些值，那么用户已经付费了。这是完成这项工作的代码片段:

```
const bought = PAID_QUERY_PARAMS.some(function (queryParams) {
  return Object.entries(queryParams).every(function(keyValue) {
    const key = keyValue[0];
    const value = keyValue[1];
    return query[key] === value;
  });
}); 
```

为了与稍旧的浏览器半兼容，我没有使用箭头函数/析构，虽然这样做会使代码更可读，但在 ES6+中我会这样做:

```
const bought = PAID_QUERY_PARAMS.some((queryParams) =>
  Object.entries(queryParams).every(
    ([key, value]) => query[key] === value
  );
); 
```

内容如下:“如果有一个`PAID_QUERY_PARAMS`条目，其中对于每个键/值对，每个键/值对的查询值都与`PAID_QUERY_PARAMS`条目对匹配”

```
<script>
  (function (document, navigator, window) {
    const PAID_QUERY_PARAMS = [{ key: 'list' }, { key: 'has-paid' }];
    // Get from https://beta.observablehq.com/@hugodf/crawler-regex
    const crawlerPattern = "REPLACE_ME";
    const crawlerRegEx = new RegExp(crawlerPattern, 'i');
    const SITE_WRAPPER_SELECTOR = '.js-site-wrapper';
    const SITE_WRAPPER_NO_SCROLL_CLASS = 'js-site-wrapper--no-scroll';

    $siteWrapper = document.querySelector(SITE_WRAPPER_SELECTOR);

    function isCrawler(userAgent) {
      return crawlerRegEx.test(userAgent);
    }

    function getQueryObject(window) {
      return window.location.search
        .replace(/^\?/, "")
        .split('&')
        .map(function(keyValue) { return keyValue.split('='); })
        .reduce(function(acc, keyValue) {
          const k = keyValue[0];
          const v = keyValue[1];
          acc[k] = v;
          return acc;
        }, {});
    }

    function didBuy() {
      if (typeof navigator === "undefined" || typeof window === "undefined") {
        return true;
      }
      if (isCrawler(navigator.userAgent)) {
        return true;
      }

      const query = getQueryObject(window);
      const bought = PAID_QUERY_PARAMS.some(function (queryParams) {
        return Object.entries(queryParams).every(function(keyValue) {
          const key = keyValue[0];
          const value = keyValue[1];
          return query[key] === value;
        });
      });

      return bought;
    }
    function init() {
      if (!didBuy()) {
        // disable scroll/hide all the things
        $siteWrapper.classList.add(SITE_WRAPPER_NO_SCROLL_CLASS);
      }
    }
    init();
  })(document, navigator, window);
</script> 
```

## 持续到本地存储

我们希望清除查询参数，并在 localStorage 中存储用户为通过付费墙而支付的费用。为什么？这样用户可以从任何地方(可能通过站点导航或其他方式)导航到页面，并且仍然可以看到内容。

要做到这一点，我们需要两个额外的非业务逻辑的东西:一些我们可以检查的新常数:

```
const PAID_LOCALSTORAGE_KEY = 'MY_KEY';
const PAID_LOCALSTORAGE_VALUE = 'MY_SUPER_SECRET_VALUE'; 
```

和一个小小的包装器，因为为什么不:

```
const persist = {
  set: function(key, value) {
    return window.localStorage.setItem(key, value);
  },
  get: function(key) {
    return window.localStorage.getItem(key);
  }
} 
```

然后业务逻辑如下，我们要检查一个`HAS_PAID`是否已经被持久化在`didBuy`中，如果还没有被设置，就退回到检查查询对象。如果查询对象检查为已付费，则持久化它。即。在`didBuy` :

```
function didBuy() {
  // navigator and user agent (crawler) checks  
  // 1\. New PAID in persistence check
  if (persist.get(PAID_LOCALSTORAGE_KEY) === PAID_LOCALSTORAGE_VALUE) {
    return true;
  }

  // 2\. Existing "PAID" check
  const query = getQueryObject(window);
  const bought = PAID_QUERY_PARAMS.some(function (queryParams) {
    return Object.entries(queryParams).every(function(keyValue) {
      const key = keyValue[0];
      const value = keyValue[1];
      return query[key] === value;
    });
  });

  // 3\. New persist PAID
  if (bought) {
    persist.set(PAID_LOCALSTORAGE_KEY, PAID_LOCALSTORAGE_VALUE);
  }

  return bought;
} 
```

全文如下:

```
<script>
  (function (document, navigator, window) {
    const PAID_QUERY_PARAMS = [{ key: 'list' }, { key: 'has-paid' }];
    const PAID_LOCALSTORAGE_KEY = 'MY_KEY';
    const PAID_LOCALSTORAGE_VALUE = 'MY_SUPER_SECRET_VALUE';
    // Get from https://beta.observablehq.com/@hugodf/crawler-regex
    const crawlerPattern = "REPLACE_ME";
    const crawlerRegEx = new RegExp(crawlerPattern, 'i');
    const SITE_WRAPPER_SELECTOR = '.js-site-wrapper';
    const SITE_WRAPPER_NO_SCROLL_CLASS = 'js-site-wrapper--no-scroll';

    $siteWrapper = document.querySelector(SITE_WRAPPER_SELECTOR);

    const persist = {
      set: function(key, value) {
        return window.localStorage.setItem(key, value);
      },
      get: function(key) {
        return window.localStorage.getItem(key);
      }
    }

    function isCrawler(userAgent) {
      return crawlerRegEx.test(userAgent);
    }

    function getQueryObject(window) {
      return window.location.search
        .replace(/^\?/, "")
        .split('&')
        .map(function(keyValue) { return keyValue.split('='); })
        .reduce(function(acc, keyValue) {
          const k = keyValue[0];
          const v = keyValue[1];
          acc[k] = v;
          return acc;
        }, {});
    }

    function didBuy() {
      if (typeof navigator === "undefined" || typeof window === "undefined") {
        return true;
      }
      if (isCrawler(navigator.userAgent)) {
        return true;
      }

      if (persist.get(PAID_LOCALSTORAGE_KEY) === PAID_LOCALSTORAGE_VALUE) {
        return true;
      }

      const query = getQueryObject(window);
      const bought = PAID_QUERY_PARAMS.some(function (queryParams) {
        return Object.entries(queryParams).every(function(keyValue) {
          const key = keyValue[0];
          const value = keyValue[1];
          return query[key] === value;
        });
      });

      if (bought) {
        persist.set(PAID_LOCALSTORAGE_KEY, PAID_LOCALSTORAGE_VALUE);
      }

      return bought;
    }
    function init() {
      if (!didBuy()) {
        // disable scroll/hide all the things
        $siteWrapper.classList.add(SITE_WRAPPER_NO_SCROLL_CLASS);
      }
    }
    init();
  })(document, navigator, window);
</script> 
```

## 清除查询而不刷新页面

使用`.replaceState`删除查询参数而不刷新的 JS 代码片段是:

```
window.history.replaceState({}, document.title, window.location.pathname); 
```

所以我们只是把它填入`init` :

```
function init() {
  if(!didBuy()) {
    $siteWrapper.classList.add(SITE_WRAPPER_NO_SCROLL_CLASS);
  } else {
    window.history.replaceState({}, document.title, window.location.pathname);
  }
} 
```

因此，完整的脚本是:

```
<script>
  (function (document, navigator, window) {
    const PAID_QUERY_PARAMS = [{ key: 'list' }, { key: 'has-paid' }];
    const PAID_LOCALSTORAGE_KEY = 'MY_KEY';
    const PAID_LOCALSTORAGE_VALUE = 'MY_SUPER_SECRET_VALUE';
    // Get from https://beta.observablehq.com/@hugodf/crawler-regex
    const crawlerPattern = "REPLACE_ME";
    const crawlerRegEx = new RegExp(crawlerPattern, 'i');
    const SITE_WRAPPER_SELECTOR = '.js-site-wrapper';
    const SITE_WRAPPER_NO_SCROLL_CLASS = 'js-site-wrapper--no-scroll';

    $siteWrapper = document.querySelector(SITE_WRAPPER_SELECTOR);

    const persist = {
      set: function(key, value) {
        return window.localStorage.setItem(key, value);
      },
      get: function(key) {
        return window.localStorage.getItem(key);
      }
    }

    function isCrawler(userAgent) {
      return crawlerRegEx.test(userAgent);
    }

    function getQueryObject(window) {
      return window.location.search
        .replace(/^\?/, "")
        .split('&')
        .map(function(keyValue) { return keyValue.split('='); })
        .reduce(function(acc, keyValue) {
          const k = keyValue[0];
          const v = keyValue[1];
          acc[k] = v;
          return acc;
        }, {});
    }

    function didBuy() {
      if (typeof navigator === "undefined" || typeof window === "undefined") {
        return true;
      }
      if (isCrawler(navigator.userAgent)) {
        return true;
      }

      if (persist.get(PAID_LOCALSTORAGE_KEY) === PAID_LOCALSTORAGE_VALUE) {
        return true;
      }

      const query = getQueryObject(window);
      const bought = PAID_QUERY_PARAMS.some(function (queryParams) {
        return Object.entries(queryParams).every(function(keyValue) {
          const key = keyValue[0];
          const value = keyValue[1];
          return query[key] === value;
        });
      });

      if (bought) {
        persist.set(PAID_LOCALSTORAGE_KEY, PAID_LOCALSTORAGE_VALUE);
      }

      return bought;
    }
    function init() {
      if (!didBuy()) {
        // disable scroll/hide all the things
        $siteWrapper.classList.add(SITE_WRAPPER_NO_SCROLL_CLASS);
      } else {
        window.history.replaceState({}, document.title, window.location.pathname);
      }
    }
    init();
  })(document, navigator, window);
</script> 
```

## 我的网站上有完整的脚本

我用 polyfill.io 聚合填充`.some`、`.entries`和`.every`。我也有一个“购买覆盖”，我用 CSS 类打开它。剩下的都在文章里解释了。

在 [Sequelize ES6 Cheatsheet](https://codewithhugo.com/guides/sequelize-es6-cheatsheet/) 处查看它的运行情况。

如果你喜欢免费获取备忘单，但也想支持我的工作，你可以[给我买杯咖啡](https://checkoutpage.co/checkout/hugo/consulting-fee)或者干脆[订阅我的邮件列表](https://buttondown.email/hugo)。

```
<script src="https://cdn.polyfill.io/v2/polyfill.min.js?features=es6"></script>
<script>
  (function (document, navigator, window) {
    const PAID_QUERY_PARAMS = [{ key: 'list' }, { key: 'has-paid' }];
    const PAID_LOCALSTORAGE_KEY = 'MY_KEY';
    const PAID_LOCALSTORAGE_VALUE = 'MY_SUPER_SECRET_VALUE';
    // Get from https://beta.observablehq.com/@hugodf/crawler-regex
    const crawlerPattern = "REPLACE_ME";
    const crawlerRegEx = new RegExp(crawlerPattern, 'i');
    const SITE_WRAPPER_SELECTOR = '.js-site-wrapper';
    const SITE_WRAPPER_NO_SCROLL_CLASS = 'js-site-wrapper--no-scroll';
    const BUY_OVERLAY_SELECTOR = '.js-buy-overlay';
    const BUY_OVERLAY_ACTIVE_CLASS = 'js-buy-overlay--active';

    $siteWrapper = document.querySelector(SITE_WRAPPER_SELECTOR);
    $buyOverlay = document.querySelector(BUY_OVERLAY_SELECTOR);

    const persist = {
      set: function(key, value) {
        return window.localStorage.setItem(key, value);
      },
      get: function(key) {
        return window.localStorage.getItem(key);
      }
    }

    function isCrawler(userAgent) {
      return crawlerRegEx.test(userAgent);
    }

    function getQueryObject(window) {
      return window.location.search
        .replace(/^\?/, "")
        .split('&')
        .map(function(keyValue) { return keyValue.split('='); })
        .reduce(function(acc, keyValue) {
          const k = keyValue[0];
          const v = keyValue[1];
          acc[k] = v;
          return acc;
        }, {});
    }

    function didBuy () {
      if (typeof navigator === "undefined" || typeof window === "undefined") {
        return true;
      }
      if (isCrawler(navigator.userAgent)) {
        return true;
      }
      if (persist.get(PAID_LOCALSTORAGE_KEY) === PAID_LOCALSTORAGE_VALUE) {
        return true;
      }
      const query = getQueryObject(window);
      const bought = PAID_QUERY_PARAMS.some(function (queryParams) {
        return Object.entries(queryParams).every(function(keyValue) {
          const key = keyValue[0];
          const value = keyValue[1];
          return query[key] === value;
        });
      });
      if (bought) {
        persist.set(PAID_LOCALSTORAGE_KEY, PAID_LOCALSTORAGE_VALUE);
      }
      return bought;
    }

    function init() {
      if(!didBuy()) {
        $siteWrapper.classList.add(SITE_WRAPPER_NO_SCROLL_CLASS);
        $buyOverlay.classList.add(BUY_OVERLAY_ACTIVE_CLASS);
      } else {
        window.history.replaceState({}, document.title, window.location.pathname);
      }
    }
    init();
  }) (document, navigator, window);
</script> 
```

塞缪尔·泽勒