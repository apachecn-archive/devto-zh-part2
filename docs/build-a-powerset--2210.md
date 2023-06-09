# 构建一个电源集

> 原文：<https://dev.to/tomerbendavid/build-a-powerset--2210>

## 什么是 PowerSet？

为什么叫 PowerSet？这对我们是好是坏？
我的下一个角度/机器学习项目会需要它吗？

如果我们在**维基百科上查看 powerset** 的定义，我们会看到以下内容:

> 在数学中，任意集合 S 的幂集(或幂集)是 S 的所有子集的集合，...

酷，这意味着如果我们有几个项目的集合{1，2}，那么幂集将是所有集合的集合，在{1，2}的情况下，它将是:

`{ {1}, {2}, {1, 2}}`

继续阅读维基百科的定义，我们看到那里的人说:

> 包括空集和 S 本身

你知道吗，那些人说我们需要**添加空集**让我们添加它，然后继续:

`{ {}, {1}, {2}, {1, 2}}`

所以我们添加了空集，**定义现在是快乐的。**:)

好了，我想现在我们和定义一致了，很好。

## 电脑使电源设定

现在的问题是，我们如何给计算机编程，给我们力量？电脑没有阅读维基百科的定义，所以它不知道如何编码，让我们帮助他。

递归立即浮现在脑海中。使用递归，如果我们用一个更小的问题来解决问题，我们可以将更小的问题与更大的问题合并，所以我们用我们的原始集合:

`{1, 2}`，这里更小的集合或者更小的问题会是:`{1, 2}.tail => {2}`。如果我们神奇地(或者换句话说，递归地)创造了它的幂集:

`{ {}, {2}}`。那么留给我们的就是将它与头部的幂集合并- `{1, 2}.head => {1}`即:`{ {}, {1}}`，然后添加集合本身即:`{ {1, 2} }`。

我不知道你怎么想，但是，尽管递归很浪漫，但我总觉得它是欺骗，那么我们如何在没有欺骗的情况下，也就是说，没有递归的情况下实现它呢？

如果我们只扫描一次集合，并对每个项目进行一次计算，我们实际上没有机会对子集进行所有的组合，所以看起来好像我们需要嵌套循环。让我们开始写吧，但是在那之前先等等，**让我们预热一下 java 语法引擎**。

## 用 Java 设定功率

**预热 java 设定技能**:

在 java 中定义一个新集合:

```
Set<Integer> set = new HashSet<>(); // define a set of integers in java.
Set<Set<Integer>> setOfSets = new HashSet<>(); // PowerSet is a set of sets..
Set<Set<Integer>> copiedSet = new HashSet(setOfSets); // Copy a set into a new variable. 
```

**我们从签名**开始，我们得到一个集合并返回一个集合，两者都是整数集合:

`public Set<Set<Integer>> powerSet(Set<Integer> set) // we get a set and return a set of sets, a power set is a set of sets.`

现在我们有了一个集合，让我们开始迭代集合项:

```
public Set<Set<Integer>> powerSet(Set<Integer> set) {
    Set<Set<Integer> powerSet = new HashSet<>();

    for (Integer item: set) {
        powerSet.add(item);
    }

    // omg omgI'm stuck here...
    // but now what after we added all items how do we combine them all to the power set?
} 
```

看起来这是不够的，我们不能只是扫描项目，并建立一套集合。让我们稍微思考一下这个问题。

嗯（表示踌躇等）...

好了，我们说了一个**集合**，让我们重复这个**集合**，所以它就像多个集合，但不仅仅是多个集合，它就像每个集合都包括其他集合。

我们创建的每一个集合，我们都需要这个集合和集合中的每一个其他项目，所以，对于我们找到的每一个集合，不管它是哪个子集，对于每个子集，另一个有效的子集将是这个子集和原始集合中的每一个其他项目。

找到{ 1，2 }个子集？原始集合中有{ 4 }，这是另一个子集{ 1，2，4 }。

为此，我们要做的是**存储我们设法找到的所有时间子集**，并为每个这样的子集添加原始集合中的每个项目，这意味着它将遵循:

1.  找到第一个子集——例如，空集。
2.  将其添加到已经找到的子集中。
3.  将原始集合中的每个项目添加到。

好了，都准备好了吗？让我们转到完整的 PowerSet 实现！:

```
 public static Set<Set<Integer>> powerSet(Set<Integer> set) {
       Set<Set<Integer>> powerSets = new HashSet<>();  // we define powerset.

       Set<Integer> emptySet = new HashSet<>();
       powerSets.add(emptySet); // start with the empty set.

       for (Integer item: set) { // We want to add each item to all previous subsets.
         Set<Set<Integer>> foundSets = new HashSet(powerSets);
         for (Set<Integer> foundSet: foundSets) {
           Set<Integer> newSet = new HashSet<Integer>(foundSet);
           newSet.add(item); // Add each item to all previously found subsets.
           powerSets.add(newSet); // Add new subset to all subsets found.

         }
       }

       return powerSets;

     }

And if we run it with:

         Set<Integer> set = new HashSet<Integer>();
         set.add(1);
         set.add(2);
         set.add(3);
         System.out.println(powerSet(set));

We get:

// [[], [1], [2], [1, 2], [3], [1, 3], [2, 3], [1, 2, 3]] Looks like a power set to me! What do you say? ;) 
```

# 回递归

现在我们已经有了命令式方法，让我们看看**Scala::functional::declarative::concrete**方法:)

```
 def powerSet(set: Set[Int]): Set[Set[Int]] = {

    set.toList match {
      case Nil => Set(Set.empty[Int])
      case x :: xs => powerSet(xs.toSet).map(xsi => xsi + x) ++ powerSet(xs.toSet)
    }
  }

  println(powerSet(Set(1,2,3))) // Set(Set(), Set(3, 1), Set(2), Set(2, 1), Set(3, 2), Set(3), Set(3, 2, 1), Set(1)) 
```

太酷了！我们刚刚拼写了幂集定义的神奇单词，即每个幂集是头部的幂集加上为尾部找到的每个幂集！我们已经完成并准备好了！好热！

# 总结

总而言之，我们从添加空集开始。然后我们从 1 开始扫描每个项目..n，并把这个项目添加到空集，我们结束时，每次只有一次在幂集。

接下来，我们将每个这样的项目添加到当前存在的集合中，当我们将{ 1 }添加到集合{ 1 }中时，它将不是{ 1，1 }而只是{ 1 } ( **psst)它是一个集合，还记得吗？**)。我们继续扫描集合中的每一项，并将它们添加到当前集合中，这意味着我们得到了所有子集——幂集。

这对我们的 AngularJs 或机器学习有什么帮助？至少在我看来，这很简单，通过掌握语法和语言，通过掌握基本的数据结构，不管是**集、列表、映射**，从中我们可以构建队列、堆、树，并从中构建更复杂的数据结构所有这些都很重要，作为软件工程师，我们每天都在直接或间接与它们打交道，所以最好掌握那些非常简单的基本术语。

**for 循环、集合、列表是基本的，但它们是强大的**有了这些基本的构造，我们已经构建了 powerSet。