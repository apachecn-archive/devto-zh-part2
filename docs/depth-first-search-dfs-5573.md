# 深度优先搜索(DFS) -第 2 部分

> 原文：<https://dev.to/mkhy19/depth-first-search-dfs-5573>

## 在你开始阅读之前，我们已经在这里讨论了关于树和二叉查找树[的介绍](https://dev.to/mkhy7/trees---data-structure---part-1--9n3)

我们将继续谈论树木。具体来说，看看在树上行走，或者访问树的元素，或者遍历树的元素。所以我们经常想要以特定的顺序遍历树的节点。

我们之前讨论过，当我们在看一个表达式的语法树时，我们如何通过从树叶开始向上计算表达式。这是一种以特定顺序遍历树的方式，这样我们就可以评估。另一个例子可能是打印树的节点。如果我们有一个二叉查找树，我们可能希望得到一棵树的元素排序。

## 遍历一棵树主要有两种方式。

*   **深度优先**:因此，在我们继续到兄弟子树之前，我们完全遍历一个子树。

*   广度优先(width-first):在我们进入下一层之前，我们遍历一层的所有节点。因此，在这种情况下，我们将在访问任何兄弟姐妹的任何孩子之前遍历所有兄弟姐妹

* * *

今天，我要讨论一下 DFS...DFS 可以以不同的方式遍历(按顺序、前顺序和后顺序)

# 1 -按顺序遍历

### InOrderTraversal(树)算法

```
if tree = nil:
    return
InOrderTraversal(tree.left)
Print(tree.key)
InOrderTraversal(tree.right) 
```

因此，我们将有一个递归实现
1-如果我们有一个零树，我们什么也不做
2-否则，

*   我们遍历左边的子树，
*   然后用这个密钥做我们要做的任何事情，访问它，在这种情况下，我们要打印它。但是通常你只是想执行一些操作
*   然后遍历右边的子树。

让我们来看一个例子。
[![Alt Stack](../Images/aa0b0f82a8a1a860279f941646ffc853.png "in order")T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--XqTwzzrk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/OKQy6vI.png)

我们得到了我们的二叉查找树。如果我们进行有序遍历，我们会看到这些节点是如何打印出来的。那么首先:

*   我们去 Les 节点。从那里开始，因为它不是零，我们将对它的左边的孩子，也就是 Cathy，进行有序遍历。类似地，现在我们将对它的左子节点 Alex 进行有序遍历。我们还对它的左孩子进行了一次有序遍历，所以它什么也不做。所以我们回到 Alex，然后打印出 Alex
*   然后遍历其右子树，右子树为零，什么也不做。我们回到亚历克斯身上。然后我们结束了亚历克斯，我们回到凯茜身边。所以，我们已经成功地完成了凯茜的左子树。
*   所以我们做了一个有序的遍历，所以现在我们要打印 Cathy
*   然后对它的右子树进行有序遍历，这是 Frank。所以我们去找弗兰克，同样的，现在我们要把弗兰克打印出来。
*   我们已经完成了 Frank，回到了 Cathy，现在我们已经完全完成了 Cathy，所以我们回到了 Les。
*   我们完成了 Les 的左侧子树，所以我们现在要打印 Les，然后遍历 Les 的右侧子树。
*   这是 Sam，遍历它左边的子树，这是 Nancy。打印出来
*   回到 Sam，我们已经完成了 Sam 的左子树，所以我们打印 Sam
*   然后继续执行 Sam 的右侧子树 Violet，最终将打印 Tony、Violet 和 Wendy。
*   我们和温蒂结束了。我们回去找瓦奥莱特。我们完成了她的右子树，所以我们回到 Sam，完成了他的右子树，回到 Les，完成了他的右子树，我们就完成了。

因此，我们看到我们将元素按排序顺序取出。
又来了，我们做左子。然后是节点，然后是右边的子节点。根据我们对二叉查找树的定义，它们是按顺序排列的，因为我们知道左边子节点中的所有元素实际上都小于或等于节点本身。

下面是 C++中的代码

```
void printInOrderTraversal(struct node* node)
{
     if (node == NULL)
          return;

     //Check the left, recur on left child
     printInOrderTraversal(node->left);

     //then print the data of node
     cout<<node->data<<" ";

     //then check the right, recur on right child
     printInOrderTraversal(node->right);
} 
```

* * *

# 2 -预序遍历

下一个深度优先遍历是一个前序遍历。但是我们要知道为什么要用前序和后序遍历？

