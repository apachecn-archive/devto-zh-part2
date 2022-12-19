# 作为媒体主持人的 Pastebin

> 原文：<https://dev.to/agentd00nut/pastebin-as-a-media-host-27in>

## 总结

几个月前，在一次相对便宜的促销活动中，我购买了一个 pastebin pro 帐户，然后试着看看我可以用它做什么。

我发现通过他们的 API 发送一个媒体文件的原始数据，该媒体文件的原始页面就能正确显示！你至少需要一个免费账户，这样你就可以从他们的 [api 页面](https://pastebin.com/api#1)获得一个开发密钥。

这很有趣，因为使用 web 界面粘贴媒体文件是行不通的！

我已经做了一个准系统的 php 脚本，让你可以玩这个，还有很多需要改进的地方，但是应该足够让你上传自己的图片了。请记住，如果您没有专业帐户，粘贴箱的大小限制是非常小的！

## ![GitHub logo](../Images/292a238c61c5611a7f4d07a21d9e8e0a.png)[agent d00n ut](https://github.com/agentd00nut)/[pasteStore](https://github.com/agentd00nut/pasteStore)

### 要上传到 pastebin 的准系统 CLI php 脚本。

<article class="markdown-body entry-content container-lg" itemprop="text">

# pasteStore

轻松发送文件，包括图像和大多数媒体类型，到 pastebin。

## 设置

从 [api 页面](https://pastebin.com/api#1)获取你的开发者密钥，并保存到 **devKey** 文件中。

如果你想让私人帖子做同样的事情，但你的用户密钥，并将其保存到**用户密钥**文件。

## 使用

从管道传递数据会将随机名称和内容设置到 STDIN。`cat myImage.jpg | pasteStore.php`

使用参数从管道传递数据会将粘贴名称设置为参数，将内容设置为标准输入。`cat myImage.webm | pasteStore.php thisIsMyCoolPaste`

仅传递一个参数会将参数视为文件的路径并提取其内容。`pasteStore.php myImage.mp4`

手动编辑代码，以改变其上传为私人或如果它将过期。

更好的是，编写一些参数处理并发出一个拉请求！

</article>

[View on GitHub](https://github.com/agentd00nut/pasteStore)

## 例子

对于图片、gif 和 webms，你可以直接访问链接，它们可以工作。对于. mp4，。你需要把它们嵌入到网页中才能使用。

我只试过 png 和 jpg 的。我相信几乎任何常见的图像格式都可以。
[PNG](https://pastebin.com/raw/rSj0j82Q)
[![PNG](../Images/6a675adebd45ec2357c6968a670461ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cMbtZfMD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pastebin.com/raw/rSj0j82Q)

[GIF](https://pastebin.com/raw/AHnbEk2a)
T3![GIF](../Images/959b3cb082d004c66a2824e49de99e19.png)T5】

[网页](https://pastebin.com/raw/Px16eDxe)

## mp4 等

. mp4，。webp，和其他**视频**格式不能直接工作。相反，我发现你需要将它们包装在视频元素中，并像这样设置源...

```
<video width="320" height="240" controls>
    <source src="https://pastebin.com/raw/exQT7F6z" type="video/mp4">
/video> 
```

Enter fullscreen mode Exit fullscreen mode

## 思想

这个有用吗？差不多吧。我喜欢这个想法，能够上传一大堆我的图片到一个我不用支付带宽的服务器上！

如果你没有专业帐户，它就没什么用了，因为你的最大上传大小是 500kb。

你也可以做一些傻事，比如把几个人的专业账户集中在一起，建立一个图片存储网站，在后台，只是链接到未列出的粘贴框帖子。

也许你可以写一个网络播放器，无缝地播放列表中的所有视频，然后将较大的文件分成 10mb 的块，并在 pastebin 上托管较大的视频。

目前，这只是一种有趣且体面的快速与他人分享图片的方式。我认为如果 pastebin 增加对 API 上传到文件夹的支持，它会变得更加有用。