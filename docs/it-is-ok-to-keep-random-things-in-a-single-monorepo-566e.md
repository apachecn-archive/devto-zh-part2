# 在一个 monorepo 中保存随机的东西是可以的

> 原文：<https://dev.to/zkochan/it-is-ok-to-keep-random-things-in-a-single-monorepo-566e>

很长一段时间，我都是 monorepos 的反对者。有许多流行的开源贡献者在 npm 上有数百个包，每个包都有一个专用的 GitHub 库。我以为大家都是这样做的，所以应该是正确的方式！没有吗？

我也开始向 npm 发布一些东西，几年后，我现在在注册表中有将近 300 个包。这花了我很长时间，但我意识到，大多数 npm 包不需要专门的库。

## 大多数 npm 包几乎从不更新

一旦一个包准备好了，你可能再也不会更新它了。唯一一次你需要更新一个包的时候是 need 或者 Greenkeeper 会打开一个 PR 来更新有重大版本变化的依赖项。

那么，为什么要为一个只有不到 10 次有用提交的包创建一个专用的存储库呢？

## 大多数的 npm 包从来没有得到任何贡献

即使是受欢迎的软件包也很少有贡献。当然，你将是*你的非流行软件包的唯一*贡献者。

那么为什么要保留一个单独的 GitHub 库呢？没有其他开发人员需要给定 npm 包的管理权限。

## 就可以了，用一个回购！

你可能会想:“但是那些包是完全不相关的”。这是真的。但是这是唯一的缺点:将随机的包保存在一个库中。想想所有的优势:

*   来自绿地管理员/翻新的通知减少
*   更少的 CI 设置
*   较少样板文件

其他优势:

*   您可以使用限制您使用的存储库数量的服务。
*   您可以轻松地将所有代码迁移到其他 git 服务器上，因为只需要迁移一个存储库

## 如何

我的方法是使用 [pnpm](https://github.com/pnpm/pnpm) 的[递归命令](https://pnpm.js.org/docs/en/pnpm-recursive.html)来安装你的包的所有依赖项并运行它们的测试:

```
pnpm recursive install
pnpm recursive test --workspace-concurrency 1 
```

Enter fullscreen mode Exit fullscreen mode

但你也可以使用 [Rush](https://rushjs.io/) 、 [Lerna](https://github.com/lerna/lerna/) 或其他 monorepo 管理工具。

要了解我如何将我的一些包转移到一个单一的回购协议中，请参见 [zkochan/packages](https://github.com/zkochan/packages) 。

## 您随时可以在以后创建专用的存储库

如果你的一个包受到了很多关注，你可以在以后把它转移到一个专门的存储库中。

* * *

照片由 Olav Ahrens rtne 在 Unsplash 上拍摄