# 教程- PHP 从字符串替换 Img Src

> 原文：<https://dev.to/joshualjohnson/tutorial---php-replace-img-src-from-string-29hc>

最近，我不得不弄清楚如何只使用 PHP 来更改标签 src。我不得不将请求从 HTTP 改为 HTTPS。我的任务是把一个网站转换到 HTTPS。大商业上经营网站。正如我们已经知道的，当你改变一个网站到 HTTPS，你必须追捕该网站的所有图像/资产，并将其转换为请求 HTTPS。否则，您将会以可怕的混合内容错误而告终，并且您的网站被您访问网站的浏览器视为不安全。

因此，通过 HTTPS 而不是 HTTP 进行请求，一页一页地浏览和更新图像/资产是非常容易的。在我遇到大问题之前，一切都进行得很顺利。

## 问题是

对于这个特定的客户来说，网站的产品并不在大商业内部经营。相反，它们由第三方 ERP(企业资源规划)软件管理。它与大型商业产品页面同步变化。因此，我们发现产品页面基本上是在 ERP 中管理的。任何在产品描述中包含图片的产品都包含在 HTTP。所以我们在这里...潜在的成千上万的产品，所有这些我都会手动编辑每个包含`<img src="[http://www..](http://www..).">`到`<img src="[https://www..](https://www..).">`的产品描述。在 **http://** 上只加了一个 **s** 。

## 用 PHP 替换字符串中的 Img Src 解决问题

在做一些研究时，我发现 ERP 有一个钩子，在 ERP 将数据同步到大商业之前，你可以引入自定义逻辑。因为每个产品描述都存储为一个 HTML 字符串，我想，我可以直接操作产品描述，并将 HTTPS 添加到内容中的所有图像。

这是我用来解决这个问题的代码:

```
<?php

$productDesc = '<a href="https://ua1.us/">Text</a>';
$productDesc .= '<img src="http://ua1.us/media/media.jpg">';
$productDesc .= '<div class="test"></div>';
$productDesc .= '<img src="http://ua1.us/media/media1.jpg">';

preg_match_all('/<img[^>]+>/i', $productDesc, $images);
foreach ($images[0] as $image) {
    $secureImg = str_replace('http://', 'https://', $image);
    $productDesc = str_replace($image, $secureImg, $productDesc);
}
echo $productDesc;
```

在上面的代码中，我们使用 PHP 中的`preg_match_all`方法返回所有`<img>`标签的实例，作为原始图像标签的数组。然后我们遍历每个图像标签，使用`str_replace`方法找到`http://`并用`https://`替换。这将返回一个表示安全图像请求的字符串。然后我们重复查找替换。这一次，我们希望用安全的图像替换原始图像。

最初发布于 [UA1 实验室](https://ua1.us/news/tutorials/tutorial-php-replace-img-src-from-string/)