# 使用 ESLint 的林挺 JavaScript 代码库快速指南

> 原文：<https://dev.to/lauragift21/quick-guide-to-linting-javascript-codebase-with-eslint-dcf>

最初发布在我的[博客](https://www.giftegwuenu.com/quick-guide-to-linting-javascript-codebase-with-eslint)

[![banner](img/e2140b857263672745935d1a73bf1005.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FQxU3AZj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/lauragift/image/upload/v1532898161/032f30a0-f2e1-11e5-8676-4676c2ca102a_canln7.png)

JavaScript 中的林挺非常重要，大多数开发人员在开发软件时往往会跳过这一步，而实际上这应该是开始项目前的重要一步。

## 什么是林挺？

Javascript 中的林挺是一种编码风格集成，它检查你的代码质量，并使你能够修复你的代码风格中可能存在的问题。

## 为什么要皮棉？

是的，为什么这一步非常重要。我可以开始我的项目而不用担心这一步，对吗？答案是否定的！！关于为什么你应该考虑林挺你的 JavaScript 代码的一些重要原因是:

*   它帮助你找出代码中的错误。
*   它有助于你与偏好的编码风格保持一致。
*   它可以节省你调试语法错误的时间。

## ESLINT

*[ESLint](https://eslint.org/) 是一个开源项目，最初由 Nicholas C. Zakas 于 2013 年 6 月创建。它的目标是充当识别和报告 JavaScript 代码中发现的模式的工具，目的是使代码更加一致并避免错误。*

## 安装

ESLint 需要 Node.js > 6.14 才能运行。它也可以使用两种不同的方法进行安装，可以是全局安装，也可以是本地安装。

### 本地安装

如果您想将 ESLint 作为项目构建系统的一部分，建议将其安装在本地。你可以通过以下步骤实现:

```
 $ npm install eslint --save-dev // or yarn install eslint --dev

// setup eslint configuration by running
$ ./node_modules/.bin/eslint --init

// After that, you can run ESLint in your project’s root directory like this:

$ ./node_modules/.bin/eslint yourfile.js 
```

Enter fullscreen mode Exit fullscreen mode

### 全局安装

如果您想让 ESLint 可用于在您所有项目中运行的工具，我们建议您全局安装 ESLint。你可以使用 npm:

```
 $ npm install -g eslint // or yarn add global eslint

// initial eslint configuration
$ eslint --init

// After that, you can run ESLint in your project’s root directory like this:
$ eslint yourfile.js 
```

Enter fullscreen mode Exit fullscreen mode

## 代码编辑器集成

另一个可以集成到代码编辑器中的酷工具是安装 ESLint 扩展

## ESLint 配置

*ESLint 旨在以多种不同方式进行配置，以适应您的使用情况。您需要一个`.eslintrc.json`文件作为配置文件。ESLint 还能让你在编写代码时集成一个风格指南。一些例子包括:*

*   [Airbnb 风格指南](https://github.com/airbnb/javascript)
*   [谷歌风格指南](https://google.github.io/styleguide/jsguide.html)
*   [反应风格指南](https://github.com/airbnb/javascript/tree/master/react)

```
 // install it as a dev dependency
npm install --save-dev eslint-config-airbnb // or yarn add --dev eslint-config-airbnb

// add the style guide name i.e:airnbnb
{
  "extends": "airbnb"
} 
```

Enter fullscreen mode Exit fullscreen mode

## ESLint 规则

Eslint 附带了大量的规则，您可以使用配置文件或配置注释来处理或修改项目所需的规则。 *以下是规则指引:*

```
 - "off" or 0 - turn the rule off
- "warn" or 1 - turn the rule on as a warning (doesn’t affect exit code)
- "error" or 2 - turn the rule on as an error (exit code is 1 when triggered) 
```

Enter fullscreen mode Exit fullscreen mode

*要在配置文件`eslintrc.json`中配置规则，需要添加规则键和要修改的选项，例如:*T3】

```
// sets the no-console rule off
"rules" : {
  no-console: 0; // or "off"
} 
```

Enter fullscreen mode Exit fullscreen mode

*要在配置注释中配置规则，您需要添加规则键和想要修改的选项，例如:*

```
 // This disables the entire eslint rules in a js file
/* eslint-disable */
console.log('test');

// This disable a specific rule within a js file
/* eslint-disable  no-console*/
console.log('test');

// This disable a specific rule on a  single line
console.log('test'); // eslint-disable-line no-console 
```

Enter fullscreen mode Exit fullscreen mode

> 使用林挺库非常重要，ESLint 是解决编写易错代码问题的一个工具。