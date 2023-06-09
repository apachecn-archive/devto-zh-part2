# PayPal 支付服务概述

> 原文：<https://dev.to/angelleye/paypal-payment-services-overview-33kf>

# 概述

PayPal 拥有广泛的支付产品和服务。多年来，他们中的许多人已经重叠，这可能是非常混乱的什么时候你应该使用。

我只是想对 PayPal 提供的东西做一个真实的回顾，因为我看到很多开发者对他们提供的东西感到沮丧和困惑。

# 贝宝标准

如果你在你的贝宝账户中创建基本的贝宝按钮，这是贝宝标准。

这也可以用在其他方面，但最终结果是一个简单的 HTML 表单，它建立了一个向 PayPal 发送的基本表单，其中包含了支付细节的标准变量。

这是将 PayPal 支付捆绑到网站或应用程序的最简单方法。

# 快速结账

这是集成贝宝支付的 API 方法，买家通常会登录贝宝账户并提交付款(尽管这不是必需的。)

这可以作为一个 REST/JSON 服务来使用，也可以作为 T2 的 NVP/SOAP 来使用。

PayPal 称 NVP/SOAP 为经典 API，他们在该文档中注意到它已被弃用。这并不完全正确。

他们确实在努力让人们使用 REST 服务(或者其他我们稍后会谈到的服务),但是传统的 API 不会有任何发展。如果您出于任何原因更喜欢 NVP/SOAP，或者您已经以这种方式集成了一些东西，您不必担心它会很快消失。

