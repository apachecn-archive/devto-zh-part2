# HTML 来 minitest_lucid

> 原文：<https://dev.to/burdettelamar/html-comes-to-minitestlucid-3ab>

正如我之前所写的，我一直在考虑给 [minitest_lucid](https://rubygems.org/gems/minitest_lucid) 添加 HTML 输出，以利用链接和颜色来组织比较。

我已经开始用`Set`做实验，因为它最简单:

*   当前[清晰文本](https://github.com/BurdetteLamar/minitest_lucid/blob/master/markdown/readme/set/assert_equal/lucid.txt)。
*   实验 [HTML](https://burdettelamar.github.io/minitest_lucid/markdown/readme/set/assert_equal/t.html#Missing%20(Expected%20-%20Actual)) 。

和以前一样，如果你比较的是小物体，这无关紧要。但是想象一下这个输出，比如说，一千个项目。

更复杂的情况将是`Array`，这还在后面。

是的，我确实没有料到事情会如此复杂，但是“一不做，二不休”