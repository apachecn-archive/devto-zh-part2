# 广度优先搜索(BFS) -第 3 部分

> 原文：<https://dev.to/mkhy19/breadth-first-search-bfs---part-3-4385>

## 在你开始阅读之前，我们已经在这里讨论了关于树和二叉查找树[的介绍](https://dev.to/mkhy7/trees---data-structure---part-1--9n3)，这里还有一个关于深度优先搜索(DFS) [的博客](https://dev.to/mkhy7/depth-first-search-dfs-5573)

我们将继续谈论树木。具体来说，看看在树上行走，或者访问树的元素，或者遍历树的元素。所以我们经常想要以特定的顺序遍历树的节点。但是在这里，我们将讨论广度优先搜索(BFS)

## 【广度优先搜索】(BFS)

*   在这里，我们将实际使用队列而不是堆栈。因此，在广度优先中，我们在这里称之为级别遍历，我们将继续实例化一个队列。
*   在队列中首先放入树根。所以我们把它放在队列中
*   然后，当队列不为空时，我们将出队，所以拉下一个节点，通过打印来处理它。
*   然后，如果它有一个左孩子，让左孩子排队。
*   如果它有一个正确的子节点，将正确的子节点排队。
*   因此，这将具有按级别顺序遍历和处理元素的效果。

### 广度优先搜索(BFS)算法

```
LevelTraversal(tree)
    if tree = nil: 
        return
    Queue q
    q.Enqueue(tree)
    while not q.Empty() :
        node ← q.Dequeue()
        Print(node)
        if node.left ̸= nil:
            q.Enqueue(node.left)
        if node.right ̸= nil:
            q.Enqueue(node.right) 
```

让我们来看一个例子。
[![Alt Stack](img/93f54cde62307ac81ca8fe1fce54b3c5.png "Breadth First Search (BFS)")T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--oMBOtL6q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/FrHLqeU.png)

-我们在这里看到了示例，我们将显示队列。

*   在这里，假设我们在 while 循环之前，队列包含 Les。我们现在要让 Les 从队列中出列，通过打印输出它。
*   然后让 Les 的孩子 Cathy 和 Sam 排队。
*   现在，我们按顺序访问它们，所以首先我们要让 Cathy 出列，打印出来，然后让它的孩子入队。
*   记得当我们排队时，我们排在队伍的最后，所以亚历克斯和弗兰克跟着山姆。
*   所以现在我们要让 Sam 出列，打印它，然后让其子元素 Nancy 和 Violet 入队。
*   我们可以看到，我们首先打印了 Les，这是第一级，然后我们打印了第二级的元素，即 Cathy 和 Sam，现在我们将继续打印第三级的元素。
*   请注意，第三级中的所有元素，Alex、Frank、Nancy 和 Violet 都已经在队列中了。
*   并且它们都将在任何第四级节点被处理之前被处理。因此，即使它们将被推入队列，因为第三级节点先到达那里，所以它们都将在我们处理第四级节点之前被处理。
*   在这里，我们让 Alex 出队，打印出来，就完成了。让弗兰克出列，把它打印出来，弗兰克的事就完了。让南希出列，把它打印出来，我们搞定南希了。维尔莉特，我们把它打印出来，
*   但是也要让托尼和温迪入队，然后让他们出队并打印出来。
*   所以这是一个广度优先的搜索，有一个明确的队列。
*   注意:你可以进行深度优先的搜索，而不是递归的，迭代的，但是你需要一个额外的数据结构，这是一个堆栈来跟踪仍然要做的工作。

## 下面是完整的例子: