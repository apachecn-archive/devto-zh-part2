# 如何为 Jekyll 博客设置 Atom

> 原文：<https://dev.to/jerr/how-to-set-up-atom-for-jekyll-blogging--29m5>

我喜欢使用 [Atom](https://atom.io/) 作为一个相对轻量级的代码编辑器。我也非常喜欢使用像 Jekyll 这样的静态网站生成器，用 Markdown 编写对我来说很自然。如果你在 [dev.to](https://dev.to) 上写一篇文章，你也会看到与 YAML front matter 相同的降价。这篇短文将帮助您设置 Atom，以便在支持 YAML 前沿事务的情况下进行 Markdown 编辑和预览。

## 先决条件

显然你需要安装 Atom T1，但是你也需要安装 T2 pandoc T3。

## 原子包

我尝试了几种不同的软件包组合，最终找到了正确的组合。我相信以下是设置 Atom 以支持降价所需的全部内容:

*   [语言-降价](https://atom.io/packages/language-markdown) -降价语法支持
*   [降价-预览-plus](https://atom.io/packages/markdown-preview-plus) -预览降价并实时更新

## 配置

不需要太多的配置，但是默认情况下 Jekyll front matter 不能很好地解析默认设置。在 Markdown Preview Plus 的设置中，执行以下操作:

1.  启用 Pandoc 解析器
2.  设置减价风味:

```
markdown-raw_tex+tex_math_single_backslash+yaml_metadata_block 
```

Enter fullscreen mode Exit fullscreen mode

减价预览加设置应该是这样的:
[![Settings screenshot](../Images/fe2a06f13bcd767e86e57beaa04b4843.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fqhHPzXT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tm8hih3cpehgnk7tzlow.png)

让我知道，如果我错过了什么，或者如果你有更好的设置，你想分享😉