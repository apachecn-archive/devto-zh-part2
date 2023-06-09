# 更多关于降价的帮助？

> 原文：<https://dev.to/burdettelamar/more-help-with-markdown--36i7>

我放的 [markdown_helper gem](https://rubygems.org/gems/markdown_helper) (到目前为止)只做了一件事:

*   支持文件包含。

我想到了另外两个特点:

*   支持图片的相对链接(见下文)。
*   支持幻灯片风格的降价页面(由下一页/上一页导航链接的页面)。

查询:还有什么有用的吗？

关于图像的相对链接:它们在 GitHub markdown 中工作良好，但是当项目形成一个 gem 时，链接在 gem 的文档中被破坏了。那是因为在 RubyDoc.info 上， [YARD 重新整理了](https://rubydoc.tenderapp.com/kb/frequently-asked-questions/broken-links-404-to-markdown-or-text-documents#why-are-links-broken-)一些文件和文件夹。

解决方法是替换`raw.githubusercontent.com`中文件的绝对链接。这将是非常不方便的，更不用说是乏味的和可能容易出错的。

我想给 helper 添加的是一个自动用绝对链接替换相对链接的方法。这样，我们在 GitHub 上有相对链接的便利，在 RubyDoc 上有正确的(绝对)链接。