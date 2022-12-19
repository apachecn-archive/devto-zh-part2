# 保持您的示例代码绿色

> 原文：<https://dev.to/burdettelamar/keep-your-example-code-green-4b7e>

在您的文档中显示示例代码对您的用户非常有帮助——除非示例代码已经过时，或者更糟糕的是，已经损坏。

如果用户运行你的示例代码并给出错误，他们怎么能对你代码的其余部分有信心呢？

问题:怎么办？

答:在每次提交之前运行您的示例代码。(就像你的测试一样，对吗？)

问:怎么做？

答:使用构建过程和文件包含:

*   将每个示例代码片段存储在单独的文件中。
*   在主文档中，为每个示例代码及其输出指定文件包含。
*   拥有构建过程(Makefile、Rakefile、Ant 等):
    1.  执行每个示例，将其输出捕获到一个单独的文件中。
    2.  运行一个文件包含过程，将每个代码片段及其输出合并到主文档(README 等)中。

到目前为止，一切顺利。

但是在 GitHub，降价处理不支持文件包含。

无耻之徒:我的 Ruby gem [markdown_helper](https://rubygems.org/gems/markdown_helper) 是一个 markdown 预处理器，支持[文件包含](https://github.com/BurdetteLamar/markdown_helper#file-inclusion)。事实上，该项目的[首页](https://github.com/BurdetteLamar/markdown_helper#markdown-helper)本身就大量使用了这个功能。

因此，如果你的项目在 GitHub 上，你可以使用`markdown_helper`将代码和输出合并到你的 markdown 页面中。

顺便说一句:

*   你的项目不需要用 Ruby 编码。`markdown_helper`有一个非 Ruby 构建过程可以使用的命令行接口。
*   文件包含可以嵌套:您包含的文件可以包含更多的文件。

查看[用例](https://github.com/BurdetteLamar/markdown_helper/blob/master/markdown/use_cases/use_cases.md#use-cases)。