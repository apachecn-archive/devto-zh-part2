# 一个简单的命令行界面和工具包来提取模块的用法

> 原文：<https://dev.to/taneba/module-analyzr---a-simple-cli-and-toolkit-to-extract-usage-of-module-4hli>

## 总结

我做了一个新的 npm 包叫做`module-analyzr`。它提取给定文件或目录或全局模式中的模块使用情况。

[https://github.com/taneba/module-analyzr](https://github.com/taneba/module-analyzr)

## 举例

想象一下，如果你想知道 React 在你的项目中的用法，

```
# first, install it via npm
npm install -g module-analyzr

# then, in your project
module-analyzr react src
{ importedModules:
   [ { moduleName: 'Element', usageAmount: 2 },
     { moduleName: 'Component', usageAmount: 37 } ],
  importedDefault: 215,
  importedWithNameSpace: 0 } 
```

Enter fullscreen mode Exit fullscreen mode

输出对象显示了 src 目录中 React 的用法。表示`Element`进口两次，`Component`进口 37 次，`import React from 'react'` 215 次！

如果显示`importedWithNameSpace`带有某个数字，则表示有`import * as React from 'react'`的数字。

## 动机

我是公司一个 React 做的组件库的维护人员。很难掌握每个组件在哪个项目中使用了多少。所以当我想用一些突破性的改变来升级我的库时，我必须向每个工程师询问组件的用法。`module-analyzr`对这种情况最有用。我在后端系统中使用`module-analyzr`来分析每个传递的 github urls，并可视化每个组件在所有项目中的使用量！

## 在关闭

这是我第一次发布开源项目，所以可能会有一些错误😅我从制作它中学到了很多，并希望更多地参与开源社区😀

我希望你会对这个图书馆感兴趣！