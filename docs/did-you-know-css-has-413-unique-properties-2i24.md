# 你知道 CSS 有超过 400 个独特的属性吗

> 原文：<https://dev.to/sadick/did-you-know-css-has-413-unique-properties-2i24>

我也是做了这个才知道的。

```
var element = document.createElement("div");
var count = 0;
for (var index in element.style) count++;
console.log(count); // 413 
```

Enter fullscreen mode Exit fullscreen mode