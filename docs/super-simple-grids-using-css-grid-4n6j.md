# 使用 CSS 网格的超级简单网格

> 原文：<https://dev.to/changoman/super-simple-grids-using-css-grid-4n6j>

今天我们将实验 CSS 网格，看看从头开始创建一个 12 列的网格有多简单。如果你以前使用过 CSS 框架，很可能它带有某种快速统一布局的网格系统。使用 CSS Grid，创建我们自己的 12 列网格非常简单。

这里是最终产品的预览:[http://cssgrid-demo.surge.sh/](http://cssgrid-demo.surge.sh/)

让我们从一些非常基本的 html 开始

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    My Website
  </head>
  <body>
    <div class="container">
      <div class="grid-wrapper">
        <div class="col-4">
          <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Cras suscipit ultricies vehicula. Curabitur iaculis, risus vel egestas pellentesque, magna eros fermentum massa, et eleifend sapien purus id dui. Pellentesque consectetur imperdiet dui ac tristique. Nulla non egestas dui. Nulla semper diam est. Morbi tincidunt eros non nisi pretium vehicula. Integer semper vulputate sem eu molestie. Fusce aliquet viverra est, sit amet blandit nibh tristique commodo. Nulla eget elit in lacus luctus iaculis et in ipsum. Curabitur et consectetur sapien.</p>
        </div>
        <div class="col-4">
          <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Cras suscipit ultricies vehicula. Curabitur iaculis, risus vel egestas pellentesque, magna eros fermentum massa, et eleifend sapien purus id dui. Pellentesque consectetur imperdiet dui ac tristique. Nulla non egestas dui. Nulla semper diam est. Morbi tincidunt eros non nisi pretium vehicula. Integer semper vulputate sem eu molestie. Fusce aliquet viverra est, sit amet blandit nibh tristique commodo. Nulla eget elit in lacus luctus iaculis et in ipsum. Curabitur et consectetur sapien.</p>
        </div>
        <div class="col-4">
          <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Cras suscipit ultricies vehicula. Curabitur iaculis, risus vel egestas pellentesque, magna eros fermentum massa, et eleifend sapien purus id dui. Pellentesque consectetur imperdiet dui ac tristique. Nulla non egestas dui. Nulla semper diam est. Morbi tincidunt eros non nisi pretium vehicula. Integer semper vulputate sem eu molestie. Fusce aliquet viverra est, sit amet blandit nibh tristique commodo. Nulla eget elit in lacus luctus iaculis et in ipsum. Curabitur et consectetur sapien.</p>
        </div>
      </div>
    </div>
  </body>
</html> 
```

如果您过去曾经使用过网格系统，那么这个 html 结构应该看起来相当熟悉。对于流体布局，`container`可以应用最大宽度或保持 100%。`grid-wrapper`将为我们的列开始一个部分，`col-#`代表实际的列。如果我们想要 3 个相同的列，我们将需要使用`col-4`，因为这是一个 12 列的网格。列数相加必须等于 12。

现在让我们编写 CSS 并实现 CSS 网格

```
.container {
  padding: 0 1rem;
  margin: 0 auto;
}
@media screen and (min-width: 992px) {
  .container {
    max-width: 1140px;
  }
  .grid-wrapper {
    display: grid;
    grid-gap: 0 2rem;
    grid-template-columns: repeat(12, 1fr);
    margin: 0 auto;
    width: 100%;
  }
  .col-1 { grid-column: span 1; }
  .col-2 { grid-column: span 2; }
  .col-3 { grid-column: span 3; }
  .col-4 { grid-column: span 4; }
  .col-5 { grid-column: span 5; }
  .col-6 { grid-column: span 6; }
  .col-7 { grid-column: span 7; }
  .col-8 { grid-column: span 8; }
  .col-9 { grid-column: span 9; }
  .col-10 { grid-column: span 10; }
  .col-11 { grid-column: span 11; }
  .col-12 { grid-column: span 12; }
} 
```

CSS 首先是移动的，所以任何低于 992px 的设备都不会有网格布局。如你所见，大部分的魔法都发生在`.grid-wrapper`类中。这里我们指定了`grid-template-columns`，它重复 12 个相等的列。`grid-gap`是两列之间的空间。然后，我们为每一列创建一个类，并告诉它们应该跨越多少个网格列。

我希望这段代码能够在不使用 CSS 框架的情况下创建一个快速的网格布局。随意定制和试验不同的网格列和间隙大小！

查看[https://codebushi.com/](https://codebushi.com/)获取更多我的网络开发技巧和资源。