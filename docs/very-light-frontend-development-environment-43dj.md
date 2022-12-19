# 非常轻便的前端开发环境

> 原文：<https://dev.to/kwbtsts/very-light-frontend-development-environment-43dj>

我介绍了如何创建一个非常轻量级的前端环境(包、打字稿、hyperapp、picostyle、jest)。我想立即创建一个前端开发环境，减少依赖性，更加简单。有很好的最小库。

## 入门

```
yarn init 
```

Enter fullscreen mode Exit fullscreen mode

## 最小前端库

### hyperapp

[![hyperapp](../Images/65ccbd91303de92fc5d91e4a095334a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V_5QHFkQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/profile_images/968897827265945600/PcmPXmEk_400x400.jpg) 
[hyperapp](https://github.com/hyperapp/hyperapp) 是非常适合前端 JavaScript like React 的一个轻库。
想知道怎么用，可以从[这篇文章](https://dev.to/aspittel/functional-programming-in-javascript-done-right-with-hyperapp-570f)开始。这非常非常小(1.4KB)并且[没有依赖性](https://github.com/hyperapp/hyperapp/blob/master/package.json)。这也包括类似 [Redux](https://redux.js.org) 的灯光功能状态管理。“行动”可以改变“状态”。

```
yarn add hyperapp 
```

Enter fullscreen mode Exit fullscreen mode

### 微微风格

picostyle 是 JS 中一个非常轻量级的 CSS 库。这是 0.4KB，也是[不依赖](https://github.com/morishitter/picostyle/blob/master/package.json)。你可以用这个代替[样式组件](https://www.styled-components.com/)。

hyperapp 和 picostyle 是由同一家公司的成员创建的。

```
yarn add picostyle 
```

Enter fullscreen mode Exit fullscreen mode

## 模块捆绑器

[![parcel](../Images/1b13c4e337dd7aea4f456a4b8204583e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fyPFw4wP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/19409/31321658-f6aed0f2-ac3d-11e7-8100-1587e676e0ec.png) 
[包裹](https://parceljs.org/)是一个简单的模块捆绑器。这没有复杂的配置。

```
yarn add parcel-bundler -D 
```

Enter fullscreen mode Exit fullscreen mode

## 变换

[![TypeScript](../Images/3ad608cdf89007a05ee1551c408d2404.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--feKa7qvM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://mherman.org/assets/img/blog/typescript-logo.png) 
我想用打字所以我选了[打字稿](http://www.typescriptlang.org)。hyperapp 的“状态”应该由类型定义，这样就可以很容易地使用组件的状态。tslint 是打字稿的 linter。
你要加上`tsconfig.json`和`tsliny.json`。

```
yarn add typescript tslint -D 
```

Enter fullscreen mode Exit fullscreen mode

## 测试框架

[![Jest](../Images/12e6523e722974a9f84c30058c737017.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Copdtwrr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://facebook.github.io/jest/img/opengraph.png) 
[Jest](https://facebook.github.io/jest/) 是脸书制作的 JavaScript 测试框架。这通常与 React 连用。我添加了`ts-jest`在这种情况下使用 TypeScript。

```
yarn add jest ts-jest @types/jest 
```

Enter fullscreen mode Exit fullscreen mode

并且我为`ts-jest`在`package.json`中添加了设置。

```
{
  "jest": {
    "transform": {
      "^.+\\.tsx?$": "ts-jest"
    },
    "testRegex": "(/__tests__/.*|(\\.|/)(test|spec))\\.(jsx?|tsx?)$",
    "moduleFileExtensions": [
      "ts",
      "tsx",
      "js",
      "jsx",
      "json",
      "node"
    ]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我对 hyperapp 的测试代码是这里的。

## 结果项目

[https://github . com/SatoshiKawabata/parcel-hyperapp-typescript](https://github.com/SatoshiKawabata/parcel-hyperapp-typescript)