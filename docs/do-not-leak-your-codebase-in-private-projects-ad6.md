# 不要在私人项目中泄露你的代码库

> 原文：<https://dev.to/carloslfu/do-not-leak-your-codebase-in-private-projects-ad6>

有时技术人员会忘记在生产中禁用源地图，尤其是在使用 create-react-app 项目时。

这听起来可能太基础了，但是我在私人制作项目中已经多次看到这个错误。

## TL &博士...不尽然，这是一篇很小的文章

create-react-app build 命令还不能用于生产，在发布项目之前，您应该删除源地图。然而，这适用于任何私人网络项目，所以，要小心，看看你的产品代码，也许你正在泄漏整个代码库。

相关问题:[https://github.com/facebook/create-react-app/issues/2005](https://github.com/facebook/create-react-app/issues/2005)