# 一个节点模块，返回一组被谷歌禁止的恶语和顶级脏话。

> 原文：<https://dev.to/robertjgabriel/a-node-module-that-returns-an-array-of-bad-words-and-top-swear-words-banned-by-google-2jc5>

谷歌无意中创建了一个 API，第三方开发者可以用它来检查用户输入的单词是否可能具有攻击性。这是现在关闭，是谷歌的一部分，你爱什么？网站。所以我在它关闭之前就把它废弃了，现在它在一个节点模块中，供人们在项目中使用。希望你喜欢！！

## 安装

```
$ npm install --save google-profanity-words 
```

Enter fullscreen mode Exit fullscreen mode

## 用法

```
const googleProfanityWords = require('google-profanity-words');

googleProfanityWords.list(); //  [ '5h1t','5hit','a55','anal','anus',etc.....]  Returns a array of Profanity. 350 
```

Enter fullscreen mode Exit fullscreen mode

您可以关注我这里和下面的更多代码和项目。

*   **[推特](https://twitter.com/RobertJGabriel)** 或者
*   **[Github](https://github.com/RobertJGabriel) 。**