# ReactJs Chrome 扩展样板

> 原文：<https://dev.to/satendra02/reactjs-chrome-extension-boilerplate-447o>

[![ReactJs Chrome](img/b9fca3da99bd4e02cbbe5fb0b60e91c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D7O_Z14B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/71if5vcvg1my4rbehyw7.png) 
我们已经用 ReactJs 使用注入页面策略开源了 chrome 扩展的样板文件。阅读[详细博客](https://medium.com/@satendra02/create-chrome-extension-with-reactjs-using-inject-page-strategy-137650de1f39)

样板文件是使用 ReactJs 快速创建一个 chrome 扩展

创建样板文件的动机是:

1.  代替 chrome 现成的弹出窗口，我们希望将我们自己的页面注入 DOM，作为更好的 UX 的侧边栏。
2.  我们希望将 ReactJs 用于基于组件的方法、路由及其构建机制。
3.  在任何情况下，扩展 CSS 都不应该与宿主页面样式冲突。

我希望，有人会发现它有所帮助

源代码:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [桑德拉 02](https://github.com/satendra02)/[react-chrome-extension](https://github.com/satendra02/react-chrome-extension)

### 使用注入页面策略的带有 ReactJs 的 chrome 扩展样板

<article class="markdown-body entry-content container-lg" itemprop="text">

# 反应铬延伸

我们开源了 chrome 扩展的样板文件，使用了注入页面策略。阅读[详细博客](https://medium.com/@satendra02/create-chrome-extension-with-reactjs-using-inject-page-strategy-137650de1f39)

样板文件是为了使用 ReactJs 快速创建 chrome 扩展，创建样板文件的动机是:

1.  代替 chrome 现成的弹出窗口，我们希望将我们自己的页面注入 DOM，作为更好的 UX 的侧边栏。

2.  我们希望将 ReactJs 用于基于组件的方法、路由及其构建机制。

3.  我们需要确保扩展 CSS 在任何情况下都不应该与宿主页面样式冲突。

## 特征

*   使用 ReactJs 编写 chrome 扩展
*   将扩展作为内容脚本注入宿主页面
*   利用了 Chrome 消息传递 API
*   使用 Iframe 的独立扩展 CSS

## 装置

> 确保您安装了最新的 **NodeJs** 版本

克隆回购

```
git clone https://github.com/satendra02/react-chrome-extension.git 
```

转到`react-chrome-extension`目录运行

```
yarn install 
```

现在使用以下代码构建扩展

```
yarn build 
```

你会看到一个`build` …

</article>

[View on GitHub](https://github.com/satendra02/react-chrome-extension)