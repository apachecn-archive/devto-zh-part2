# 在你的 Ember 应用中使用 React 组件

> 原文：<https://dev.to/sivakumar_kailasam/using-react-components-in-your-ember-app-41m9>

[![](img/1a9afa84d1f53f0433b024b8b812583b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--u_WLIlyP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ABSs196_7DmG5oPDW_RL4Ug.png)

> *免责声明:*我是 ember.js 学习团队的成员。这不是一篇 react vs ember 的文章。他们都很棒。

如果一个与 Ember 合作的团队想要重用一个由 React 团队构建的内部组件，该怎么办？或者您可能知道并欣赏多种前端工具集？这篇文章适合他们，也适合你，开明的开发者！

这是基于我的团队在与一个企业客户合作时的经验，这个客户已经在生产中进行了大约六个月的变更。唯一需要注意的因素是包的大小，确保应用程序中不包含 React 库的副本。

让我们从让 Ember 项目了解 JSX 语法开始，并赋予它编译 JSX 代码的能力。在您的 Ember 项目中运行以下命令:

```
npm install --save-dev babel-plugin-transform-class-properties babel-plugin-transform-react-jsx 
```

Enter fullscreen mode Exit fullscreen mode

在 ember-cli-build.js 文件中，进行以下更改: