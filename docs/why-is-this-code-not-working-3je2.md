# 为什么这段代码不起作用？

> 原文：<https://dev.to/dimitri_acosta/why-is-this-code-not-working-3je2>

几天前，我看到有人问为什么这段代码没有到达第三个`var_dump`，所以我试着在我的电脑上执行这段代码，所有不同版本的 PHP 都可用，但没有一个能运行，那么，这里发生了什么？

我已经尝试了很多方法来改变代码，但它从来没有达到第三个`var_dump`，这让我发疯

```
<?php

$test = 9.99;
if ($test == 9.99) {
    var_dump($test);
}

$test = $test + 30;
if ($test == 39.99) {
    var_dump($test);
}

$test = $test + 30;
if ($test == 69.99) {
    var_dump($test);
} 
```

Enter fullscreen mode Exit fullscreen mode