# 提高页面速度:优化图像

> 原文：<https://dev.to/codingfix1/improving-page-speed-optimizing-images-367e>

为你的网页图像优化图像是一个非常重要的和战略上至关重要的任务，以提高平均网页速度。图像大小在几个方面影响你的页面速度，我们必须采取不同的技术来优化页面。

## 调整图像大小

首先，我们必须考虑图像的大小，也就是它们的宽度和高度。如果你懒，就像我 [![Resize pictures](img/b593e36229710c31c3db1bc85b1a0e3c.png)](http://codingfix.com/wp-content/uploads/2018/07/respic.png) 一样；)，您可能会让 css 为您完成这项工作:您只需上传您的图像，然后编写一系列 css 规则来调整它的大小(或者您甚至可以将宽度和高度设置为< img >标记的属性！！！).这是一个巨大的错误:数百千字节可以节省服务缩放图像。像 [GTMetrix](https://gtmetrix.com) 这样的工具会将此作为提高页面速度的高优先级任务来签署。

因此，如果网页中真正显示的图像的宽度为 300 px，高度为 120 px，则不要使用 1200 px x 480 px 的原始图像:而是调整其大小，并使用请求大小的图像。如果同一个图像必须以不同的尺寸显示，上传到你的服务器上，每种尺寸一个图像，可以在图像名称后面附加一个指定宽度和高度的后缀。并且确保将正确的图像链接到您的标签的“src”属性。

这里的黄金法则是:**不要使用 CSS 来调整图片的大小！**

## 组合您的图像

为了优化图像的加载时间，另一个有用的技术是使用精灵。这项技术诞生于视频游戏开发中，用于创建动画，也可以熟练地用于 web 开发。

假设你正在建立一个多语言网站。也许你会想在你的页面上放置 2、3 或更多的标志，用户可以点击这些标志来相应地改变语言。最好的方法是构建一个独特的图像，保存所有你想使用的标志，并使用 css 每次只显示一个标志。举个例子吧。

假设有 3 个像这样的旗帜图像:

| [![Union Jack](img/24f9b91a8bbb2799b223f29bd50a88e7.png)T2】](http://codingfix.com/wp-content/uploads/2018/07/flag_en.png) | [![Rojigualda](img/2bd20a6776948ba6dc39dbbe608f0203.png)T2】](http://codingfix.com/wp-content/uploads/2018/07/flag_es.png) | [![Tricolore](img/a4f6161f236e79758e1f1b7f918bd171.png)T2】](http://codingfix.com/wp-content/uploads/2018/07/flag_it.png) |

### 错误的方式

我们可能会想写这样的东西:

```
<ul id="flags">
    <li>
        <a href="#" class="lang active" id="es">
            <img src="images/flags/flag_es.png" width="32" height="32" />
        </a>
    </li>
    <li>
        <a href="#" class="lang" id="en">
            <img src="images/flags/flag_en.png" width="32" height="32" />
        </a>
    </li>
    <li>
        <a href="#" class="lang" id="it">
            <img src="images/flags/flag_it.png" width="32" height="32" />
        </a>
    </li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

### 正确的方式

代替使用分别嵌套在三个<anchor>标签中的三个图像，这可能会触发语言变化事件，我们将使用我们选择的图像编辑器将三个标志组合在一个图像文件中。每个旗帜是 32 像素 x 32 像素，所以我们可以建立一个宽度为 32 像素，高度为 96 像素的图像。我们会得到这样的结果:</anchor>

[![Flags sprite](img/9e19b0bbbfc1c331aef9c65ed08e0b3c.png)T2】](http://codingfix.com/wp-content/uploads/2018/07/flags.png)

现在我们可以为我们的语言菜单使用下面的 html 结构:

```
<ul id="flags">
    <li>
        <a href="#" class="lang active" id="es"&gt;&lt;/a>
    </li>
    <li>
        <a href="#" class="lang" id="en"&gt;&lt;/a>
    </li>
    <li>
        <a href="#" class="lang" id="it"&gt;&lt;/a>;
    </li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

下面是这段标记的 CSS 规则:

```
ul#flags{
    list-style-type: none !important;
    margin-bottom: 0 !important;
}
ul#flags li{
    display: inline !important;
}
ul#flags li a{
    display: inline-block !important;
    width: 30px !important;
    border-radius: 50% !important;
    overflow: hidden !important;
    text-align: center !important;
}
ul#flags li a#es{
    background: url(img/flags/flags.png) 0 0 no-repeat;
}
ul#flags li a#en{
    background: url(img/flags/flags.png) 0 32px no-repeat;
}
ul#flags li a#it{
    background: url(img/flags/flags.png) 0 64px no-repeat;
} 
```

Enter fullscreen mode Exit fullscreen mode

你注意到最后三条规则了吗？在这里，我们设置了背景图片的顶部属性:0 代表西班牙国旗，32 代表英国国旗，64 代表意大利国旗。结果将如下所示:

[![Language Flags](img/f2c9662003dc7f9132bd30e72eb97821.png)T2】](http://codingfix.com/wp-content/uploads/2018/07/flags-1.png)

在本例中，我用橙色圆圈突出显示了活动语言(使用 javascript 来更改 click 事件中的活动项，但我将在另一篇关于如何使用几个精彩的 jQuery 插件创建多语言网页的文章中介绍这一点)。

使用精灵有什么好处？正如 GTMetrix 解释的那样，

> 使用 CSS sprites 将图像合并到尽可能少的文件中，可以减少下载其他资源的往返次数和延迟，减少请求开销，并可以减少网页下载的总字节数。

## 压缩您的图像

### 选项 1

无论你使用什么技术来优化你的图像，几乎可以肯定的是，PageSpeed Insights(或 GTMetrix 或任何其他你用来衡量页面性能的工具)会告诉你"**优化图像**"。好消息是，GTMetrix 和 PageSpeed Insights 都允许您从它们的服务器下载优化的图像。PageSpeed Insights 甚至以一种更舒适的方式实现了这一点:它为您优化您的图像，迷你化您的 javascripts 和 Css，并以压缩文件的形式提供它们以供下载:这难道不好吗？

*关于 minified javascript 和 css 在内容优先排序过程中的使用，你可以通过阅读[这篇文章](http://codingfix.com/improving-page-speed-prioritizing-content/)了解更多。*

### 选项 2

您甚至可以选择压缩和优化您的图像，只需使用已经优化的内容进行测试。你的第一个选择是使用在线工具。例如， [compressor.io](https://compressor.io/) 这是一个神奇的工具，可以让你无损压缩你的图像，并节省数百 Kb，正如他们在主页上声称的那样。如果你使用 Wordpress，你可以不用 [SmushIt 插件](https://wordpress.org/plugins/wp-smushit/)来优化你上传的图片！

### 选项 3

您也可以使用独立软件。我试过的最好的一个当然是 [Riot](http://luci.criosweb.ro/riot/) (代表激进图像优化工具的首字母缩写):它只适用于 Windows，但是你可以使用 Wine 很容易地从你的 Linux 机器上运行它。它有魔力！它可以将 19.4 Mb 的 4500 x 3000 图像压缩到 5.09 Mb，而无需对图像进行重新采样，并且没有任何可见的质量损失。如果您将图像的宽度和高度调整到更合理的大小，比如宽度为 1000，高度为 668，那么新的文件重量将为 173.9 Kb！

我希望这篇文章对你有用。如果您有任何意见或问题，请随时发表评论或通过[联系页面](http://codingfix.com/contacts/)联系我。如果你真的觉得自己很慷慨，别忘了在你的社交网络上分享这篇文章！谢谢大家！

帖子[提高页面速度:优化图片](https://codingfix.com/improving-page-speed-optimizing-images/)最早出现在 [codingfix](https://codingfix.com) 上。