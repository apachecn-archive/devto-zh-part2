# 你可能不知道的 3 个秘诀

> 原文：<https://dev.to/kevcui/3-mitmproxy-tips-you-might-not-know-about-5dbg>

[![coverimage](img/85f157cf0757d694460a6bae64b617f4.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--xacUQW4h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AaleslIm6g3NqRL1xLQ3w5g.jpeg) <sup>*照片由[阿什巴兹](https://unsplash.com/photos/betmVWGYcLY)*</sup> 

mitmproxy 是一个强大的代理工具，用于调试网络请求和响应。这是我最喜欢的代理工具，并成为我日常工作的必备工具之一。我想和你分享 3 个你可能不知道的秘诀。

首先，一个小清单:

*   你知道 mitmproxy 是怎么工作的吗？
*   你知道如何安装 mitmproxy 吗？
*   你知道如何在客户端安装 CA 证书吗？

如果这些问题对你来说太简单了，而你的答案是“是的！是啊！是啊！”，然后继续阅读。否则，请点击并阅读上面的链接，稍后再见。

### 技巧 1:自定义配置和按键绑定

mitmproxy 的一个优点是高度可定制。设置一次配置，每次 mitmproxy 启动时自动加载它们，这是非常重要的。所有可用的便利选项**都在 [mitmproxy 文件](https://docs.mitmproxy.org/stable/concepts-options/)中。这是我的`~/.mitmproxy/config.yaml` :** 

```
console_palette: "dark"
console_palette_transparent: True
console_mouse: False
console_focus_follow: True
ignore_hosts: [] 
```

Enter fullscreen mode Exit fullscreen mode

由于我使用的是 [colemak 键盘布局](https://dev.to/kevcui/one-week-with-colemak-482c-temp-slug-2571522)， *j* 和 *k* 键是相反的，用于在 mitmproxy 中上下导航。我必须改变这两个键，使导航顺畅。以前，要更改旧版本中的键绑定，我必须修改`defaultkeys.py`中的代码。从 [mitmproxy v4.0](https://mitmproxy.org/posts/releases/mitmproxy4/) 开始，终于正式支持自定义键绑定了！以下是我在`~/.mitmproxy/keys.yaml` :
中的按键绑定

```
-
  key: j
  ctx: ["global"]
  cmd: console.nav.up
-
  key: k
  ctx: ["global"]
  cmd: console.nav.down
-
  key: l
  ctx: ["flowlist"]
  cmd: console.nav.select 
```

Enter fullscreen mode Exit fullscreen mode

要了解在 **cmd** 字段中使用哪个**命令**，请在 mitmproxy 中键入`K` (shift + k)。将显示当前键绑定列表。关于密钥绑定配置的更多信息在 [mitmproxy 文档](https://docs.mitmproxy.org/stable/tools-mitmproxy/)中。

### 提示 2:应用过滤器时了解客户端连接状态

我经常遇到这种情况:当我预先应用了所有过滤器，然后试图将一个客户端连接到 mitmproxy 时。但是，流列表中没有显示任何内容。我如何知道是因为*客户端没有成功连接到代理*还是*没有请求匹配过滤器*？

一种常见但不太好的方法:嗯，我必须移除过滤器才能看到所有请求。如果我可以看到来自客户端的请求，那么我将再次应用过滤器…

一种简单的方法是在过滤器中添加`| .*`，这将显示所有传入的请求。是的，正则表达式魔术:

[打开](https://kevcui.github.io/videos/mitmproxy-filter-on.svg)上的滤色镜

另一个简单的方法是简单地输入`E` (shift+e)，这将打开事件日志。它显示客户端连接状态。使用`q`退出事件视图:

[促销活动视图](https://kevcui.github.io/videos/mitmproxy-events-view.svg)

### 技巧 3:使用 mitmproxy 作为模拟服务器

mitmproxy 的杀手级特性是脚本。使用 python 脚本，所有的请求和响应都是完全可控的，并且可以动态地改变。除此之外，mitmproxy 非常健壮，可以处理同时连接到一台主机的多个客户端。这些特性使 mitmproxy 成为一个不错的模拟服务器。因此，我大量使用 mitmproxy 作为模拟服务器[。](https://dev.to/kevcui/bad-use-of-a-good-tool-4ja7-temp-slug-6954387)

例如，以重写和嘲讽响应体为目的的脚本， [mitm-rewrite](https://github.com/KevCui/mitm-scripts/tree/master/mitm-rewrite) :

*   地图`http://example.com/pass`到`test_pass.json` *(左下终端)*
*   在`test_pass.json` *(右下终端)*中创建模拟响应数据
*   在客户端访问`http://example.com/pass`
*   初始响应现在被`test_pass.json` *(顶端终端)*中的响应所取代

[![mitmproxy mock server picture](img/3b7e9d70f6fe09ad704bbeb788002ffe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Rh3qMV5K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/KevCui/mitm-scripts/master/screenshot/mitm-rewrite-example.jpg)

如果你想知道更多有用的脚本，我有一个 [mitm-scripts 集合](https://github.com/KevCui/mitm-scripts)可以查看。如果你想制作自己的脚本，这里有[一些例子](https://github.com/mitmproxy/mitmproxy/tree/master/examples/simple)。

* * *

还没试过 mitmproxy？试一试。😉
知道其他 mitmproxy 的招数吗？告诉我更多。😍**