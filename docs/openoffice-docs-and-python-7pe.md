# OpenOffice 文档和 Python

> 原文：<https://dev.to/lukaszkuczynski/openoffice-docs-and-python-7pe>

# 用 Python 写 ODT

在创建我的小 Python 工具时，我意识到通过 print(控制台)将数据写入 txt 是不够的。为了让我的 [pinmix](https://github.com/lukaszkuczynski/pinmix) 更加有用，我想创建一种清晰呈现文本数据的方式。第一个想法是使用类和 div 创建一个包含内容的 HTML。但是，我通常使用类似 Word 的工具来编辑和修改我的文档。他们可以选择发表评论等等。总的来说，这是管理文本文档更简单的方式，对吗？那么如何用 Python 创建 OpenOffice doc 呢？使用`odfpy`模块是如此简单。

# 解

这基本上就是我用 Python 做的。你只需导入一些库，瞧，编辑愉快。定义了单独的样式后，您就可以批量更新所有相似的文本部分了。这听起来像是快速文档改编，符合您的所有需求。

```
from odf.opendocument import OpenDocumentText
from odf.style import Style, TextProperties
from odf.text import H, P, Span

class DocFactory:

    def __init__(self, filename):
        self.filename = filename
        self.quotStyle = Style(name="Quotations")
        self.marginaliaStyle = Style(name="Marginalia")
        self.doc = OpenDocumentText()

    def add_p1_text(self, txt):
        self.doc.text.addElement(P(text=txt))

    def add_p2_text(self, txt):
        self.doc.text.addElement(P(text=txt, stylename=self.quotStyle))

    def add_p3_text(self, txt):
        self.doc.text.addElement(P(text=txt, stylename=self.marginaliaStyle))

    def save(self):
        self.doc.save(self.filename) 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

我再一次明白，在你的工具集中有了 Python，你总是离高级解决方案只有几行之遥。