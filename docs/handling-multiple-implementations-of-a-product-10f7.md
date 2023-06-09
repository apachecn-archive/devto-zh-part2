# 处理一个产品的多个实现

> 原文：<https://dev.to/jsjlewis96/handling-multiple-implementations-of-a-product-10f7>

> 虽然我的目标是一般的、非特定的解决方案，但我正在使用想要重写我的 [Java 日志库](https://github.com/logdyn/logdyn-api)的真实例子

## 所以你想在 Node 中重写你的 Java 库...

我最近遇到了这样一种情况，我想用一种新的语言重新实现一个现有的项目，我试图决定如何最好地管理与现有产品相关的新代码。

我最初的想法是简单地创建一个新的存储库；这具有项目历史和问题之间的清晰区别的优点。

```
java-repo
    > master
        - dir1
        - dir2
node-repo
    > master
        - dir1
        - dir2 
```

Enter fullscreen mode Exit fullscreen mode

我看到过将所有代码保存在一个存储库中的论点，比如能够改变一个算法，并且在一次提交中改变所有的语言。

```
project-repo
    > master
        - java
            - dir1
            - dir2
        - node
            - dir1
            - dir2 
```

Enter fullscreen mode Exit fullscreen mode

我看到的最后一个建议是，你可以为每种语言建立一个“主”分支，但我认为这对我来说有点太不符合常规了...

```
project-repo
    > master-java
        - dir1
        - dir2
    > master-node
        - dir1
        - dir2 
```

Enter fullscreen mode Exit fullscreen mode

你对此有什么想法？是不是有什么新的热门方法让我错过了，或者是用单独的回购保持老派风格？

> 我可能会在某个时候写一篇关于重写过程的帖子，所以即使它失败得很可怕，至少应该是有趣的！😄