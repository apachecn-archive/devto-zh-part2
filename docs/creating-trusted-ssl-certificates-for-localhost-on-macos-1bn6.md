# 在 MacOS 上为本地主机创建可信 SSL 证书

> 原文：<https://dev.to/georgemandis/creating-trusted-ssl-certificates-for-localhost-on-macos-1bn6>

最近，我正在使用 [Stripe Elements](https://stripe.com/docs/stripe-js/elements) toolkit 为我的网络开发公司使用的一款定制计费软件[工作。该库是一个精心设计的 UI 组件的集合，用于与 Stripe 的服务进行接口。具体来说，我正与他们的](https://snaptortoise)[支付请求按钮](https://stripe.com/docs/stripe-js/elements/payment-request-button)一起工作，该按钮被设计成与目前正在讨论的 W3C 支付请求 API 标准一起工作。

要实现支付请求按钮，您需要使用安全(https)连接。使用 [OpenSSL](https://www.openssl.org/) 进行设置并不困难，但是支付请求 API 需要的不仅仅是一个基本的 SSL 证书——它还必须是可信的。Stripe 建议使用像 [ngrok](https://ngrok.com/) 这样的服务，这是一个很好的建议，但不一定总是可行或可取的。这就是我的项目的情况。

在 macOS 上为本地主机服务器创建可信的 SSL 证书需要很多步骤和大量的研究。当我最终生成了证书并让一切顺利运行时，我决定将它打包成一个可以在终端中运行的命令，并用它创建了一个 GitHub 项目:

[github.com/snaptortoise/ssloca](https://github.com/snaptortoise/ssloca)

现在，您可以用两行代码生成并信任这些证书，而不是像我之前的许多开发人员那样将神秘的 OpenSSL 命令复制并粘贴到终端中:

`git clone git@github.com:snaptortoise/ssloca.git && cd ssloca` `./create-local-certificates.sh`

您应该最终得到一个名为`certs`的文件夹，其中包含所有本地生成的证书，包括根证书和您的服务器的证书。更多关于事物是如何产生的信息可以在代码中找到。

我用西班牙语将这个项目称为 SSL Loca T1——SSL、local 和 loca 的组合。因为让 **SSL** 在**本地**工作让我觉得有点**疯狂**。

我的脚本还有很大的改进空间，我知道有很多开发人员也经历了同样的困境来解决这个问题。请看看它，让我知道你是否觉得它有用，如果你对如何改进它有想法，尽一切办法提交一个拉请求。