# 如何在 Git 中管理不同的项目发布版本？

> 原文：<https://dev.to/marmalade118/how-to-manage-different-project-release-versions-in-git-13pf>

我相对来说是使用 Git 的新手，我希望有人能帮我解决一个我发现自己所处的场景？

我需要创建一个应用程序的多个版本，每个版本根据客户的要求激活不同的功能集；而且还会继续开发适用于所有版本的核心功能集。

最初我打算把每个版本放在它自己的库中，但是，据我所知，当发布与所有版本相关的更新时，这将意味着更多的管理开销，并且没有利用 Gits 的特性。

我想正确的方法是使用分支，将核心更新合并到每个客户端特定的分支中，但是，我只使用 Git 管理过一个包含主分支、开发分支和发布分支的解决方案，所以我不知道这是正确还是错误的方法，我应该如何构建存储库等等。

任何建议都将不胜感激。