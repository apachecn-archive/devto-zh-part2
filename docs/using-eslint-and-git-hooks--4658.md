# 使用 ESlint 和 Git 挂钩

> 原文：<https://dev.to/tailomateus/using-eslint-and-git-hooks--4658>

你发送过多少次错误百出或者完全不符合模式的推送请求？该提交在办公时间结束前 5 分钟发送。这种情况可能发生在任何人身上，不管你是初学者还是学长。这取决于我们去改善我们的局限和缺乏关注。这篇文章的目的是在这件事上帮助你。

## 创建 package.json

在我们继续之前，让我们通过运行:
来创建 package.json 文件

```
npm init –y 
```

Enter fullscreen mode Exit fullscreen mode

了解更多:
[使用 package . JSON](https://docs.npmjs.com/getting-started/using-a-package.json)
[NPM-init](https://docs.npmjs.com/cli/init)

## 安装 ESlint

ESlint 是由 Nicholas C. Zakas 于 2013 年创建的 JavaScript 代码解析器。本质上，ESlint 允许开发人员发现问题并创建他们自己的开发规则和标准。它是用 Node.js 编写的，可以很容易地通过 npm 安装。

```
npm install eslint --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

编辑配置文件:

```
./node_modules/.bin/eslint --init 
```

Enter fullscreen mode Exit fullscreen mode

选择“使用流行风格指南”选项，然后选择您公司选择的风格指南。

在“JavaScript”下选择文件格式。如果一切顺利，将会创建. eslintrc.js 文件..

## 测试 ESlint

创建一个名为 main.js 的文件，并将以下代码放入其中:

```
a = 10
const b = 5;
b = 10 
```

Enter fullscreen mode Exit fullscreen mode

在阅读代码时，我们可以意识到一些错误会发生。但是让我们通过执行:
来测试 ESlint 的行为

```
./node_modules/.bin/eslint *.js 
```

Enter fullscreen mode Exit fullscreen mode

现在只要修复错误:D

了解更多:
[文档 ESlint](https://eslint.org)
[演示 ESlint](https://eslint.org/demo/)
[在崇高文本上设置 ESlint 3](https://medium.com/@junshengpierre/making-the-switch-from-jshint-to-eslint-5b6c4fa3c92a)

## 使用 npm 脚本

在 package.json 文件中，替换以下代码段:

```
“scripts”: {
    “lint”: “./node_modules/.bin/eslint *.js”
} 
```

Enter fullscreen mode Exit fullscreen mode

在终端中运行:

```
npm run lint 
```

Enter fullscreen mode Exit fullscreen mode

了解更多:
[为什么是 npm 脚本？](https://css-tricks.com/why-npm-scripts/)

## Git 挂钩

它们是在任务之前或之后做一些事情的脚本，例如，在提交做一些事情之前。

安装哈士奇:

```
npm install husky@next --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

要使用，让我们在 npm 脚本中添加 prepush 命令:

```
“scripts”: {
    “lint”: “./node_modules/.bin/eslint *.js”,
    “prepush”: “lint”
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们发送推之前，它将运行棉绒。

了解更多:
[GitHub 资源库](https://github.com/typicode/husky)

## 结论

我希望这有所帮助。如果您有任何问题，整篇文章中有大量的参考资料可供您更深入地理解这些主题。你有什么建议吗？留下评论:D

审查人:[马科斯·戈比](https://github.com/MacPardo)