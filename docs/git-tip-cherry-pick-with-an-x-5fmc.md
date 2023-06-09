# Git 提示:用-x 进行挑选

> 原文：<https://dev.to/tsalikispk/git-tip-cherry-pick-with-an-x-5fmc>

### TL；速度三角形定位法(dead reckoning)

*   接受提交并将其更改应用到当前分支。
*   与他人合作时，使用`-x`选项，以便在提交消息上突出显示这是精心挑选的，而不是原始提交。
*   当您只需要一个特定的提交时，请谨慎使用它，而不是使用`merge`。
*   过度使用它会导致历史与重复提交混淆，因此通常首选`merge`。

# 什么是挑樱桃？

当你挑选时，你基本上把你的目标付诸实施，并把它的变化应用到你当前的头脑中。这将导致具有不同 SHA 和相同提交消息的全新提交。

```
git cherry-pick <target commit SHA> 
```

Enter fullscreen mode Exit fullscreen mode

# 首先为什么要挑肥拣瘦？

假设你已经把自己逼入了一个角落——你需要来自另一个分支的变更，但是你不需要*所有的*变更，只需要一些提交。这里是唯一应该应用`cherry-pick`的情况。

# 主要缺点

大多数时候开发人员选择`merge`而不是执行`cherry-pick`——但这是为什么呢？嗯，在应用`cherry-pick`时必须非常小心，尤其是不同提交的应用顺序。当合并时，您可以确保提交以正确的顺序应用，所以您只需要适当地解决冲突。相反，当处理大量精选时，您必须手动找到提交的正确顺序，并根据它们的日期逐个应用它们。

此外，您最终会得到重复的提交，因为精选提交的名称将与原始提交的名称相同。

所以，`cherry-pick`不好是因为:

1.  您必须手动应用精选——根据您必须应用的提交数量，可能很难找到正确的顺序，这肯定是一个容易出错的过程。
2.  重复提交导致怪异的外观和丑陋的历史。
3.  假设其他人需要在另一个分支中进行修复，他们会选择正确的提交还是选择*精心挑选的*提交。毕竟，精心挑选的人可能会有你不需要的冲突。

# 历史丑恶的人为例子。

下面是一个精心设计的例子，展示了一颗樱桃如何让历史看起来丑陋不堪。假设您在*主*、 *A* 和 *B* 上创建了两个提交，并且在那一刻您想要创建一个新的特性分支(用额外的想象力命名为*特性*)并添加一个新的提交 *C* 。

目前我们有这样的东西。

```
A --- B <- (master)
          \
           \
            C <- (feature) 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们再次返回到*主机*并提交 *D* 。我们在*特性*上提交 *E* ，但在此之前，我们需要从*主*应用修复 *D* ，因此我们挑选 *D* 。

```
 A --- B --- D     <- (master)
          \
           \
            C --- D' --- E     <- (feature) 
```

Enter fullscreen mode Exit fullscreen mode

此时，我们希望将*特征*合并回*主特征*。现在这个图看起来会像这样:

```
A --- B --- D ----- Merge Commit     <- (master)
          \                /
           \              /
            C --- D' --- E     <- (feature) 
```

Enter fullscreen mode Exit fullscreen mode

一个`git log --oneline`将向我们显示重复的提交:

```
5b5f739 (HEAD -> master) Merge branch 'feature'
d5c76ad E
52ed367 D
c097b61 D
af599fa C
383a1e1 B
c1fffad A 
```

Enter fullscreen mode Exit fullscreen mode

当然，在这个例子中，我们可以使用`rebase`来避免重复提交，但是我们可以想到一个场景，其中第二个分支是一个长期分支，并且只需要从上游应用一个热修复程序。

# 与他人合作

那么，如果你是**而不是**唯一需要这些宝贵修复的人，你会怎么做？自然地，另一个人会尝试使用提交消息来搜索期望的提交。你猜怎么着，你会发现不止一个提交，你不知道应用哪一个。当然，可以尝试更改提交名称，这样就可以暗示这是一个`cherry-pick`提交，而不是原来的提交。这可以通过下面的命令来完成:

```
git cherry-pick <target commit SHA> -e 
```

Enter fullscreen mode Exit fullscreen mode

这将打开一个编辑器，您可以在其中更改提交消息并添加一些提示这是一个`cherry-pick`提交的内容。

经过一番挖掘，我发现还有另一个选项可以自动追加 *`(cherry picked from commit <original commit's SHA>)`*

```
git cherry-pick <target commit SHA> -x 
```

Enter fullscreen mode Exit fullscreen mode

至少现在您知道哪个提交是精选的，以及最初的提交是什么。

罗伯特·祖尼科夫在 [Unsplash](https://unsplash.com/s/photos/cherry?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的封面图片