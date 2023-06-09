# TIL:样式表不仅阻止呈现，还阻止 JavaScript 执行

> 原文：<https://dev.to/stefanjudis/til-stylesheets-do-not-only-block-rendering-but-also-javascript-execution-3dlm>

今天我学到了当你有了下面的片段...

```
<link rel="stylesheet" href="app.css" />

<script>
  var script = document.createElement('script');
  script.src = "analytics.js";
  document.getElementsByTagName('head')[0].appendChild(script);
</script> 
```

Enter fullscreen mode Exit fullscreen mode

...在样式表加载之前，不会执行内联 JavaScript 代码片段。考虑到这一点，这很有意义，因为 JavaScript 可能会访问元素和任何与样式相关的值。不过，我从来没想过。

哈里·罗伯特在[的一篇优秀文章](https://csswizardry.com/2018/11/css-and-network-performance/#dont-place-link-relstylesheet--before-async-snippets)中描述了这种行为，这是所有人的荣誉。谢谢哈利！