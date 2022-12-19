# 了解 package.json 中的 dep

> 原文：<https://dev.to/uetchy/know-your-deps-on-packagejson-in-seconds-3d2a>

[![screenshot](../Images/b6cfd30989e1dc269879c331b019d233.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fBiDB77a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://uetchy.github.io/npm-deps-list/screen.png)

你如何知道项目/库依赖于哪些包，这些包到底是做什么的？
你会想要快速地对它们进行调查。所以 [npm-deps-list](https://github.com/uetchy/npm-deps-list) 来了。

您可以使用`npm`或`yarn`来安装它们。

```
npm install -g npm-deps-list 
```

Enter fullscreen mode Exit fullscreen mode

运行`ndl`，您将获得当前目录下的包的依赖项的详细列表。

如果你正在使用 iTerm2，你也可以在一个包名上`Command + Click`跳转到他们的主页。

如果您对此有任何想法，请考虑提交问题或拉动请求！