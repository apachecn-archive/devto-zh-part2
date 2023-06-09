# 净化代码的文件名

> 原文：<https://dev.to/ssbozy/sanitizing-filenames-for-code-5g43>

我们中的很多人肯定已经预见到了这一点。文件名中的空格。没有逃避，因为每个人都不遵循理智。然而，你的工程师/开发人员会说让我来解决这个问题。有很多方法可以做到这一点。尽管我更喜欢写代码来解决我的问题，但并不是所有事情都需要代码，因为我坚信**不要重新发明轮子**。于是有了下面这个帖子。

最常见的令人头疼的问题来自文件名中的空格。从使用`tr`命令到在 MacOS 中使用*自动机*，我已经使用了所有可能的解决方案。然而，并非所有解决方案都适用于所有平台。因此，我停止使用排毒命令行工具。原因如下。

```
[~/Downloads/tmp/temp] $ tree .
.
├── file\ name\ 1.txt
├── file\ name\ 2.txt
├── file\ name\ 3.txt
└── file\ name\ 4.txt

[~/Downloads/tmp/temp] $ detox *.txt
[~/Downloads/tmp/temp] $ tree .
.
├── file_name_1.txt
├── file_name_2.txt
├── file_name_3.txt
└── file_name_4.txt 
```

Enter fullscreen mode Exit fullscreen mode

我希望这个优雅的解决方案能提高您的工作效率。快乐编码。