# MarkdownHelper 已更新

> 原文：<https://dev.to/burdettelamar/markdownhelper-updated--c8l>

我已经更新了 [markdown_helper 宝石](https://rubygems.org/gems/markdown_helper)。

特点:

*   文件包含
*   图像路径分辨率(新)

图像路径分辨率用绝对图像 URL 替换相对图像路径。

*   这很重要，因为在一个 gem 的文档中(在 RubyDoc.info 上)，码格式化[改变了一些文件结构](https://rubydoc.tenderapp.com/kb/frequently-asked-questions/broken-links-404-to-markdown-or-text-documents#why-are-links-broken-)，这打破了图像的相对链接。
*   对绝对 URL 的解析避免了这种破坏。