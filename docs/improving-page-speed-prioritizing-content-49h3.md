# 提高页面速度:区分内容优先级

> 原文：<https://dev.to/codingfix1/improving-page-speed-prioritizing-content-49h3>

每个 web 开发人员都知道，优化网站性能以向访问者提供最佳用户体验是多么重要。可能几乎每个 web 开发者都知道 Google PageSpeed Insights 是什么，以及我们必须监控和调整网站的哪些方面才能从我们的服务器获得最大的加载速度。这是我想向您详细解释如何提高页面性能的系列文章的第一篇。在这第一篇文章中，我们将看到如何提高页面加载速度优先内容。

当您使用 PageSpeed Insigths 测试您的网页时，您可能会得到以下建议:

> 缩小折叠内容的大小

这是什么意思？你可以阅读[这一页](https://developers.google.com/speed/docs/insights/PrioritizeVisibleContent)来知道谷歌到底是什么意思，但本质上它意味着你必须改变你的 html 的呈现方式。换句话说，你必须先加载用户首先看到的内容，然后再下载其他资源。如果你想要一步一步的指导，继续读下去。

## 识别折叠以上的内容

我们要做的第一件事是确定页面中首先呈现的部分。就我个人而言，我使用 1920 x 1080 的分辨率这样做是为了确保在优化过程中涉及到大部分用户(如果我的意思不清楚，我解释一下:在一个分辨率为 1366 x 768 的显示器上，正如大多数笔记本电脑一样，它会显示页面的某一部分；但是，如果显示器分辨率较高，渲染页面的部分将会较大，因此，如果您在低分辨率下优化了您的页面，在较大的显示器上将会出现未优化的元素，这会降低渲染速度)。

因此，将显示器分辨率设置为最大值，打开浏览器并导航到您的网页。页面完全加载后，按 F12 打开开发人员工具并取消停靠。

| 在 **Chrome** 中，要移除开发者工具，你只需点击右上角的 3 个点，然后点击下图所示的选项[![Undocking developer tools](img/b1209929bbc93ff246990ca2840111de.png)T2】](http://codingfix.com/wp-content/uploads/2017/12/Screenshot-06_30_18-11_23_23.jpg)在 Firefox 中，3 个点是水平对齐的，菜单显示了 3 个选项:“停靠到底部”、“停靠到左边”、“停靠到右边”和“独立窗口”在微软 Edge 中，你可以点击图标[![Undocking developer tools](img/495ae6140a1b61783a9183f152e5fb7d.png)T2】](http://codingfix.com/wp-content/uploads/2017/12/Screenshot-06_30_18-15_56_03.jpg) |

一旦开发人员工具被移除，您就可以看到由浏览器呈现的整个页面部分，并识别所有立即可用的部分。下一步是给浏览器所有必要的信息，以正确的方式呈现内容，这就是必须应用的 css 规则。

## 确定必要的 css 规则

确保在谷歌开发者工具窗口中激活元素标签(在 Edge 中也是一样，而在 Firefox 中这里使用的右边标签叫做‘Inspector’)。该选项卡分为两个面板:左侧是文档的 html 标记，右侧是应用于所选元素的 css 规则。只需选择页面的每个可见元素，并确保复制所有适用的规则，并将其粘贴到一个新的 css 文件中。你给这个文件起什么名字并不重要，它只是一个临时文件，我们用它来收集所有的 css 规则，我们需要这些规则来呈现上面的文件夹内容。

请记住，如果你在页面的可见部分使用了一些图标(比如 FontAwesome 图标或者 Foundation 图标),你甚至需要获取显示这些图标所需的规则！

## 缩小标题部分的 css

一旦你有了渲染上述折叠内容所需的所有规则，你需要精简它们，也就是删除临时 css 文件中多余的空间或空白行。要做到这一点，你可以使用你选择的一个插件，一个独立的程序，甚至一些在线服务。就我个人而言，我用过这个 Css minifier:它非常容易使用，不需要任何安装。只需复制你的临时 css 文件(Ctrl + A，Ctrl + C)的所有内容，并将其粘贴到左侧的“输入 css”框(Ctrl + V)中，然后单击蓝色的 Minify 按钮，缩小的 CSS 几乎会立即出现在右侧的框中。将它复制并粘贴到页面的 head 部分，紧接在最后一个 meta 标签之后，用

## 推迟下载其他资源

现在，我们必须处理我们的网页中使用的 css 的其余部分。首先，创建一个新的 css 文件，将其命名为 *style.css* 。在这个文件中，我们将忽略网页中使用的所有 css 规则。为了确保我们的新文件保留 css 规则的正确排序顺序，我们将使用网页中 css 文件链接的顺序。如果你使用 Bootstrap，你可能会在链接到任何其他 css 文件之前先链接到 Bootstrap，不是吗？很好，打开你的 bootstrap.min.css，复制全部内容并粘贴到你的 styles.css 中。对你正在使用的任何其他样式表做同样的事情，并确保从你的<头>部分删除该文件的链接。如果你的一些 css 文件还没有被缩小，使用 css 缩小器来缩小它的内容并粘贴到你的样式中。

一旦完成，你应该得到一个巨大的，不可读的 css 文件:如何处理呢？我们将编写一小段 Javascript 来确保文件只有在整个文档被加载后才会被下载。让我们开始吧。

在你的网页的页脚，在

```
<script type="text/javascript">
    function downloadCssAtOnload(){
        var d=document.head, n=document.createElement("link");
        n.type="text/css", n.rel="stylesheet",n.href="css/styles.css",d.appendChild(n)
    }
    if (window.addEventListener){
        window.addEventListener("load", downloadCssAtOnload, false);
    }else if (window.attachEvent){
        window.attachEvent("onload", downloadCssAtOnload);
    }else{ 
        window.onload = downloadCssAtOnload;    
    }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

我们来分析一下。首先我们有 downloadCssAtOnload()函数。这个函数非常简单:它只创建一个

```
<link type="text/css" rel="stylesheet" href="css/styles.css">
```

剩下的代码只是设置如何在文档加载后调用 downloadCssAtOnload()函数:根据用户的浏览器特性，我们将使用 window.addEventListener()方法、window.attachEvent()方法或 window.onload()方法。

## javascript 呢？

说得好！你可以用你的 javascript 做同样的事情。首先创建一个新的 javascript 文件，保存你的网页使用的所有 javascript(不要忘记[缩小](https://javascript-minifier.com/))。然后从你的文档页脚中移除所有的脚本元素，除了上面的小片段。最后，添加类似的代码来推迟 javascript 的加载。最终的结果应该是这样的:

```
<script type="text/javascript">
    function downloadCssAtOnload(){
        var d=document.head, n=document.createElement("link");
        n.type="text/css", n.rel="stylesheet",n.href="css/styles.css",d.appendChild(n)
    }
    function downloadJSAtOnload(){
        var d=document.createElement("script");
        d.src="js/javascript.js",document.body.appendChild(d)
    }
    if (window.addEventListener){
        window.addEventListener("load", downloadJSAtOnload, false);
        window.addEventListener("load", downloadCssAtOnload, false);
    }else if (window.attachEvent){
        window.attachEvent("onload", downloadJSAtOnload);
        window.attachEvent("onload", downloadCssAtOnload);
    }else{ 
        window.onload = downloadJSAtOnload; 
        window.onload = downloadCssAtOnload;    
    }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

新代码将在文档的页脚追加以下行:

```
<script src="js/javascript.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。现在用 PageSpeed Insights 重新测试你的页面，你应该可以看到你的分数有了相应的提高。

## 还有什么？

为了进一步提高我们的页面性能，我们还有很多工作要做，但目前只是休息一下。在这个小系列的下一篇文章中，我们将看到如何优化我们的图像以及如何设置. htaccess。

如果你有什么意见，请随意写在这里:我很高兴听到你的想法:)

帖子[提高页面速度:优先排序内容](https://codingfix.com/improving-page-speed-prioritizing-content/)最早出现在 [codingfix](https://codingfix.com) 上。