如果可以的话，我建议你使用 REST 服务，并且我特别建议你确保集成了[智能支付按钮](https://developer.paypal.com/docs/checkout/#smart-payment-buttons?mark=smart%20pay)。

智能支付按钮为买家提供了更好的体验，并且更明显的是，你不必拥有 PayPal 账户就可以用信用卡支付。不过，这仍将是 PayPal 品牌的结账方式。

# 网站支付专业版

具体来说，这就是 [DoDirectPayment](https://developer.paypal.com/docs/classic/api/merchant/DoDirectPayment_API_Operation_NVP/) API。这是直接处理信用卡的经典 API 版本，就像您处理任何其他信用卡处理器帐户(即 Stripe、Intuit、PayLeap、Group ISO 等。)

这样，您只需将一个信用卡表单直接添加到您的收银台，用户可以在那里输入他们的详细信息。您可以将这些详细信息发送到 PayPal 的服务器，以获得成功/失败的结果。

此处未显示任何 PayPal 品牌。构建结账系统并相应地处理数据完全取决于您。

# 付款亲

这个名字中没有包含“网站”,实际上与 DoDirectPayment 是一样的，只是它使用了 [PayFlow 网关](https://developer.paypal.com/docs/classic/products/payflow/)。

PayPal 几年前从 Verisign 收购了 PayFlow gateway。不幸的是，他们使用的产品名称非常像 DDP 产品，因此经常被混淆。

如果你曾经与客户合作或为“PayPal Pro”构建自己的集成，你需要确保你确切地知道你将使用哪个版本:DoDirectPayment 或 PayFlow。

我见过不少人找到了“Pro”的文档，进行了相应的整合，后来发现他们使用了错误的文档，这是基于客户帐户上的实际数据。小心这个。

# 布伦特里

这是 PayPal 几年前的又一次收购。Braintree 本质上是另一个标准的信用卡处理器，就像 Stripe 或任何其他的，但是它们提供了几种不同的方式来集成东西。

## 插件界面

为了简化集成，您可以使用 [Braintree 插件 UI](https://developers.braintreepayments.com/guides/drop-in/overview/javascript/v2) ，它使用 iFrame 将 Braintree 托管的支付系统嵌入到您的应用程序中。

这将包括 PayPal、直接信用卡、Apple Pay 和 Google Pay，所有这些都集成在一个单一的集成中。这很棒，而且在大多数情况下都非常有效。如果你觉得太局限，你可以直接集成 Braintree。

## 直接

这本质上和 PayPal Pro(上面讨论的 DoDirectPayment 和 PayFlow)是一回事。)这允许您直接处理卡片，而根本不需要任何 iFrame 或品牌(除了您自己添加的)。

# 参考交易

这是当你使用贝宝存储在他们的服务器上的数据来处理同一买家的未来付款。这允许你设置类似“一键式支付”的东西，而不需要在你自己的数据库中存储敏感的信用卡信息。

你可能会看到这被称为许多不同的东西:参考交易，代币支付，保险支付，未来支付等。它们的意思都一样。

## 经典 API

对于经典 API，术语是“引用事务”。这可以与快速结帐和贝宝专业使用。

但是，如果您使用快速结账，卖家 PayPal 帐户需要启用“[账单协议](https://developer.paypal.com/docs/classic/express-checkout/integration-guide/ECReferenceTxns/?mark=reference%20transaction#)”。不幸的是，这是一件很难获得批准的事情。

使用 PayPal Pro 的参考交易无需额外批准

## 休息 API

这个功能在 REST API 中被称为[库。](https://developer.paypal.com/docs/integration/direct/vault/)

# 自适应支付

这是 PayPal 在 2009 年推出的一个平台，非常酷，但它没有持续下去，现在它已被官方弃用。如果你已经获得批准，你可以继续使用它，但他们不会批准任何新的自适应支付应用程序 id。

它可以让你在结账时实时分期付款，调整谁支付费用，使用 API 创建账户，以及其他一些我不会在这里过多讨论的事情，因为它已经不再可用了。

自适应支付意味着将被贝宝市场取代。

# 支付宝商城

这是 PayPal 为人们建立在线市场而推出的产品，如易贝、亚马逊或其他类似网站，第三方供应商在这些网站上发布产品进行销售，网站所有者通常希望获得佣金。

这个平台将允许你像 Adaptive Payments 一样进行分期付款，如果你愿意，它还允许你收取自己的额外费用(超过 PayPal 的费用)。)

## 提示

在撰写本文时，Marketplaces API 仅适用于部分 PayPal 合作伙伴。因此，有一点差距需要填补，因为自适应支付已经被否决，但市场还没有准备好。如果你发现自己陷入了这种困境，请查看本指南寻找解决这个问题的方法。

# 结账

PayPal 提供了一个非常强大的免费发票系统，内置在平台中。如果你还不知道的话，去 www.paypal.com/invoice 就可以体验一下了。

它允许您创建带有专业品牌设计的发票，并将其发送给买方。它可以让你的买家用 PayPal 帐户付款，或直接使用信用点数付款。您可以发送发票提醒、取消发票等。所有这些都来自您的 PayPal 发票管理器。

[Invoicing API](https://developer.paypal.com/docs/invoicing/)简单地允许您通过 API 调用完成所有这些。

# 即时付款通知(IPN)

这是一个[通知系统](https://developer.paypal.com/docs/classic/ipn/integration-guide/IPNIntro/)，其中 PayPal 将向您指定的监听器发送一份数据，任何时候交易到达您的 PayPal 帐户。

IPN 设计用于:

*   贝宝标准
*   经典 API

# 网页挂钩

Webhooks 和 IPN 是一样的，但是它们更加广泛和灵活。Webhooks 与 REST APIs 一起工作。

确保将 IPN 用于经典 API，将 Webhooks 用于 REST APIs 是非常重要的。如果你用 REST 触发了一个付款，期望得到一个 IPN，当你没有得到一个时，你会困惑地坐在那里。

# 结论

这是我尝试写的一篇简短而甜蜜的帖子，内容是关于 PayPal 的产品，以及如何避免将各种产品集成到您的应用程序中时出现的混乱或问题。这可能是一篇更长的文章，所以如果我错过了你感兴趣的东西或者我搞混了，你就告诉我，我会澄清的。

希望这在某些方面对你有价值！