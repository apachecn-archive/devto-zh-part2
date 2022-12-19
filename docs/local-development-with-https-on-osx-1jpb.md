# HTTPS 在 OSX 的地方发展

> 原文：<https://dev.to/tability/local-development-with-https-on-osx-1jpb>

软件开发中一个很好的经验法则是，您应该尽可能地接近您的生产环境。这样做有助于避免由于本地开发设置和生产配置之间的差异而导致的常见错误。容器确实有助于减轻一些依赖性问题，但对我来说一直很重要的一点是使用 HTTPS 进行本地开发。

在 Squadlytics，我们有一个用于 UI 的 React 应用程序和一个 Rails API 后端，我们强制使用 SSL。我们尝试了很多方法来寻找一个简单的方法来使用 HTTPS 进行本地开发，我们最终找到了一个使用代理和 dnsmasq 的解决方案。我将在此分享我们的工作方式，但首先，让我向您展示我们探索的替代方案。

## 选项一:自己生成证书

我们首先着眼于为本地开发生成我们自己的证书。首先，您需要创建自己的证书颁发机构。然后，您使用该实体来构建您的开发证书。

Brad Touesnard 写了一篇很棒的教程,如果你想沿着这条路走下去，你可以读一读。一开始看起来很棒，但是当我们必须为后端和前端找出定制的启动脚本以使用正确的证书时，它开始变得很麻烦。

我们不得不做一些不同的尝试来获得正确的结果，最终我们找到了一个更简单的解决方案，这样我们就不必自己管理证书了。

优点:一旦完成，它就工作了。缺点:设置和管理有点困难。

## 选项二:使用 [ngrok](https://ngrok.com)

如果您还没有使用 ngrok，那么您迟早会需要它。这是一个很好的工具，它允许你创建隧道来将你的本地开发机器暴露给 web。典型的用例是测试 webhooks，但是您也可以保留您的域，并使用子域([https://subdomain . ngrok . io](https://subdomain.ngrok.io))或使用您自己的开发域([https://API . squadlytics . dev](https://api.squadlytics.dev))生成到您的开发环境的 SSL 隧道。

它最小化了我们这边的配置。你只需要安装 ngrok 就可以让它工作——而且你不会在浏览器中得到证书错误。

但是如果你需要共享专用的 URL，问题就来了。例如，如果您的后端 API 可以在 [https://api.ngrok.io](https://api.ngrok.io) 访问，那么您只能将这个 URL 通过隧道传输到一台本地开发机器。据我所知，你必须为每个开发者保留自定义域来解决这个问题([https://API . John . ngrok . io](https://api.john.ngrok.io)，[https://API . Jane . ngrok . io](https://api.jane.ngrok.io))，并且可能在你的代码中做一些技巧来支持它。最重要的是，每个请求都需要通过 ngrok，这会降低开发速度，并阻止您在离线时进行编码。

所以我们放弃了 ngrok，因为它不适合我们本地的 SSL 开发问题。我想在这里强调的是，它是一个很棒的工具，试图过度使用它主要是我们的错。

优点:易于上手，非常适合测试 webhooks
缺点:开发速度慢，不能连续使用

## 选项三:本地-SSL-代理+ dnsmasq

为了简化我们的问题，我们回顾了当地发展的制约因素:

*   我们需要能够针对 API 和身份验证访问特定的域。
*   我们在工作空间中使用子域，因此需要在本地访问大量的子域。
*   我们需要通过 HTTPS 完成所有的请求。
*   我们希望有一些快速设置额外的服务，并易于新的开发人员安装。
*   能够离线编码将是一大优势。

考虑到这些目标，我们满足于使用代理服务器和定制的本地 DNS 服务器的简单解决方案。

#### [本地-SSL-代理](https://github.com/cameronhunter/local-ssl-proxy)

local-ssl-proxy 是一个 npm 包，它使用自签名证书启动一个简单的 SSL HTTP 代理。我们创建了一个小的配置文件，将 Rails 后端和 React 前端映射到 SSL 的不同端口。

```
{
  "Rails backend": {
    "source": 3001,
    "target": 3000
  },
  "React UI": {
    "source": 8090,
    "target": 8080
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这个配置，我们可以使用[https://API . squadlytics . dev:3001](https://api.squadlytics.dev:3001)和[https://acme . squadlytics . dev:8090](https://acme.squadlytics.dev:8090)通过 https 访问使用平台。不需要为 Rails 创建定制的启动脚本，也不需要弄清楚如何将证书传递给 React 应用程序。

添加新服务就像在配置文件中添加一个新条目一样简单。

#### [dnsmasq](http://www.thekelleys.org.uk/dnsmasq/doc.html)

我们需要解决的另一个问题是支持通配符域，以避免在/etc/hosts 中创建多个条目。幸运的是，您可以使用 dnsmasq 来实现这一点，并使用您自己的规则创建一个本地 DNS 服务器。

你可以按照[这个要点](https://gist.github.com/ogrrd/5831371)中的说明，看看如何在你的机器上设置它。在我们的例子中，我们创建了一个 bash 脚本来一次性完成这一切。

您应该小心不要为所有的`.dev`域设置通配符域，因为这是一个真正的 TLD。这就是为什么我们将配置限制为只将`.squadlytics.dev`域映射到本地主机，而不是支持所有使用`.dev`的 URL。

```
#!/bin/bash
echo "1\. Installing dnsmasq"
brew install dnsmasq
echo "2\. Configuring *.squadlytics.dev local domains"
mkdir -pv $(brew --prefix)/etc/
echo 'address=/squadlytics.dev/127.0.0.1' > $(brew --prefix)/etc/dnsmasq.conf
sudo cp -v $(brew --prefix dnsmasq)/homebrew.mxcl.dnsmasq.plist /Library/LaunchDaemons
sudo launchctl load -w /Library/LaunchDaemons/homebrew.mxcl.dnsmasq.plist
sudo mkdir -v /etc/resolver
sudo bash -c 'echo "nameserver 127.0.0.1" > /etc/resolver/dev' 
```

Enter fullscreen mode Exit fullscreen mode

有些人建议使用假 TLD(如。test)，但是像 Google 这样的服务需要 OAuth 的真实 TLD。

不要忘记在你的网络设置中添加你的本地主机作为 DNS 服务器，否则它将无法工作。

### 绕过 SSL 证书错误

我们的最后一步是绕过不可避免地被浏览器抛出的证书错误。

如果您使用的是 Safari，您可以在错误屏幕中接受访问该站点。只要确保不仅为您的 UI，而且为客户端使用的任何 API 都这样做。

Chrome 似乎要严格得多，直到今天，我只能通过使用`--ignore-certificate-errors`标志启动 Chrome 来绕过这些错误。

```
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --ignore-certificate-errors &> /dev/null & 
```

Enter fullscreen mode Exit fullscreen mode

希望这能对你们有些帮助。你可以在 https://github.com/squadlytics/local-ssl-development 的回购协议中查看我们的配置脚本。如果您有问题或建议，请不要犹豫，写下评论。

*照片由[利亚姆·塔克](https://unsplash.com/photos/cVMaxt672ss)在 Unsplash* 上拍摄