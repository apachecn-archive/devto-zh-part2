# 在 TFVC 编辑文件的以前版本

> 原文：<https://dev.to/collinbarrett/editing-a-file-s-previous-version-in-tfvc-6ik>

[![Visual Studio](img/f1037a9c1177a51fc2739194d6bc2cb5.png)T2】](https://collinmbarrett.com/editing-previous-version-tfvc/)

## 痛点

有时，我想从 TFVC 获取一个特定的非最新版本的文件，并在本地工作区*摆弄它来测试一个理论。在 Visual Studio 中，有很多方法可以获得文件、文件夹或整个分支的特定版本。然而，每当您开始编辑一个文件的先前版本时，Visual Studio 会自动签出该文件的*最新*版本。*

## 解

修复方法(读取解决方法)是在获得文件的先前版本之前手动签出文件，而不是依赖于`Check out automatically`选项。

## 可配置选项

有一个选项来切换`Check out automatically`是获取文件的最新版本还是保留当前版本似乎很简单。也许这是一种选择，但我还没有找到。

在`Tools`->-`Options`->->-`Visual Studio Team Foundation Server`有下面的选项，但是在一个*本地*工作区似乎没有任何影响。

> 在服务器工作区中签出时获取项目的最新版本

## 环境

我的团队使用的是 Visual Studio Enterprise 2017 和 Team Foundation Server 2015。

*经由[栈溢出](https://stackoverflow.com/questions/35748680/tfs-check-out-specific-version-make-changes-check-in)T3】*