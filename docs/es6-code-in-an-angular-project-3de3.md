# 角度项目中的 ES6 代码

> 原文：<https://dev.to/prestonjlamb/es6-code-in-an-angular-project-3de3>

我最近在做一个 Angular 项目，在这个项目中，我将一个 JavaScript 文件加载到。angular-cli.json 文件(如果您的文件是 angular.json 文件，也会发生同样的情况)。这个文件非常简单，最多只有 40 行。但是它使用了 ES6 语法(像`const`)和一个`for(const - of -)`循环。我在我的 Node.js 应用程序和 Angular 项目的 TypeScript 中编写这样的 JavaScript 已经有很长时间了。现在这已经成了习惯，我也没多想。

几天前，当我们准备部署应用程序时，我试图进行生产构建，这时问题出现了。我运行了`ng build --prod`命令，大约运行到一半时，我得到了一个错误:

[![Output of an error during an Angular build process.](../Images/33978ab4152bdd23fda653c0aee7f855.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9MdDiB_u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.prestonlamb.com/static/images/blogPosts/es6CodeInAngular/error-output.png)

有时我会得到这个错误，有时它是不同但相似的。错误是关于“意外的标记常量”。虽然不是很具体，我甚至不知道应该查看哪个文件来找出问题所在。所以我开始在谷歌上搜索这个问题，很幸运地遇到了这个 GitHub 问题，它给我指出了正确的方向。具体来说，我发现了这个评论:

[![GitHub issue comment that gave me the idea to look for JavaScript code written in ES6.](../Images/e1420377b1e1320b125b180942b8ffde.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pfsogXxr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.prestonlamb.com/static/images/blogPosts/es6CodeInAngular/github-issue.png)

在那一点上，我能够回到我的代码库并找出问题所在。因为文件足够小，所以我可以手动将其改回 ES5 语法。这样做之后，构建成功了，我可以继续了。

这个问题可能不会经常出现，因为您可能不会编写通过。angular-cli.json 文件。但是如果你需要的话，不要忘记要么首先编写有效的 ES5，要么通过 Babel 或另一个 JavaScript 编译器运行代码，并将该文件包含在项目中。