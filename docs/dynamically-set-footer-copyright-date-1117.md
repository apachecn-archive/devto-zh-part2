# 动态设置页脚版权日期

> 原文：<https://dev.to/chrisotto/dynamically-set-footer-copyright-date-1117>

随着 2018 年接近尾声，我发现自己需要留一个便条来更新我网站上的页脚，将年份改为 2019 年。我再也不会年复一年地静态修改页脚中的版权年份了。那么为什么不动态设置呢？

```
<script>
  document.write(new Date().getFullYear())
</script> 
```

Enter fullscreen mode Exit fullscreen mode

除了添加年份，我还在新剧本中加入了版权字符和我的名字。

```
©
<script>
  document.write(new Date().getFullYear())
</script>
Chris Otto 
```

Enter fullscreen mode Exit fullscreen mode