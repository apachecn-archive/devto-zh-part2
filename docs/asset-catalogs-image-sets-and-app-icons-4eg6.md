# 资产目录、图像集和应用程序图标

> 原文：<https://dev.to/leogdion/asset-catalogs-image-sets-and-app-icons-4eg6>

由于我一直在研究 [Speculid](https://speculid.com/?utm_source=learningswift&utm_medium=web) ，我发现了一些伴随资产目录而来的有趣特质。基本上，资产目录是专门文件(比如图形文件)以及包含元数据的 JSON 文件的集合。来自 JSON 文件的元数据用于组织专门的文件，并表示它们在集合中的用途。对于像图像集这样的图形集，它们包含几条信息。

# 资产目录—图像集

以下是一个图像集示例:

```
{
  "images" : [
    {
      "idiom" : "universal",
      "filename" : "apple-watch@1x.png",
      "scale" : "1x"
    },
    {
      "idiom" : "universal",
      "filename" : "apple-watch@2x.png",
      "scale" : "2x"
    },
    {
      "idiom" : "universal",
      "filename" : "apple-watch@3x.png",
      "scale" : "3x"
    }
  ],
  "info" : {
    "version" : 1,
    "author" : "xcode"
  }
} 
```

首先，我们有一个具有以下属性的图像集合:

`filename`–特定图像的文件名

`scale`–图像文件的指定比例；到目前为止，使用了四种等级:

*   *默认未指定* —任何显示的矢量 pdf 文件
*   `1x`–iphone 最高可达 3GS，iPod Touch 最高可达 3 档
*   `2x`–iPhone 4、iPhone 4s、iPhone 5、iPhone 5c、iPhone 5s、iPhone SE、iPhone 6、iPhone 6s、iPhone 7、iPhone 8、iPhone Xr、iPod Touch 第 4 代及以上、MacBook Pro 第 3 代及以上、MacBook (2015 年及更新版本)
*   `3x`–iPhone 6+，iPhone 6s+，iPhone 7+ iPhone 8+ iPhone X，iPhone Xs，iPhone Xs Max，iMac 4K 和 5K

`idiom`–特定图像文件的指定设备和用途

# 更多关于成语…

习惯用法指明了图像文件的设备和用途。以下是特定属性字段的一般
设备值:

*   `universal`–默认值，表示图像文件可在任何设备和平台上工作。
*   `iphone`–该图片适用于 iPhone 设备。
*   `ipad`–该图片适用于 iPad 设备。
*   `mac`–该图像适用于 Mac 电脑。
*   `watch`–图片是苹果手表设备。
*   图片是苹果电视的。

一些习惯用法表示它们的用途是用于 App Store:

*   `ios-marketing`–应用商店图标的图像
*   `watch-marketing`–App Store 图标的图像。

# 资产目录—应用程序图标

就包含的属性而言，图像集相当简单。但是，资产目录中的应用程序图标包含相同的信息，甚至更多。例如，以下是 38 毫米 Apple Watch 的通知中心图标示例:

```
... 
{   
"size" : "24x24",   
"idiom" : "watch",   
"scale" : "2x",   
"role" : "notificationCenter",   
"subtype" : "38mm" 
}, ... 
```

*   `size`图像文件的大小以磅为单位，这指定了图像是否
*   `subtype`–通常用于 Apple Watch 图标，它们指定图像文件的 Apple Watch 尺寸
*   `role`–通常用于 Apple Watch 图标，表示图标的特定用途。

角色的值可以是:

*   `appLauncher`–显示应用程序启动器的图像。
*   `companionSettings`–苹果手表设置应用的图片
*   `notificationCenter`–watch OS 上通知中心的图像。
*   `quickLook`–用于简短外观通知的图像
*   `longLook`–用于长时间观看通知的图像

由于每个可用设备及其可能的角色和子类型，一个应用程序图标集可能包含多达 41 个单独的文件。这就是为什么最好使用自动化的方法来渲染这些文件。查看我们关于自动化流程的系列博客文章，了解更多细节。

如果你有兴趣了解更多关于资产目录和捆绑包的信息，也可以查看一下[古伊列梅·兰博的帖子](https://rambo.codes/ios/2018/10/03/unleashing-the-power-of-asset-catalogs-and-bundles-on-ios.html)还有[苹果文档中有关于每项可用资产和价值的更多细节。](https://developer.apple.com/library/archive/documentation/Xcode/Reference/xcode_ref-Asset_Catalog_Format/)

* * *

*最初发布于 2018 年 11 月 7 日[learningswift.brightdigit.com](https://learningswift.brightdigit.com/asset-catalogs-image-sets-app-icons/)。*