# 基于 SVG 的图像懒懒散散的 UX

> 原文：<https://dev.to/btdev/hey-user-you-already-see-me11---svg-based-image-lazyloading-as-a-matter-of-enjoyable-ux-1de9>

2017 年 11 月的某一天，我们的团队偶然发现了 José M. Pérez 的这篇文章，其中包含了大量关于图像占位和 lazyloading 的新内容。看着文章中提到的例子与我们那天处理它的方式相比，令人印象深刻。印象如此深刻，以至于我们决定试一试，在我们名为蓝色世界的[blue-tomato.com 内容部分](https://www.blue-tomato.com/blue-world/)上使用内联 SVG 实现图像延迟加载技术。

[![SVG-based image lazyloading](img/d7cfe0df2b9d969901a729d89cfc2191.png "SVG-based image lazyloading")](https://res.cloudinary.com/practicaldev/image/fetch/s--cXKoiPDM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/dwiuG5V.jpg) 
*差不多就是我们努力争取的转变*

除了 SVG 解析为高分辨率图像时令人印象深刻的“神奇时刻”之外，我们还遇到了更多可以改善用户体验的优势:

*   DOM 就绪时的即时视觉响应，因为 SVG 占位符是内联的，因此是 html 源代码的一部分
*   减少获取所有小像素、缓慢加载的初始占位符的请求数量

前面提到:我们设法实现了它，并且已经部署了它，所以[在我们的蓝色世界部分查看一下](https://www.blue-tomato.com/blue-world/),或者快速浏览一下下面的视频，看看实现情况。

[https://www.youtube.com/embed/HhpayDoMK5w](https://www.youtube.com/embed/HhpayDoMK5w)
*实现- slowmo 展示区*

开发之旅伴随着技术挑战、概念任务，最后是对实现的一些重写。因此，如果您喜欢基于 SVG 的镜像 lazyloading，并希望自己实现它，请查看我们即将发布的提示和实现说明，这样您也可以做到这一点(没有那些烦人的重写)。我们用来实现它的主要堆栈由以下令人惊叹的库组成:

*   [图元](https://github.com/fogleman/primitive):用几何图元复制图像的 Golang 工具
*   [Svgo](https://github.com/svg/svgo) :优化 SVG 矢量图形文件的 Nodejs 工具
*   [appear.js](http://creativelive.github.io/appear/) :当 DOM 元素出现在视图内外时执行回调
*   [Processwire](https://processwire.com/) :基于 PHP 的 cms 不需要使用那个 cms，按照我们接下来建议的概念和解决方案选择你想要的任何 cms

## 实现细节基于 SVG 的镜像 lazyloading

我们不会仔细检查交付给我们的存储库的每一行代码，以实现基于 SVG 的图像 lazyloading。我们的代码与我们的内部系统(主要是 Processwire cms)紧密耦合，因此向您全面介绍我们是如何解决实施问题是没有意义的。相反，如果我们不得不从头开始，我们将为您提供我们今天会考虑的路线图。下面将指导您完成这些主要步骤:

1.  生成并保存 SVG 占位符
2.  检查 SVG 可用性并发货
3.  正确的 SVG lazyload 占位符前端处理

### **1 日**生成并保存 SVG 占位符

*挑战*
我们的**第一个**方法是在上传过程中在 cms 的管理面板中生成 SVG。我们面临的第一个问题是，由于我们使用优秀的[原语](https://github.com/fogleman/primitive)库触发的转换过程，图像的上传过程会花费更长的时间。但这是两个问题中较小的一个。第二个问题是我们的图像资产管理系统(Adobe Scene7)即时创建了所有需要的图像格式。所以最初我们只使用上传图像的格式创建了一个 SVG，而没有考虑图像资产管理系统处理的其他格式。

[![different image formats as a challenge for creating svg-based lazyload images](img/a1974b18e978fb6fc76eac1af278a3de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gq60Xc0x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/hIhRuJo.jpg) 
*不同的图像格式需要不同的 SVG 格式*

***示例***
上传的图像格式:
15:4

但是我们模板中使用的格式:
skateboard-image.jpg？format = 16:9
skateboard-image.jpg？skateboard-image.jpg format = 4:3
？格式=1:1

因为我们只有一个基于原始上传图像宽/高比的 SVG，这有时会导致从一个 SVG 到原始图像的转换效果看起来很笨拙。

我们的**第二个**方法是动态生成 SVG 文件，并缓存所有生成的 html 文件，这些文件内联了所有需要的 SVG。我们知道生成和缓存整个标记文件(包括所有 SVG)的初始过程需要很长时间。但最终不会影响用户体验。我们的假设也是如此:)。最后，我们遇到了 http 超时问题，我们的编辑在保存需要长时间 SVG 生成循环的页面时会遇到这个问题。

*解决方案*
我们的**第三种**方法导致了我们目前的工作解决方案。如果一个编辑器上传了一个图像，而 SVG 还没有在我们的网络文件系统中生成，我们会自动创建一个“svg-to-be-produced-soon.json”文件。该文件包括一个关于 SVG 的信息对象(维度、图像服务器源、散列名称等。)那是需要生产的。

```
{  
   "jpgTmpFilePath":"/root_path/assets/primitives/tmp/kwleklsl090309f0g0.jpg",
   "svgFilePath":"/root_path/assets/primitives/svg/kwleklsl090309f0g0.svg",
   "svgMinFilePath":"/root_path/assets/primitives/svg/kwleklsl090309f0g0.min.svg",
   "bgColor":null,
   "inputUrl":"http://s7w2p3.scene7.com/is/image/bluetomato/ugc/2-1508153542.tif?$mc1$",
   "inputUrlHash":"kwleklsl090309f0g0",
   "env":"production"
} 
```

Enter fullscreen mode Exit fullscreen mode

*SVG-to-be-producted-soon . JSON 文件示例*

如您所见，这个即将生成的 svg 对象非常“哈希密集”。我们使用这个散列——取自`inputUrlHash`属性——来匹配图像和 SVG，以便有一个适当的资产识别过程，从 SVG 创建开始，直到在我们的模板文件中使用。

因此，每个新的 svg-to-be-produced-soon.json 文件都是队列的一部分，cronjob 将每小时处理和触发一次以生成 svg。让我们看看它能做什么。

```
#!/bin/bash

for jsonFile in /root_path/primitives/queue/*.json
do echo "Processing $jsonFile"

  primitiveBinary="${root_path}tools/primitive" 

  # primitive image
  goPrimitiveCommand="$primitiveBinary -i $jpgTmpFilePath -o $svgFilePath -n 64 -m 1 -r 256 -bg='$bgColor'"
  eval $goPrimitiveCommand

  # svgo
  svgoBinary="${root_path}node_modules/svgo/bin/svgo"
  svgoCommand="$svgoBinary  $svgFilePath -o $svgMinFilePath"
  eval $svgoCommand

  rm -f -- $jsonFile
done echo "All SVGs created"
exit 
```

Enter fullscreen mode Exit fullscreen mode

简化的 cronjob(我们删除了导致混乱的系统相关代码)做了四件主要的事情。

1.  循环并获取每个新的 svg-to-be-produced-soon.json 文件
2.  获取原始二进制文件并执行它来创建 SVG
3.  获取 svgo 二进制文件并执行它，以优化创建的 SVG 代码
4.  删除 SVG-to-be-producted-soon . JSON 文件

上传图像和发布页面之间的时间通常超过一个小时。令人高兴的结果是，大部分已经可用的 SVG 被发送给我们的用户，而不是我们的(同时内部不酷；))lazyload 回退选项。

所以我们终于拿到了！我们有自己创建和保存的 SVG。让我们走下一步，向我们的用户发送漂亮的、高性能的占位符图像。

### **2 日**检查 SVG 可用性并发货

一旦 SVG 创建完成，事情就会变得更加简单和直接。我们在代码库中定义了一个`createPrimitiveSvg`函数，它将图像及其格式作为参数，并返回打包到基于 base64 的数据 uri 中的最终 SVG 代码。

```
function createPrimitiveSvg($image, $format) {
  if(inSvgCache($image)) {
    $svgFromCache = loadFromSvgCache($image); // svg already available, get it
    return makeBase64DataUri($svgFromCache);
  } else {
    pushToSvgGenerationQueue($image, $format); // svg not available, make sure it will be made
    return getBlurryImageUrl($image); // ship old lazyload fallback
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*我们的 PHP `createPrimitiveSvg`函数为我们的模板处理 SVGs】*

进一步使用这个函数，将图像的根数据从我们的 cms 传递给它，如下所示。

```
<div class="imageContainer">
  <img
    class="js-lazyload"
    src='{$image->url|createPrimitiveSvg:"16:9"}'
  />
</div>

<div class="imageContainer">
  <img
    class="js-lazyload"
    src='{$image->url|createPrimitiveSvg:"4:3"}'
  />
</div> 
```

Enter fullscreen mode Exit fullscreen mode

### **第三**适当的 SVG lazyload 占位符前端处理

appear.js 在检查 DOM 中的目标资产是否已经到达视口方面做得很好。通过使用它，我们可以用相当轻量级的代码在前端管理 lazyloading。

```
appear({
  elements: () => document.querySelectorAll("img.js-lazyload"),
  appear: (element) => {
    const img = $(`<img class='${element.className} image--fadeIn' alt='${element.alt}' />`)
      .attr('src', element.dataset.original)
      .bind('load', () => {
        img.insertAfter(element);
        window.setTimeout(() => {
          $(element).remove();
        }, 2000);
      });
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看这有什么用:

1.  调用`appear(config: Object)`初始化所有配置对象属性出现。js 要求
2.  当 lazyload SVG 图像到达用户的视口时，`appear`回调处理高分辨率图像标签的创建
3.  一旦加载了高分辨率图像，它将被插入到已经显示的 SVG 的后面
4.  SVG 图像将使用 CSS3 及其模糊过渡选项淡出
5.  2 秒钟后从 DOM 中删除 lazyload SVG 图像

就是这样！

## 结论

我们对在我们的[蓝色世界](https://www.blue-tomato.com/blue-world/)中实现基于 SVG 的 lazyloading 所带来的成果和收益感到非常高兴。如果你至少有一点喜欢用户体验的话。试试看。看到这一切发生在你自己的项目中会有趣得多。编码快乐，lazyloading 快乐！