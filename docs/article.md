# 4 年前，我挑战自己编写最小最快的 markdown 解析器

> 原文: [https://dev.to/adhocore/4 年前，我挑战自己写最小最快的 markdown-parser-4i0](https://dev.to/adhocore/4-years-ago-i-challenged-myself-to-write-smallest-and-fastest-markdown-parser-4i0)

四年前，我挑战自己为 html 解析器编写最小最快的 markdown，几天后就实现了。虽然它没有完全实现所有的降价规格，但在许多情况下仍然是有用的。

我把它命名为 **htmlup** ！打包在一个文件、一个类和一个方法中，它少于 220 sloc。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [【粘液】](https://github.com/adhocore) / [ htmlup](https://github.com/adhocore/htmlup)

### 轻量级快速降价解析器

<article class="markdown-body entry-content container-lg" itemprop="text">

## ad hoc core/html up

[![Latest Version](img/3b2c36df4d9095de25f54353e78ccd06.png)](https://github.com/adhocore/htmlup/releases)[![Travis Build](img/7dd3f8d5f236e35da6931f8cf23cf00a.png)](https://travis-ci.org/adhocore/htmlup?branch=master)[![Scrutinizer CI](img/0c749c13687c1f680247f8499cb006b2.png)](https://scrutinizer-ci.com/g/adhocore/htmlup/?branch=master)[![Codecov branch](img/78fc1d73a37dfef1cbf36788ee9ddd81.png)](https://codecov.io/gh/adhocore/htmlup)[![StyleCI](img/06e7e816a8c31cd4466bc8c7444ec6a9.png)](https://styleci.io/repos/20793745)[![Software License](img/bef0dd78b2319f6aa43444906a81e982.png)](https://github.com/adhocore/htmlupLICENSE)[![Donate 15](img/b2c2f94625d612f1d6f170d10bb8de6d.png)](https://www.paypal.me/ji10/15usd)[![Donate 25](img/55169c512eb5e340a3267ace0ef15ff2.png)](https://www.paypal.me/ji10/25usd)[![Donate 50](img/f0d5a67bfcb273a3a2b8548db2a580d6.png)](https://www.paypal.me/ji10/50usd)[![Tweet](img/3aedcf72e57e3736cd6ea05d11dfd7db.png)](https://twitter.com/intent/tweet?text=Lightweight+and+fast+markdown+to+HTML+parser+for+PHP&url=https://github.com/adhocore/htmlup&hashtags=php,markdown,markdownparser,phpmarkdown)

`htmlup`是用 PHP **概念**编写的超轻量级和超快速的 markdown to html 解析器——它将 markdown 拆分成行，并逐个解析标记，最后在标记上应用 markdown 语法，它支持大多数 markdown，如[规范](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet "cheatsheet")。

# 装置

运行`composer require adhocore/htmlup`

# 使用

```
<?php

use Ahc\HtmlUp;

// require '/path/to/vendor/autoload.php';

// Defaults to 4 space indentation.
echo new Ahc\HtmlUp($markdownText);

// Force 2 space indentation.
echo new HtmlUp($markdownText, 2);

// Also possible:
echo (new Htmlup)->parse($markdownText);
```

Enter fullscreen mode Exit fullscreen mode

# 特征

## 嵌套

它对深层嵌套元素提供有限的支持，支持的项目有:

*   列表中的列表
*   块代码中的块引号
*   块引号内的列表

## raw html

你可以放入你的原始 html，但是在开头和结尾要有一个空行来分隔块，就像…

</article>

[View on GitHub](https://github.com/adhocore/htmlup)

你可以在这里查看初始工作版本

尽管当前的版本有点冗长，但它仍然带有相同的解析核心概念:

> 1.  Split markdown into lines and parse it line by line like a human.
> 2.  Apply block element rules where applicable to obtain html
> 3.  Apply any spanning element rule. Finally

***奖励:*** 也支持表格语法:)

PS:在你问之前，我知道它的缺点，我不建议在任何严肃的事情上使用它。

如果它鼓励你从小的和最基本的角度考虑更大的问题，或者如果它鼓励你在不同的平台上实现一个端口，那将是非常棒的。

感谢阅读！:)