# 又快又脏。小型个人网站的 htaccess

> 原文：<https://dev.to/thejessleigh/quick-and-dirty-htaccess-for-small-personal-sites-2cfn>

我在一个共享的 Dreamhost 实例上托管我的个人网站。我喜欢它，因为它相对便宜，我的流量低，我没有做什么特别复杂的事情。通过托管在虚拟专用服务器(VPS)或专用服务器上拥有完全的根访问权限对我正在做的事情没有意义。然而，我仍然希望对我的网站提供什么以及如何提供有一些服务器级别的控制。这就是`.htaccess`的用武之地。

> 更新:截至 2019 年初，我的个人网站在数字海洋上使用 nginx 设置，而不是在 Dreamhost 上使用 apache 设置。到目前为止，我更喜欢数字海洋的体验，不管这有什么价值。:)

# 什么是`.htaccess`？

许多主机选项在后端使用 Apache HTTP 服务器为您的网站提供服务。从最基本的层面上来说，你的网站是电脑上某个地方的文件集合。您希望其他计算机上的人能够通过 internet 访问这些文件。web 服务器是接收请求、找到正确文件并将内容返回给客户端的软件。`.htaccess`是 Apache 使用的一个文件，可以让您更好地控制这些请求的处理方式。`.htaccess`文件为其所在目录中的所有文件请求设置配置。

## 我为什么要在乎？

你的网站将能够在没有一个`.htaccess`文件来微调你的服务器配置的情况下运行。但是利用它的特性可以实现表面上的改进，比如在你的 url 中强制添加或删除`www`,或者在链接到你网站的页面时消除对`.html`文件扩展名的需要。

它还可以提供安全优势。如果您使用 git 来控制您的站点部署，您可能会不小心将凭据或敏感信息暴露给开放的 internet。你也可以强迫人们在连接到你的网站时使用`https`,这不仅是安全方面的正确举措，也有助于提升你的 SEO 排名。

# 局限性和弊端。htaccess(和共享主机)

一些人很快指出，使用`.htaccess`进行配置而不是使用主 Apache 配置[会导致性能下降](https://www.danielmorell.com/guides/htaccess-seo/basics/dont-use-htaccess-unless-you-must)。当您的服务器负载较高时，这种性能损失会变得更严重。一般来说，如果你在一个共享的主机环境中，你可能最依赖于`.htaccess`。如果你只是想托管一个小型投资组合网站或一个玩具应用程序，那么一头扎进云计算，获得 root 访问权限，并成为自己的 Apache 系统管理员可能是多余的。

如果你正在吸引大量的流量和/或在你的服务器上有高负载的困难，你可能*应该*转移到一个 VPS，在那里你有根访问和使用主服务器配置。如果这对你来说是一个问题，那么你可能已经超越了一个人的操作，应该找个人来帮助提高网站的可靠性。但是如果你只是运行一个小的，大部分是静态的站点，就不值得担心使用`.htaccess`会导致性能下降。

`.htaccess`在范围上比`httpd.conf`中的主 Apache 配置更加有限。你不能用`.htaccess`做目录级别的操作。如果你正在做大量的工作，你想控制对你的网站的目录级访问，一个更健壮的主机选项是有意义的。但是，除非你在一个复杂的环境中工作，并且希望做大量的站点可靠性微调，否则没有 root 访问权限也不会有问题。

# 常见用例备忘单

这里有一些你可能想使用`.htaccess`的东西。请随意从这里直接复制/粘贴，因为我已经从我的前辈那里复制/粘贴了。

许多这些片段需要你打开`RewriteEngine`。对于每个文件，您只需要这样做一次，并且可以将所有的重写请求放在它下面。为了便于复制/粘贴，我在所有相关的片段中都包含了`RewriteEngine On`。确保不要复制超过一次。

## 保护自己免受带宽窃取

带宽盗窃，或热链接，是指链接到别人网站上的图片，并在你自己的网站上显示。这将使用您链接到的服务器的带宽，而不是您自己的带宽。挺没礼貌的！

然而，你可以保护你的网站不被热链接。

```
RewriteEngine On
RewriteCond %{HTTP_REFERER} !^$
RewriteCond %{HTTP_REFERER} !^http(s)?://(.+\.)?mysite\.com [NC]
RewriteRule .*\.(jp(e)?g|gif|bmp|png)$ https://imgur.com/ZtXiCBw.gif [L,R] 
```

Enter fullscreen mode Exit fullscreen mode

(确保用您的实际 url 替换`mysite\.com`)

上面的代码检查请求来自哪里，或者来自**引用者**。如果引用者不是你的站点(或者是空的),它将显示你引用的任何图片。

上述链接重定向到 imgur 上的以下图像:

<figure>

[![Animated radioactivity symbol with text reading "WARNING DO NOT HOTLINK IMAGES AND STEAL BANDWIDTH beneath](../Images/b95f648b9794d03b59ddc5d09fa11f8a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IJfaoeOQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://imgur.com/ZtXiCBw.gif)

<figcaption>Anyone who runs into this enough will learn quickly to stop stealing bandwidth from you :)</figcaption>

</figure>

## SEO 改进

### 将旧页面重定向到新位置

使用`.htaccess`可以做的最基本也是最有用的事情之一就是将一个曾经存在于某个位置的页面重定向到一个新的 URL。这对于 SEO 来说是极其重要的。如果你写了一篇很受欢迎的博客文章，说你要搬到别处去，你不想仅仅因为你的内容改变了位置就失去链接权益或者一个链接对搜索引擎的影响。

一般来说，您可能希望使用 301 或 302 重定向。301 是永久重定向。302 重定向是暂时的，不会保护你的链接权益。然而，当处理重定向时，你应该总是首先使用 302 并测试你的改变。如果一切看起来都很好，那就换成 301。您应该这样做，这样您就不会意外地将链接资产从旧位置转发到错误的位置。

当格式化重定向 stament 时，参数的顺序如下

*   状态代码(301、302、404 等。)
*   旧文件位置
*   新文件位置

```
Redirect 302 /2016/08/02/rad-old-blog-post/ https://newdomain.party/rad-old-blog-post/
Redirect 301 /menu/dessert.html https://new-restaurant-website.supply/menu/dessert/ 
```

Enter fullscreen mode Exit fullscreen mode

### 强制执行`https`

一些主机选项允许你在人们连接到你的网站时强制`https`。其他人只是允许使用`https`,而没有严格执行它的使用。使用`https`对于安全和 SEO 来说[是非常重要的](https://developers.google.com/web/fundamentals/security/encrypt-in-transit/why-https)，所以如果你的主机提供商不执行`https`连接，*你*应该执行。

您可以用下面的代码片段将所有不安全的请求重写为`https`请求。

```
RewriteEngine On
RewriteCond %{HTTPS} off
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301] 
```

Enter fullscreen mode Exit fullscreen mode

## 整容改进

### 不要求`.html`

没有什么比把`.html`挂在你的 url 末尾更让人觉得俗气和过时了。我的意思是，我主要是在开玩笑，但我真的不喜欢有那个文件扩展名晃来晃去。幸运的是，你可以解决这个问题！

```
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME}\.html -f
RewriteRule ^(.*)$ $1.html 
```

Enter fullscreen mode Exit fullscreen mode

该条件检查您发出的请求是否针对项目中的有效目录。我们在这里检查目录，而不是文件，因为请求将类似于`mysite.com/info`，它看起来像一个目录名。如果它找不到有效的目录，那么它会检查是否有一个名为`info.html`的有效文件。如果有，重写请求以提供来自`mysite.com/info.html`的内容。

您可以使用同样的方法删除其他文件扩展名，如`.htm`或`.php`。

### 强制执行(或强行删除)`www`

力 www:

```
RewriteEngine On
RewriteCond %{HTTP_HOST} ^example\.com [NC]
RewriteRule ^(.*)$ http://www.example.com/$1 [L,R=301,NC] 
```

Enter fullscreen mode Exit fullscreen mode

移除 www:

```
RewriteEngine On
RewriteCond %{HTTP_HOST} ^www\.example\.com [NC]
RewriteRule ^(.*)$ http://example.com/$1 [L,R=301] 
```

Enter fullscreen mode Exit fullscreen mode

这里的[NC]标志代表不区分大小写，所以不必担心区分大小写。

[L]标志代表“最后”，意味着如果该条件为真，则执行`RewriteRule`并且不尝试匹配其他`RewriteConds`或对请求执行其他操作。

[R]标志代表“重定向”。您可以向 R 传递一个选项来设置重定向的 HTTP 状态代码。示例[R=302]或[R=404]。

## 安全改进

### 隐藏`.git`目录

版本控制非常棒，即使对于静态网站也是如此。它可以帮助您轻松地恢复到以前的版本，并让您回顾代码的发展。然而，通过 git push/pull 控制站点更新可能会导致安全漏洞。访问者可以通过他们的浏览器公开访问你的`.git`目录，浏览你的整个回购，包括你可能存储在那里的任何秘密或密钥。如果你托管的是比静态个人网站更复杂的东西，它也允许任何人查看你的源代码并复制你的作品，即使你想保密。

幸运的是，您可以重定向任何请求您的`.git`目录的尝试。

```
RedirectMatch 404 /\.git 
```

Enter fullscreen mode Exit fullscreen mode

简单！你可以在这里回复任何你喜欢的状态。我个人喜欢使用 404，因为服务器上似乎没有这样的目录，可能会阻止进一步访问类似的文件。然而，403 在这里也是有意义的。这取决于你。

### 限制访问敏感文件

您可能不希望随机访问您网站的访问者访问您的点文件或其他敏感信息。您可以禁止任何人访问特定文件，也可以在`.htaccess`中允许/阻止某些 IP 地址。

注意:您不能使用`.htaccess`来限制或允许对*目录*级别的访问。这是需要进入`httpd.conf`并需要 root 访问的操作之一。

```
# Don't allow anyone to see my .htaccess file
<Files .htaccess>
  Order allow,deny
  Deny from all
</Files> 
```

Enter fullscreen mode Exit fullscreen mode

`Order`关键字在这里有点棘手，所以我们来浏览一下。`Order`接受两个参数之一。不是`allow,deny`就是`deny,allow`。确保逗号和参数中的第二个单词之间没有空格。如果你在那里放一个空格，它会导致所有对你的站点的请求达到 500。这个参数告诉 Apache 应该以什么顺序处理 allow 和 deny 语句。

`allow,deny`表示首先处理任何`Allow`语句，最后处理`Deny`语句。最近的语句将覆盖它之前的任何语句。所以，在上面的例子中，如果你想允许你的 IP 地址，并且只允许*你的 IP 地址通过 curl 或浏览器查看`.htaccess`，你可以包含一个类似`Allow from 198.51.100.254`的声明。但是，因为您的配置指示服务器首先处理所有的`Allow`语句，然后覆盖它们，所以您仍然会被拒绝访问，因为`Deny from all`是最近的订单。*

相反，`deny,allow`首先处理所有的`Deny`语句，然后用`Allow`覆盖。如果你要做比拒绝或允许任何人访问更复杂的事情，确保你关注你的`Order`参数，并确保测试它。

您还可以使用此语法来限制对特定 HTTP 方法的访问。例如，如果您愿意，您可以将`POST`和`PUT`操作限制在您的局域网(LAN)内。

```
<Limit POST PUT>
  Order deny,allow
  Deny from all
  Allow from 198.51.100.254
</Limit> 
```

Enter fullscreen mode Exit fullscreen mode

# 调试等一般提示

确保您知道日志文件在哪里。即使您没有 root 访问权限，也应该能够看到您站点的访问和错误日志。错误日志对于调试您在尝试设置配置时遇到的任何意外结果或错误非常有用。

## 错误的标志分隔符

如果您的错误消息包含短语`bad flag delimiter`，您可能在您的`RewriteRule`标志中包含了一个额外的空格。标志是逗号分隔，而不是逗号+空格分隔，所以请确保您的标志格式如下:`[L,R=301,NC]`，而不是这样:`[L, R=301, NC]`

## 这里有些操作不允许

如果您的错误消息包含短语`not allowed here`，您可能正在尝试做一些需要在`httpd.conf`主配置文件中完成的事情。你需要想出一些其他的方法来实现你的目标，或者转移到一个你有根权限并且可以修改`httpd.conf`的解决方案。

# 附加资源

*   github 上的 htaccess 片段牛逼列表
*   [Apache HTTP 服务器教程](https://httpd.apache.org/docs/current/howto/htaccess.html) : `.htaccess`文件
*   [Apache 配置文件文档](https://httpd.apache.org/docs/2.4/configuring.html)

你在你的网站上用`.htaccess`做什么？有没有什么事情是你想和`.htaccess`一起做却一直想不通的？请在评论中告诉我！