# 给 vs 代码用户的快速提示。

> 原文：<https://dev.to/sait/quick-tip-for-vs-code-users-5hlo>

你有没有在 markdown 文件中输入时犯过拼写错误，或者厌倦了在 vs 代码中输入长单词？

给你一个小提示，有一个叫做[词典补全](https://marketplace.visualstudio.com/items?itemName=yzhang.dictionary-completion)
的扩展，它可以帮助你快速给出我们正在输入的相关关键词的建议。

[![vscode auto complete](img/169b3eb47a6f94b0621bee10c4ef3490.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_kdadKB---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6nfojjt6jqpuujrlcubh.gif)

安装后，在 vs 代码中打开设置，并在工作区设置中添加以下代码。

```
{
    "[markdown]": {
        "editor.quickSuggestions": true
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

希望你喜欢快乐的编码。