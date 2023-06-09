# 评论-启示|第 1 部分

> 原文：<https://dev.to/codevault/the-comment-pocalypse--part-1-4p79>

*最初发布于[CodeVault](https://code-vault.net/articles/comments-part1.php)T3】*

在对代码进行注释时，有很多约定。让我们把它们分解一下，看看它们有多好/多坏。

首先是**注释你所有的方法**约定。你为什么要这么做？所以每个人都知道你的代码做什么以及如何使用它。好处是:

*   糟糕的命名方法变得不那么成问题了
*   难以理解的方法通常更容易理解
*   在例子的情况下，学习如何使用真的很容易

这听起来不错，但是评论你所有的方法是一把双刃剑。我们来看一个例子:

```
/**
 * Gets all the hardware of the type and brand given.
 * Searches for substring *brand* inside each hardware's brand name.
 * @param type the type of hardware
 * @param brand the brand to search for
 * @returns a list of hardware
 * Example: getHardwareOfTypeAndBrand(HardwareType.Keyboard, "Das") 
 *          returns List with dasKeyboard1 and dasMechKeyboard2
 */
public List<Hardware> getHardwareOfTypeAndBrand(HardwareType type, String brand) { ... } 
```

Enter fullscreen mode Exit fullscreen mode

我想我们都同意至少有评论和例子看起来不错。但是它们的可用性如何呢？※评论:

```
 * Gets all the hardware of the type and brand given.

 * @param type the type of hardware
 * @param brand the brand to search for
 * @returns a list of hardware 
```

Enter fullscreen mode Exit fullscreen mode

请告诉我们更多关于该方法的信息，比:

```
public List<Hardware> getHardwareOfTypeAndBrand(HardwareType type, String brand) 
```

Enter fullscreen mode Exit fullscreen mode

答案是否定的。这里我们来看第一个问题:**冗余**。你知道当你的代码中有冗余时会发生什么吗？当一个地方发生变化时，你必须在另一个地方改变它。

评论也是如此。如果我们要更新我们的方法并添加另一个参数，我们也必须改变我们的注释，否则我们的注释会变成**误导**。

误导性评论是我们的第二个问题。尤其是当我们也有例子的时候。但是当方法的签名改变时，我们没有工具和插件来帮助我们更新我们的注释吗？是的，我们有，但这意味着在一些事情上投入更多的时间，到最后，这些都是多余的。

想象一下，将我们的方法从，而不是搜索包含给定字符串的品牌，我们将检查品牌名称是否完全对应。

您还应该更改注释。但是，现在是晚上 8 点，10 分钟后你要进行部署，你会吗？再加上没有办法检查这个例子是否有效，你就得到了一个灾难的处方。这里是本评论:

```
Searches for substring *brand* inside each hardware's brand name. 
```

Enter fullscreen mode Exit fullscreen mode

也会产生误导。

下一部分，我们将讨论其他公约及其利弊。