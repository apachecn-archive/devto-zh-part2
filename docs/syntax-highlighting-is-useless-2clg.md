# 语法高亮是没有用的

> 原文：<https://dev.to/dmerejkowsky/syntax-highlighting-is-useless-2clg>

...至少在降价文件方面是这样；)

*最初发表于[我的博客](https://dmerej.info/blog/post/syntax-highlighting-is-useless/)。*

# 简介:在 Neovim 中编辑此博客

以下是我在 Neovim 上编辑一篇文章时的样子:

[![Colored markdown file in Neovim](img/6806469e2b6f56000cf6c49bb4e4f926.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ewAzYQNa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dmerej.info/blog/pics/markdown-colored.png)

快速看一下。你看出什么问题了吗？

*   首先，前面物质中关于`summary`的线是绿色的。前面的内容使用了 YAML 语法，但是 Neovim 不知道它，因此错误地突出显示了这一行。

*   第二，python 代码可以，但是语言名拼错了:`pyton`而不是`python`。我*正在*使用 Neovim 的内置拼写检查器，但是由于 Neovim“知道”三个反勾号后的内容是源代码，所以它禁用了这部分文本的拼写检查。

*   第三，到“使用 CMake 和 Ninja”的链接是使用 [hugo 语法](https://gohugo.io/functions/ref/)构建的，如果你格外小心，你可能会注意到`ref`之后的文章路径以双引号(`"`)开始，但以单引号(`'`)结束。

# 关闭语法高亮显示

很长一段时间我都没怎么考虑过这种情况，直到我读到了莱纳斯·奥克松的《反对语法高亮显示的案例 》。

所以有一天我决定试着关闭语法高亮:

[![Un-colored markdown file](img/e832868e1070b89c565718d917f503a1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h0U0JnA---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dmerej.info/blog/pics/markdown-plain.png)

*   这一次，拼写检查器检查了*的所有内容*，并且发现了`pyton`中的错别字。
*   前面事的“渲染 bug”没了。
*   此外，单引号的问题更容易发现。不太可能被所有的颜色分散注意力。这可能是主观的:但这是我的直觉告诉我的。

还有一件事:我还会在 Neovim 窗口旁边的浏览器中查看呈现的 HTML。Hugo 每次保存后都会重建我正在编辑的页面，浏览器会自动重新加载。

这有几个后果:

*   第一，高光的某些部分是多余的。我可以在浏览器中看到斜体，我不需要*也不需要*在编辑器中查看它们。

*   其次，`{{ref`用法的问题被`hugo`进程本身抓住了:

```
ERROR 2018/10/04 16:36:23 error processing shortcode
  "_internal/shortcodes/ref.html"
  for page "post/0087-demo-syntax-highlight.md":
    template: _internal/shortcodes/ref.html:1:89:
    executing "_internal/shortcodes/ref.html" at <0>:
    invalid value; expected string 
```

让我们想想我们学到了什么:

*   语法高亮是有问题的，它永远不会像 HTML 渲染那样好。(尤其是 Markdown，每个渲染工具都有自己的特点)。
*   它“隐藏”了重要的信息，比如拼写错误

那么，我们为什么还要继续使用它呢？

# 结论

目前，我只是关闭了 Markdown 文件的语法。下面是我的`init.vim`文件中的相关部分:

```
augroup textfiles
  autocmd!
  autocmd filetype markdown :setlocal spell spelllang=en | syntax clear
augroup end 
```

在其他情况下，我仍然使用语法高亮。

但是，同样地，当可以使用 HTML 渲染器时，语法高亮对于编辑 Markdown 可能是无用的(甚至是有害的)，如果可以直接使用[linter](https://dmerej.info/blog/post/lets-have-a-pint-of-vim-ale/)来检查正在键入的代码**，那么语法高亮*也可能*无用。**

 **思考的食粮…

* * *

我很想听听你有什么要说的，所以请随时在下面留下你的评论，或者查看我的联系页面了解更多与我联系的方式。**