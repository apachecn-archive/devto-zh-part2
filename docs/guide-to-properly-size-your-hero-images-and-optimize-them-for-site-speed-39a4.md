# 英雄图像大小指南，以适当的大小，你的英雄图像，并优化他们的网站速度

> 原文：<https://dev.to/flores8/guide-to-properly-size-your-hero-images-and-optimize-them-for-site-speed-39a4>

如果你像我一样，你已经遇到过太多次了。

你的网页需要一个新的图片放在英雄部分或者页面的顶部。您希望该图像对于任何尺寸的屏幕都足够大，但又不至于大到显著降低页面的加载速度。你也不希望它太高。

你已经这样做了很多次，但是每次你打开你的设计工具来创建你的英雄图像时，你永远不会记得那个英雄图像应该有多大。

你知道第一件事是得到正确的图像尺寸。

### 英雄背景尺寸

[![Example of a hero background image](img/4aa1f548866c373db2249a6e9e0a7a36.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AzUfDXQb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://photos.lauraleeflores.com/n9bS/hero_bg_image.jpg) 
如果你的图像是标题/英雄背景图像，你需要保持 16:9 的宽高比。我将标题图像设为 1600 x 500px 像素。

### 整页背景图片尺寸

[![Example of Full Page background Image](img/e4a47c336415512ddefe0c0ec09b42a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---fG282S---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://photos.lauraleeflores.com/n9dE/full_page_bg_image.jpg) 
如果你要创建一个整页的背景图像，你会希望保持 4:3 的宽高比，我把这些图像做成 1600 x 1000px，有时是 1920 x 1200px。我一般两个尺寸都做，然后压缩。如果大图压缩得很好，并且不是太大，我会选择大图。

### 移动图像尺寸

根据移动设备上的反应和外观，我有时会切换移动设备上的英雄图像(使用媒体查询),以便在较小的屏幕上使用正确的纵横比。我通常制作 800 x 1200px 像素的移动图像。

你可以像这样交换英雄图像:

```
.hero-image {
  background: url('my-hero-image.jpg') no-repeat center top;
  background-size: cover;
}
@media (max-width: 768px) {
  background: url('my-mobile-hero-image.jpg') no-repeat center top;
} 
```

Enter fullscreen mode Exit fullscreen mode

这将显示您的背景图像，直到屏幕尺寸下降到 768 像素宽。然后任何小于 768px 宽视窗的英雄图像将显示移动图像。

### 下载草图文件

为了节省你的时间和精力，下次你必须创建你的下一个标题，只需[下载这个草图模板](http://photos.lauraleeflores.com/n8Ji/hero_image_template.sketch)。对于页眉图像、整页图像和移动图像，该模板都有一个画板。

把它保存在一个你知道的地方，下次你需要为你的网站创建一个图片的时候就可以找到它。所以下次你需要创建一个新的图像时，你只需要打开这个草图文件就可以了。

### 图像优化

一旦你知道了图片的高度和宽度，你就要把注意力转移到保持图片足够小上。

有时候 jpg 更小，有时候 png 更小。通常，对于这种类型的图像，jpg 会更小。但是，如果你不确定导出 jpg 和 png。为下一步使用哪个更小？

现在你要压缩你的图像，使它尽可能的小，而不是像素化。一般来说，尽量将图像的大小减小到 400 kb 左右或更小。

### 图像压缩工具

如果你还没有这样做过一千次，你应该对你放在网站上的每一张图片都这样做。每次你这样做的时候，你都在加速网页的加载时间。

如果你没有工具，你可以使用 [Tiny PNG](https://tinypng.com/) 。我个人更喜欢在我的电脑上安装这个工具，以便在不需要互联网连接的情况下随时随地使用。这就是为什么我用 [PNG 压缩器](https://itunes.apple.com/us/app/png-compressor/id434886325?mt=12) (macOS 应用程序)来压缩我所有的 PNG 图像。如果图片是 jpg，我会使用[压缩器](https://itunes.apple.com/us/app/compress/id405850942?mt=12) (macOS 应用程序)。

### 总结

[下载您可能会在每个页面使用的每个标题图像的草图模板](http://photos.lauraleeflores.com/n8Ji/hero_image_template.sketch)。一旦你下载了图像，导出并压缩它，使它成为没有像素化的最小尺寸(希望小于 400kb)。记住只把压缩过的图片放在你的网站上。