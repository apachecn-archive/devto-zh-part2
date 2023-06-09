# 使我的框架成为一个 Composer 框架

> 原文：<https://dev.to/sbebbers/making-my-framework-a-composer-framework-4di4>

在过去两年左右的时间里，我一直在开发一个 PHP 7 框架，首先作为一个教学工具，然后用于商业项目。我的一个同事告诉我，我可以将它添加到 Composer 中，我很少使用它，因为我发现在我工作的使用 Composer 的项目中，那些项目往往安装了在任何地方都不使用的 Composer 包。这也让他们变得臃肿，变得更加肥胖。许多 Composer 包我可以很容易地自己构建，这样我就可以控制脚本的源代码和质量。最后，通过为自己解决问题，我学到了更多。

无论如何，除此之外，Composer 会给我的框架更多的曝光。那么，我该如何将它添加到 Composer 中呢？

我的框架目前位于

#### 【https://github.com/sbebbers/FrameWork.php/releases】T2

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[sbebbers](https://github.com/sbebbers)/[FrameWork.php](https://github.com/sbebbers/FrameWork.php)

### 一个简单的面向对象 PHP 框架，作为学习工具构建，但足够稳定，可以在实际产品上使用。

<article class="markdown-body entry-content container-lg" itemprop="text">

# FrameWork.php

### 由肖恩·贝宾顿和林登·布瑞恩开发

#### 特别感谢 Aaron Zvimba 的帮助、建议和支持

当我在斯塔福德郡 Burton-Upon-Trent 的 Rushcliff Ltd 工作时，为了指导一名初级 PHP 开发人员，FrameWork.php 以一个简单而又相当混乱的 OO MVC 框架起家。想法是将一个控制器链接到每个页面，并且每个控制器有一个模型，这样控制器类将在必要时充当前端视图和数据库之间的中间人。

我还认为有必要在页面 uri 别名上允许许多文件扩展名，例如，在所提供的框架中，可以使用以下任何一种:

*   [http://framework.local/home](http://framework.local/home)
*   [http://framework.local/home.php](http://framework.local/home.php)
*   [http://framework.local/home.aspx](http://framework.local/home.aspx)
*   [http://framework.local/home.htm](http://framework.local/home.htm)
*   [http://framework.local/home.html](http://framework.local/home.html)

这个想法是为了掩盖它是一个 PHP 框架，并用。aspx 文件扩展到我们的笔测试。我不知道这是否…

</article>

[View on GitHub](https://github.com/sbebbers/FrameWork.php)

先谢过了。