# 对使用非标准化规范的代码使用 babel-eslint

> 原文：<https://dev.to/ohbarye/use-babel-eslint-for-code-using-not-standardized-specifications-4g1o>

## ESLint 解析错误

在为[的审查等待列表机器人](https://github.com/ohbarye/review-waiting-list-bot)写代码时，我在`eslint`遇到了下面的`Parsing error`。

```
$ eslint .

/Users/ohbarye/.ghq/github.com/ohbarye/review-waiting-list-bot/src/App.js
  19:21  error  Parsing error: Unexpected token ..

✖ 1 problem (1 error, 0 warnings)

error Command failed with exit code 1. 
```

Enter fullscreen mode Exit fullscreen mode

原因似乎是由于代码使用了不标准的规范，如下所示。是啊，[物体静止/扩散属性](https://github.com/tc39/proposal-object-rest-spread)显然还在第三阶段(截至 2018-04-30)。

```
const { authors, ...conditions } = { authors: [], owner: '', repo: '' } 
```

Enter fullscreen mode Exit fullscreen mode

此外，eslint 官方声明默认的 eslint 解析器应该如此。

参考:t0【https://github . com/ESL int/ESL int/issues/6693】

## 巴别塔-埃斯林

当我们想要使用 *stage n* 规范时，我们需要使用 [babel-eslint](https://github.com/babel/babel-eslint) 。

首先，让我们将其添加为一个 devDependency。

```
yarn add -D babel-eslint 
```

Enter fullscreen mode Exit fullscreen mode

然后，在`.eslintrc.json`中指定一个解析器。

```
# .eslintrc.json
{
  "parser": "babel-eslint",
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我可以再次见到火花了。✨

```
$ eslint .
✨  Done in 1.45s. 
```

Enter fullscreen mode Exit fullscreen mode

## 环境

*   纱线版本 1.6.0
*   节点 v8.3.0
*   eslint 4 . 4 . 1 版
*   巴别塔-埃斯林版本 8.2.3