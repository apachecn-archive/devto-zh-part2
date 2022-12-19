# TIL:减少 Bootstrap4 中的断点

> 原文：<https://dev.to/terrierscript/til-reduce-breakpoints-in-bootstrap4-3pj0>

Bootstrap4 默认有 5 个断点(xs，sm，md，lg，xl)。

但是很难控制所有的断点。

我发现用[网格轮胎](https://getbootstrap.com/docs/4.0/layout/grid/#grid-tiers)定制可以减少到 2 个断点。

```
 $grid-breakpoints: (
  xs: 0,
  sm: 576px,
  md: 576px,
  lg: 992px,
  xl: 992px
);

$container-max-widths: (
  sm: 540px,
  md: 540px,
  lg: 960px,
  xl: 960px
);

@import "bootstrap"; 
```

Enter fullscreen mode Exit fullscreen mode