# 用引导程序创建侧导航菜单

> 原文：<https://dev.to/michaeljota/creating-a-sidenav-menu-with-bootstrap-cje>

到目前为止，我写大多是关于 Angular 和 Typescript，这不会改变。但是今天，我将带给你一个关于如何在 Bootstrap 中做一个简单而有效的 Sidenav 的帖子。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [迈克尔约塔](https://github.com/michaeljota) / [自举——举例](https://github.com/michaeljota/bootstrap-examples)

### 一些基于 Bootstrap 4 的组件示例

<article class="markdown-body entry-content container-lg" itemprop="text">

# 引导示例

这是一个小小的回购，只是为了展示我用 Bootstrap 制作的一些组件。每个元素都有一个单独的分支。

但是请注意，我将只使用引导。jQuery 和 Pooper。JS 也包括在内，但只是因为 Bootstrap 需要它们。

## 如何使用

你需要`node`来做这个。无论什么版本[包裹](https://parceljs.org/)支持。

*   `npm install`
*   切换到您想要检查的分支
*   `npm start`

因为我对所有的例子都使用了相同的依赖关系，所以在切换分支时不需要重新安装。

</article>

[View on GitHub](https://github.com/michaeljota/bootstrap-examples)

在这个 repo 中，您将找到包含所有最终代码的`sidenav`分支。

# 什么是 Sidenav？

Sidenav 是一个可折叠的组件，用来放置导航内容。Bootstrap 有一个 Navbar 菜单组件，用于某种相同的目的，将导航组件分组，默认情况下在 Navbar 中显示它们，在 mobile 中隐藏它们，并在与 manu 按钮交互时切换它们。

默认情况下，Bootstrap 在导航栏下面显示菜单，推送内容以腾出空间。取而代之的是侧导航，覆盖内容，有时有背景，但这次没有。

# 第一步

我想你有一个引导项目设置。如果不这样做，就可以自由地使用回购的`master`分支之一来启动。

我将使用来自 Bootstrap 的相同代码。你可以阅读 Navbar 组件的文档，并将从[开始截取的内容复制到这里](https://getbootstrap.com/docs/4.1/components/navbar/#supported-content)

如果屏幕很大，默认情况下你应该有一个带有导航条的页面显示所有的导航项，否则就折叠它们。此外，当菜单折叠时，您应该能够切换菜单。

对于这个例子，我将首先测试一个额外的小屏幕，所以用一个`.navbar-expand-sm`替换`nav`元素中的`.navbar-expand-lg`类。这将指示引导，默认情况下，对于任何比小屏幕大的屏幕，导航条都应该展开。

所以，打开你的 DevTools，把设备设置成特别小的东西，比如 iPhone 5S。我创建了另一个文件来放置 sidenav 样式，但是你可以把代码放在任何你想放的地方。创建一个`.sidenav`类，并用`.navbar-collapse`将其添加到`div`元素中。

# 创建侧导航

我们希望在显示时做一些与屏幕重叠的事情，并且我们可以利用默认情况下有一个`position: relative`的`navbar`组件。所以我们可以使用`position: absolute`来放置它。

```
.sidenav {
  position: absolute;
} 
```

Enter fullscreen mode Exit fullscreen mode

你应该看到有东西在动，但实际上你看不到它是什么。为了能够看到所有的元素，您需要设置位置。这真的是一个品味的问题，但是我会把菜单放在导航条下面，然后展开它来显示所有可用的屏幕。

```
.sidenav {
  position: absolute;
  top: 100%;
  height: calc(100vh - 100%) !important;
  left: 0%;
  width: 100%;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在您应该可以看到菜单是如何出现和消失。这是因为 Bootstrap 应用了一个`.show`类，当不在元素中时，元素有一个`display: none`。但是我们也可以用它来达到我们的目的，从左到右显示菜单。高度中的`!important`是因为 Bootstrap 用自定义高度修改了元素的样式属性。

```
.sidenav {
  position: absolute;
  top: 100%;
  height: calc(100vh - 100%) !important;
  left: 0%;
  width: 100%;
}

.sidenav:not(.show) {
  left: -100%;
} 
```

Enter fullscreen mode Exit fullscreen mode

这似乎没有任何效果。但是，实际上元素是从左向右移动的。我们只是没有看到动画。我还在学习动画，这就是为什么对于这个简单的例子，我将使用`transition`属性。

```
.sidenav {
  position: absolute;
  top: 100%;
  height: calc(100vh - 100%) !important;
  left: 0%;
  width: 100%;
  transition: left 0.35s;
}

.sidenav:not(.show) {
  left: -100%;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你有了。好看的侧导航。这并不难。我确实添加了一些东西来使它看起来更好，填充和背景颜色，和父元素一样。

```
.sidenav {
  position: absolute;
  top: 100%;
  height: calc(100vh - 100%) !important;
  left: 0%;
  width: 100%;
  transition: left 0.35s;
  padding: 2rem;
  background-color: inherit;
}

.sidenav:not(.show) {
  left: -100%;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，在 DevTools 设备列表中设置一个更大的屏幕，比如 iPad。

> 什么？你弄坏了我的应用。

好吧，首先我没说要用真正的 app。第二，

# 定格大屏幕

你能做的最好的事情就是设计小屏幕，然后再为更大的屏幕修改它。但这真的好像是 mees up。Bootstrap 所做的，以及我们将要做的，是将该类包装在媒体查询中。您可以先做我做的事情，然后使用与您用于`.navbar-expanded-{media query}`相同的媒体查询。

```
@include media-breakpoint-down(sm) {
  .sidenav {
    position: absolute;
    top: 100%;
    height: calc(100vh - 100%) !important;
    width: 100%;
    left: 0%;
    transition: left 0.35s;
    padding: 2rem;
    background-color: inherit;
  }

  .sidenav:not(.show) {
    left: -100%;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这有一个缺点，现在要修改断点你就要调整`.navbar-expand-*`类，还有函数中的断点。优点是你不需要引导源文件，如果你做的很快，那么这可能是你最好的选择。

或者，您可以使用 Bootstrap 使用的相同功能。请注意，这是一个私有函数，它目前没有被导出。不过我们可以用`copy-paste`的经典手法。

```
.navbar-expand {
  @each $breakpoint in map-keys($grid-breakpoints) {
    $next: breakpoint-next($breakpoint, $grid-breakpoints);
    $infix: breakpoint-infix($next, $grid-breakpoints);

    &#{$infix} {
      @include media-breakpoint-down($breakpoint) {
        .sidenav {
          position: absolute;
          top: 100%;
          height: calc(100vh - 100%) !important;
          width: 100%;
          left: 0%;
          transition: left 0.35s;
          padding: 2rem;
          background-color: inherit;
        }

        .sidenav:not(.show) {
          left: -100%;
        }
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个循环块目前可以在第 140 行的`_navbar.scss`文件中找到。它所做的是将来自`.navbar-expand-*`的所有媒体查询关联到相应的媒体查询。有了它，你可以使用任何你想要的媒体查询，只需要更新那个类。

# 那都是乡亲们！

感谢阅读，希望你觉得有趣。我不打算将此作为 npm 包发布，因为我认为这非常固执己见。例如，这个例子使用了`.navbar`组件的`position: relative`缺省值，所以如果您想应用另一个位置作为固定的或粘性的，那么您必须更新所有的位置属性。也许你想在你的`.navbar`中使用浅色主题，但是在`.sidenav`中你会使用深色主题。同样，您需要根据自己的需要进行修改。然而，我认为这是一个非常简单的开始方式，并且随着你的移动来调整。