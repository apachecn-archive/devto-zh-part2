# 需要测试人员:elm-live 的新特性

> 原文：<https://dev.to/wking/beta-testers-needed-new-feature-in-elm-live-3eba>

我刚刚发布了 elm-live 的测试版，增加了向`--before-build`和`--after-build`标志传递多个参数的能力。我希望有人能测试一下，并让我知道他们的想法！

安装:`npm install elm-live@beta [--save-dev]`

这提供了将多个脚本链接到一个带参数的脚本文件中的能力，或者创建一个链接不同命令的 npm 脚本。

```
elm-live src/Main.elm --before-build="npm run things" --after-build="customstuff.sh --flag" 
```