*   现在，有序遍历实际上只针对二叉树定义，因为我们讨论的是先遍历左边的子节点，然后是右边的子节点。
*   所以不清楚如果你有三个孩子，你会把节点放在哪里。所以你可以打印第一个子节点，然后打印第二个和第三个子节点。或者先打印第一个子节点，然后打印第二个子节点，最后打印第三个子节点。这是一种未定义的，所以没有很好的定义。
*   然而，接下来的这两个，前序和后序遍历被很好地定义了。不只是二叉树，而是一般的，任意数量的子树。

### 前序遍历(树)算法

```
if tree = nil:
    return
Print(tree.key)
PreOrderTraversal(tree.left)
PreOrderTraversal(tree.right) 
```

让我们来看一个例子。
[![Alt Stack](../Images/1dd3c12260e7bfb57e04ae411bf824bc.png "pre order")T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--TS3gLm-u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/sMI7qai.png)

所以这里的前序遍历说，如果我们返回的是零，我们就先走一步。我们首先打印密钥，也就是说，我们访问节点本身，然后访问其子节点。

*   因此，在这种情况下，我们将转到 Les 树，然后打印出它的键，再转到它的子树。
*   所以我们将首先转到它左边的孩子 Cathy，对于 Cathy，我们将打印 Cathy
*   然后转到它左边的孩子 Alex，打印 Alex，我们回到 Cathy。我们完成了它的左子
*   所以我们去做它的好孩子，弗兰克。
*   我们干掉了弗兰克。我们完成了凯茜。我们回到莱斯。我们已经印了 Les。我们已经拜访过莱斯的左孩子了。
*   现在我们可以遍历 Les 的右边的孩子，所以它是 Sam，我们将把它打印出来。
*   然后我们会去南希，我们会打印出来
*   我们先去找萨姆，然后去找瓦勒特，我们会印出瓦勒特的指纹，
*   然后打印紫罗兰的孩子，这将是托尼和温迪，然后返回。

下面是 C++中的代码

```
void printPreorderTraversal(struct node* node)
{
     if (node == NULL)
          return;

     //print data of node
     cout<<node->data<<" ";

     //then check the left, recur on left sutree
     printPreorderTraversal(node->left);

     //then check the right, recur on right subtree
     printPreorderTraversal(node->right);
} 
```

* * *

# 3 -过帐顺序遍历

后序遍历类似于前序遍历 expect，而不是首先打印节点本身，这是 pre，我们最后打印它，这是 post。
所以我们所做的只是移动到打印声明所在的位置。

### PostOrderTraversal(树)算法

```
if tree = nil:
    return
PostOrderTraversal(tree.left)
PostOrderTraversal(tree.right)
Print(tree.key) 
```

让我们来看一个例子。
[![Alt Stack](../Images/e846cf94c78b9da0e856bb756b729ddc.png "post order")T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--FIHFcC_g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/eVU2m7q.png)

那么，这里，**这些纸币的最后一张是什么？？**实际上将会是 **Les** ，因为我们在完成之前无法打印 Les

*   完全处理 Les 的左子树和右子树。我们将访问 Les，然后访问 Cathy，然后是 Alex，然后我们将实际打印出 Alex。
*   一旦我们完成了 Alex，我们将回到 Cathy 和 Frank，然后打印出 Frank
*   然后一旦我们完成了亚历克斯和弗兰克，我们就可以打印凯茜。
*   我们回到 Les，现在我们需要去处理 Les 的右孩子，也就是 Sam。
*   为了对付山姆我们去找南希，打印南希
*   然后回到上面的 Sam 和下面的 Violet，处理 Violet 树，它将打印出 Tony，然后 Wendy，然后 Violet。
*   在我们返回的路上，当我们到达 Sam 时，我们已经完成了它的子元素，所以我们可以打印出 Sam。
*   当我们到达 Les 时，我们已经完成了它的子元素，所以我们可以打印出 Les。

关于递归遍历需要注意的一点是，我们确实有一个正在使用的堆栈。因为在递归调用中，每次我们回调一个过程，都是在调用堆栈上的另一个框架。所以我们隐式地保存了我们在栈中位置的信息。

下面是 C++中的代码

```
void printPostorderTraversal(struct node* node)
{
     if (node == NULL)
        return;

     // first check the left, recur on left sutree
     printPostorderTraversal(node->left);

     // then check the right,recur on right subtree
     printPostorderTraversal(node->right);

     //print data of node
     cout<<node->data<<" ";
} 
```

## 下面是完整的例子: