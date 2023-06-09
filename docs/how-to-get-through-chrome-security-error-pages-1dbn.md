# 如何通过 Chrome 安全错误页面

> 原文：<https://dev.to/m1guelpf/how-to-get-through-chrome-security-error-pages-1dbn>

*原载于[我的博客](https://miguelpiedrafita.com/chrome-thisisunsafe/)*

如果你使用 Chrome，你很可能见过上面的图片。例如，当一个页面在[谷歌安全浏览](https://safebrowsing.google.com/)黑名单中，或者当它的 SSL 证书过期时，就会显示出来。通常情况下，您可以通过单击“*高级*”然后“*前往 whatever.com(不安全)】【T5”)来绕过此警告，但是，如果网站使用特殊技术(称为 [HSTS](https://really-simple-ssl.com/knowledge-base/what-does-hsts-mean/) )，上述方法将不起作用，您将看到以下消息:*

[![How to get through Chrome security error pages](img/3e9ce8b5439b9c7abc4959728cae521f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HOvBKPQK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miguelpiedrafita.com/conteimg/2018/10/Image-2018-10-14-at-2.57.16-PM.png)

我最近学会了一个简单的技巧来绕过这一点。只要输入`thisisunsafe`，Chrome 就会让你进入网站。

虽然这种保护是有原因的，并且每次进入警告屏幕时都使用这种技巧并不是一个好主意，但这可能会帮助您访问一些否则无法访问的网站(证书已过期的旧网站，甚至是维护期间您自己的网站)。

*喜欢这篇文章？考虑在《T2》上支持我。[了解更多](https://miguelpiedrafita.com/patreon)。*