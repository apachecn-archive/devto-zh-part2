# “Nextron”:电子+ Next.js 梦想

> 原文：<https://dev.to/saltyshiomix/nextron-electron--nextjs-dream-3hg>

我通常是日本的. NET 开发人员，但我喜欢在业余时间写 JavaScript/TypeScript。

本文介绍 [Nextron](https://github.com/saltyshiomix/nextron) ，可以用 [Next.js](https://nextjs.org/) 制作[电子](https://electronjs.org/) app。

## 为什么？

我完全尊重 zeit/next.js 图书馆，但是对于电子图书馆，现在还没有事实上的标准。

所以我在这个空闲时间，黄金周，在日本写了 [Nextron](https://github.com/saltyshiomix/nextron) 。

一旦你安装了`nextron`，那么

*   您可以使用 Next.js 编写所有渲染器代码
*   你可以通过**只使用一个命令**来构建所有的跨平台包以供发布使用。

## 要求

[Nextron](https://github.com/saltyshiomix/nextron) 内部使用 [npx](https://blog.npmjs.org/post/162869356040/introducing-npx-an-npm-package-runner) ，所以要求如下:

*   `"node": ">=8.2.0"`
*   `"npm": ">=5.2.0"`

## 安装

```
$ npm install --global nextron 
```

Enter fullscreen mode Exit fullscreen mode

## 用法

```
# Install scaffolds for quick start
$ nextron init <YOUR-APP-NAME>
$ cd <YOUR-APP-NAME>

# for development
# it starts development process (with HMR(Hot Module Replacement) featuire)
$ yarn dev

# for production
# it builds the electron app for release usage
$ yarn build 
```

Enter fullscreen mode Exit fullscreen mode

## 文件夹结构

在`nextron init`之后，创建下面的文件夹结构。

```
.
├── common
├── main
│   └── index.js
├── package.json
├── renderer
│   ├── next.config.js
│   └── pages
│       └── home.js
└── static
    ├── icon.icns
    └── icon.ico 
```

Enter fullscreen mode Exit fullscreen mode

这时，我们可以:

1.  用 Next.js 开发**整个**渲染器流程！
2.  忘记了解我们如何为发布构建电子包

与 [Nextron](https://github.com/saltyshiomix/nextron)

## 截图

**纱线开发**

[![yarn-dev-image](../Images/5e1d84aa4e0bb955d3a91181c4477473.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--awzX519f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dstcbld2xakdq89n0u9n.png)

**纱线构建**

[![yarn-build-image](../Images/a4ab4217e8e4ac7322047def6299e0e8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uLCsEuzo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3lul0sgeuvd6anq1zysg.png)

## 结论

这个时候这个 [Nextron](https://github.com/saltyshiomix/nextron) 项目是高度 WIP 的，所以欢迎 PRs！！

愿法典与你同在！