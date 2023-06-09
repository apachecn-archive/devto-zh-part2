# 您了解这些 SVG 技术吗？

> 原文：<https://dev.to/ycmjason/do-you-know-about-these-svg-techniques-2k3o>

我最近发现了 3 种真正令人兴奋的技术:

1.  用[画布](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/canvas)将 [SVG](https://developer.mozilla.org/en-US/docs/Web/SVG) 转换成图像
2.  [HiDPI 画布](https://www.html5rocks.com/en/tutorials/canvas/hidpi/)
3.  预取 SVG: *Verrrrrryyyy cool*

我等不及要告诉你他们的事了！

## 背景故事

所以几个月前，我创建了 Faviator，到目前为止，我收到了一些非常好的反馈。如果您还没有这样做，请查看并开始使用它。

Faviator 依赖于一个名为 [convert-svg](https://github.com/NotNinja/convert-svg) 的库，它基本上是用[木偶师](https://dev.toheadless%20chrome)截取 svg 的截图。选择这个库是因为它直观的 API 和它使用[木偶师](https://github.com/GoogleChrome/puppeteer)的事实，这保证了图像输出看起来和我们在 Chrome 上看到的完全一样。

然而，这个库有两个主要问题:

1.  图像质量不佳；产生模糊的图像
2.  截图时，CSS `@import()`或`url()`并不总是被加载

> 不要误解我。 [convert-svg](https://github.com/NotNinja/convert-svg) 还是一个很神奇的项目！

第二个问题对于从[谷歌字体](https://fonts.google.com/)获取字体文件的[收藏夹](https://github.com/faviator/faviator)来说非常关键。我确实找到了如何修复它，并提交了一份 [PR](https://github.com/NotNinja/convert-svg/pull/45) ，但是所有者似乎太忙了，没有时间花在这个项目上。

于是决定自己创建:[@ ycm . Jason/SVG-to-img](https://www.npmjs.com/package/@ycm.jason/svg-to-img)；旨在解决这些问题。在从事这项工作的过程中，我发现了一些非常酷的 SVG 技术，所以我想我可以写一篇博客来介绍我的发现。

## 用[画布](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/canvas)将 [SVG](https://developer.mozilla.org/en-US/docs/Web/SVG) 转换成图像

正如我提到的，一种将 [SVG](https://developer.mozilla.org/en-US/docs/Web/SVG) 转换成图像的方法是用[木偶师](https://github.com/GoogleChrome/puppeteer)做一个截屏。这实际上是一个非常好的方法，因为我们不必担心在渲染 [SVG](https://developer.mozilla.org/en-US/docs/Web/SVG) 时可能出现的任何错误。

另一种方法是使用 HTML5 [画布](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/canvas)。我们可以使用 [drawImage](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/drawImage) 方法在[画布](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/canvas)上轻松绘制一个`<img>`。作为一个组合， [canvas](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/canvas) 还提供了一个[toda aurl](https://developer.mozilla.org/en-US/docs/Web/API/HTMLCanvasElement/toDataURL)方法，将绘图导出为 PNG 或 JPEG 格式。

行..那又怎样？

这意味着，我们可以:

1.  将一个`<img>`指向一个 SVG
2.  在一个`<canvas>`上画出`<img>`
3.  将`<canvas>`导出为 PNG 或 JPEG 格式

这里有一个快速演示:

```
const img = document.createElement('img');
img.src = 'some/path/to/the/awesome.svg';
img.onload = () => {
  const canvas = document.createElement('canvas');
  const context = canvas.getContext('2d');
  // draw img to (0, 0) on the canvas
  context.drawImage(img, 0, 0);
  // export the PNG or JPEG
  const pngDataURL = canvas.toDataURL('image/png');
  const jpegDataURL = canvas.toDataURL('image/jpeg');
  // ... do something with them ...
}; 
```

Enter fullscreen mode Exit fullscreen mode

注意所有这些都发生在浏览器中，这使得 [@ycm.jason/svg-to-img](https://www.npmjs.com/package/@ycm.jason/svg-to-img) 能够同时支持浏览器和 Node.js(使用[木偶师](https://github.com/GoogleChrome/puppeteer))。

## [HiDPI 画布](https://www.html5rocks.com/en/tutorials/canvas/hidpi/)

HiDPI Canvas 是由 Paul Lewis 引入的一种技术。它解决了高 DPI 设备和绘制[画布](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/canvas)的问题。详细解释请看他的[篇](https://www.html5rocks.com/en/tutorials/canvas/hidpi/)。

总结:

1.  画布绘图在高 DPI 设备中以 2x 像素绘制，以保持宽度和高度
2.  这基本上是放大图像，导致图像模糊
3.  为了解决这个问题，我们可以绘制两倍于预期大小的画布，并使用 CSS 将其缩小到预期大小。

快速举例(打算画 200x500):

```
<canvas width="400" height="1000" style="width: 200; height: 500">
</canvas> 
```

Enter fullscreen mode Exit fullscreen mode

这项技术使我能够在高 DPI 屏幕上将 SVG 转换成清晰的图像。但是，它需要手动调整图像的大小。当前的实现使用了 [jimp](https://github.com/oliver-moran/jimp) ，但是我打算为此写一个更小的。(因为现在我把整个该死的 [jimp](https://github.com/oliver-moran/jimp) 都包含在这个包里了。这是有罪的...)

你可以看到它们是多么的不同:
**原 SVG**
[![](img/7d79e3f7f43d1a0e3a898db09098b53d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OvuwoGxh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://svgshare.com/i/7Sp.svg)
[https://svgshare.com/i/7Sp.svg](https://svgshare.com/i/7Sp.svg)

(显示的图像不是 SVG...图像主机将其转换为 png...)

**无 HiDPI 技术**
[![](img/257475d30b2612b431ae343b4a366505.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tSQ2DCoB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/vzps1C7.png)

**用 HiDPI 手法**
[![](img/49a554870468f0c8e7fe953380df3420.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UeAQWVLY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/UsQSmgN.png)

你能看出区别吗？如果您关注“F”的提示，您会注意到最后一个与原始 SVG 更加匹配。

虽然这肯定改善了图像细节，但它仍然显得有点模糊。这可能是由调整图像大小引起的。我不是图像处理方面的专家，如果你能告诉我一些有用的技术，我会很高兴。

## Prefetching SVG

CSS 可以嵌入到 SVG 中来控制样式。随着 css3 [@import](https://developer.mozilla.org/en-US/docs/Web/CSS/@import) 的引入，我们现在可以在 CSS 定义中包含 CSS 了！多神奇啊！

然而，如果您在`<img>`中显示您的 SVG，您可能会发现样式没有被导入。如果在`<img>`中使用 SVG，浏览器(或者只是 Chrome)似乎会忽略任何外部资源。

我发明了一种叫做预取 SVG 的技术，可以解决上面的问题，让你的 SVG 即使离线看起来也一样！

这个想法是用导入的内容替换 [@import](https://developer.mozilla.org/en-US/docs/Web/CSS/@import) 。用数据 url 替换所有的`url()`。我为此创建了一个库: [prefetch-svg](https://github.com/ycmjason/prefetch-svg) 。

**无预取**

> 因为您可能在本地安装了字体系列，所以我明确地删除了`@import`来演示。

[![](img/8bc90326e0e2b6b463a3f123d0b49028.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PW6qLdhV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://svgshare.com/i/7RH.svg)

**带预取**

[![](img/510318a6342a706ce2a2b45fdf1de11f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wJuvEFsN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://svgur.com/i/7Sp.svg)

## 就是这样

仅此而已。下面是我关于 SVGs 的一点分享。说说你的想法！我错过了什么吗？