# 乳胶骨架

> 原文：<https://dev.to/michaelmior/latex-skeleton--2b35>

当我开始写一篇新论文时，我会遇到一个重复的任务，那就是设计存储库的初始结构来存储论文文本。我最近向 GitHub 推了一个简单的框架，我用它作为起点。这里没有什么特别的东西，但这是一个很好的起点。我使用 [latexmk](https://mg.readthedocs.io/latexmk.html) 来构建我所有的文档，因为它负责运行 BibTeX 和其他事情。另一个好处是，它会自动尝试使用`make`来构建任何丢失的文件。这个存储库基本上由一个生成论文的`Makefile`以及一个简单的 LaTeX 框架和一个空的 BibTeX 文件组成。希望对其他人有所帮助！