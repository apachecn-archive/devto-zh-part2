# 发展中的牛哲基尔

> 原文：<https://dev.to/gonsie/developing-ox-jekyll-hn6>

在之前的一篇文章中，我调查了现有的 org 和 jekyll 集成工作流。这项调查发现了两种常见的方法，一种是以 Jekyll 为中心的，另一种是使用 Org-publish(以 Emacs 为中心)。不幸的是，两者都不适合我的工作流程，我决定开发自己的 Org exporter。

我终于有了一些功能代码，所以我想我应该分享一下这个项目和开发过程的细节。你可以在 GitHub 上找到项目[。](https://github.com/gonsie/ox-jekyll)

## 起点

在我的调查中，我找到了一些有用的资源:

*   关于[组织导出引擎](https://orgmode.org/worg/dev/org-export-reference.html)的工作文件；
*   一个现存的牛哲基尔在 [org-octopress](https://github.com/yoshinari-nomura/org-octopress) 。

> 伟大的艺术家偷窃。

我想要 org-octopress 用它的 jekyll exporter 所做的一切，我只是希望它建立在 markdown 而不是 HTML 上。

所以，我已经复制了`ox-jekyll.el`，把“html”换成了“md”。固定了几个函数名之后，就管用了！

最重要的是，前面的内容在正确的位置(有和没有目录)。下一步是用我的发布工作流程的细节来完成这项工作。

## 我的发布工作流程详情

我希望我的出口商完成许多事情。其中许多是我的工作流程所特有的。

*   我在每篇文章中使用一个组织文件。
*   导出时，应该将日期(YYYY-MM-DD)添加到文件名中。
*   子树级别匹配导出标题级别(这就是我如何设计我的网站的 CSS)
*   代码片段应该易于使用适当的语法导出。这是许多 markdown 转换器的不同之处，但 jekyll 目前用`{ % highlight LANG %}` / `{ % endhighlight %}`包装代码块。[忽略第一个%]前的空格。
*   Jekyll 前面的内容应添加在 org-inserted 目录的上方。手动 HTML 源代码块实在是太可怕了。
*   将生成的文件移动到一个单独的位置应该很容易(我认为这在 org-publish 工作流中已经解决了)。
*   给文章添加标签的方法。
*   安全导出标题(冒号字符搞乱了 Jekyll 的事情)。
*   修复链接并导出连接的图像。这个真的很不错，没有必要。
*   TOC 应该是一个有序列表(带有使其无序的选项)。WTF 是 HTML 导出器使其成为一个文本中有数字的无序列表。

这些问题中的许多都用 org-octopress 的代码解决了，特别是 yaml 前端的问题。

## 实现功能

下面是我的实现的一些注释。

### 思考目录

我在每篇文章的顶部都放了一个目录。我实际上并没有给它贴上“目录”的标签，我更喜欢一个没有编号的列表。这似乎与 org-exporter 喜欢做的事情非常相反。它几乎不允许对目录的显示方式进行任何细粒度的控制。

虽然我能够得到我想要的代码，但它最终变得太简单了。最后，我决定不要目录。实际上，如果有必要，将由 markdown / jekyll / liquid 解析器来创建 TOC。这使得向 TOC 应用单独的 HTML 样式变得容易(把它放在侧边栏或其他地方)。

### 保持标题级别

我希望`:headline-offset`(如这里描述的)在文件级别上工作。这允许我确保我的帖子的内容与网站上的 CSS 相匹配(内容的最终目的地)。这绝对是一种比 org 更廉价的做事方式。

不幸的是，`:headline-offset`选项只支持每个子树。我可以用一个简单的函数来破解它，这个函数给每个子树添加一个“偏移 0”的属性:

```
(defun org-jekyll-headline-offset (headline contents info)
  "proper headline offset"
  (let* ((info (plist-put info :headline-offset 0)))
    (org-md-headline headline contents info))) 
```

Enter fullscreen mode Exit fullscreen mode

我把它添加到派生的后端`:translate-alist`，就像这样:

```
(org-export-define-derived-backend 'jekyll 'md
  ;; ...
  :translate-alist
  '((headline . org-jekyll-headline-offset)
  ;; ... 
```

Enter fullscreen mode Exit fullscreen mode

现在，每次导出引擎翻译标题时都会调用此函数，并且我的标题级别会直接转换为 markdown。

### 设定日期

我有一个奇怪的工作流程，我希望`#+DATE:`标记是我开始写文章的日期，文件名中的日期是我点击发布的日期。对于我的导出器，我可以使用选项`date:`来触发这个行为。`date:`选项优先于变量`org-export-with-date`。

虽然我希望我的代码支持将`#+DATE:`属性放入文件名，但这对于导出引擎来说似乎是不可能的。date 属性存储在 info plist 中，在文件命名时我无法访问它。

相反，我实现了这个功能:

*   `date:`选项控制是否将`#+DATE`属性的值放入 Jekyll front matter。
*   我给出口商增加了一个新选项:`org-jekyll-use-today-date`。如果`t`，导出器将在文件名前加上今天的日期。

## 结果

我对我的导出器很满意，我甚至用它导出了这篇文章！我仍然需要弄清楚如何使用 org-publish 工作流，但是现在我可以成功地导出我的博客文章。

和我的大多数项目一样，你可以在 GitHub 上找到它[。请随时提出问题并分享您的任何反馈。](http://github.com/gonsie/ox-jekyll)