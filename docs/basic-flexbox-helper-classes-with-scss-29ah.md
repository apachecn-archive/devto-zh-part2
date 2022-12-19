# SCSS 的基本 flexbox 助手类

> 原文：<https://dev.to/wiaio/basic-flexbox-helper-classes-with-scss-29ah>

Flexbox 是一个非常强大的 CSS 布局规范。虽然 flexbox 有时用于大规模的页面布局，但它最适合于小规模的布局，如单个组件。如果你以这种方式使用 flexbox，你可能会发现自己一遍又一遍地编写相同的规则。我们将看看几个基本的助手类，它们可以使 flexbox 更快地添加到您的组件中。这些类并没有提供所有可能的 flexbox 规则，只是我发现我最常用的基本规则。我发现像这样的类通常对一些小事情很有用，比如匹配一行元素的高度，以及将它们的内容垂直居中。

```
I'm using SCSS here for brevity, but you could easily write the same classes in regular CSS.

.flex-parent { // Add this class to create a flexbox parent
  display:flex;
  &.flex-direction-column { // This changes the layout direction from the default horizontal to vertical
    flex-direction:column;
  }
  &.justify-content-center { // This centers the parent's children along the main axis (horizontal by default, vertical if the parent has the  flex-direction-column class)
    justify-content:center;
  }
  &.align-items-center { // This centers the parent's children along the cross axis
    justify-content:center;
  }
  &.flex-wrap-wrap { // This allows the children to wrap as needed
    flex-wrap:wrap;
  }
  @for $i from 1 through 12 { // Here we loop through numbers 1 to 12, generating flex child classes for each number, eg. .flex-1, .flex-2 etc.
    .flex-#{$i} {
      flex:$i;
    }
  }
}
Here is an example of a component layout using our flexbox helper classes:

<div class="flex-parent">
  <div class="flex-1 flex-parent flex-direction-column justify-content-center">1</div>
  <div class="flex-2 flex-parent flex-direction-column justify-content-center">2</div>
  <div class="flex-3 flex-parent flex-direction-column justify-content-center">3</div>
  <div class="flex-4 flex-parent align-items-center">
    <div class="flex-parent flex-direction-column">
      <div class="flex-1">row 1</div>
      <div class="flex-1">row 2</div>
      <div class="flex-1">row 3</div>
    </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

关于 flexbox 厂商前缀的信息，点击[这里](http://ptb2.me/flexbox/)，但是我强烈推荐使用像 [Autoprefixer](https://github.com/postcss/autoprefixer) 这样的工具，所以你不需要担心它们。