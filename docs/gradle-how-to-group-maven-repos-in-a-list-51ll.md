# Gradle:如何在列表中对 Maven Repos 进行分组

> 原文：<https://dev.to/foso/gradle-how-to-group-maven-repos-in-a-list-51ll>

在我的一些基于 Gradle 的项目中，我使用必须从许多不同的 Maven 仓库下载的库。为了更好地了解所有二手回购，我想把它们放在一个列表中。不幸的是，事情比我想象的要复杂一些。因为我无法用我的方法找到其他博客帖子，所以我想与你分享。所以这是我的解决方案:

这就是如何将**一个**存储库添加到您的构建脚本: