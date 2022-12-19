# 自平衡二分搜索法树和 JavaScript 中的 AVL

> 原文：<https://dev.to/amejiarosario/self-balanced-binary-search-trees-with-avl-in-javascript-3ckf>

二分搜索法树(BST)被用于许多我们可能不知道的事情。例如:在编译器中生成语法树，加密和压缩算法在 JPG 和 MP3 中使用。然而，搜索树需要平衡才能有用。因此，我们将讨论如何在添加和删除元素时保持 BST 平衡。

在这篇文章中，我们将探索不同的技术来平衡一棵树。我们将使用旋转来移动节点，并使用 AVL 算法来跟踪树是否平衡或需要调整。让我们开始吧！

您可以在 Github repo 中找到所有这些实现以及更多内容:

## ![GitHub logo](../Images/a73f630113876d78cff79f59c2125b24.png) [阿梅加罗萨里奥](https://github.com/amejiarosario)/[DSA . js-数据-结构-算法-javascript](https://github.com/amejiarosario/dsa.js-data-structures-algorithms-javascript)

### 🥞JavaScript + eBook 中解释和实现的数据结构和算法

<article class="markdown-body entry-content container-lg" itemprop="text">

[![image](../Images/56b5d4dbac1233ee93c5155b15079c68.png)T2】](https://user-images.githubusercontent.com/418605/59557258-10742880-8fa3-11e9-84fb-4d66a9d89faa.png)

# JavaScript 中的数据结构和算法

[![CircleCI](../Images/d5922fc0cbf322196a6f5e893fcfcf37.png)](https://app.circleci.com/pipelines/github/amejiarosario/dsa.js-data-structures-algorithms-javascript)[![NPM version](../Images/2c26fb04f41469582f423d88af92342e.png)](https://badge.fury.io/js/dsa.js)[![chat](../Images/e028ff0be0803e8e9dbdd25d5a8c6ca4.png)](https://dsajs-slackin.herokuapp.com)

> 这是 DSA.js book 的编码实现和 NPM 包的回购。

> 在这个库中，您可以找到 JavaScript 中算法和数据结构的实现。这些材料可以作为开发人员的参考手册，或者您可以在面试前刷新特定主题。还有，你可以找到更高效解决问题的思路。

[![Interactive Data Structures](../Images/8f63557fae7adb265acdf574fa489c65.png)T2】](https://user-images.githubusercontent.com/418605/46118890-ba721180-c1d6-11e8-82bc-6a671428b422.png)

## 目录

*   [安装](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#installation)
*   [特性](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#features)
*   [里面有什么](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#whats-inside)
    *   [<g-emoji class="g-emoji" alias="chart_with_upwards_trend" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/1f4c8.png">📈</g-emoji>算法分析](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#-algorithms-analysis)
    *   [<g-emoji class="g-emoji" alias="pancakes" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/1f95e.png">🥞</g-emoji>线性数据结构](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#-linear-data-structures)
    *   [<g-emoji class="g-emoji" alias="evergreen_tree" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/1f332.png">🌲</g-emoji>非线性数据结构](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#-non-linear-data-structures)
    *   [<g-emoji class="g-emoji" alias="hammer_and_pick" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/2692.png">⚒</g-emoji> 算法技巧](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#%E2%9A%92-algorithms-techniques)
*   [预定](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#book)
*   [常见问题解答](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#faq)
*   [支持](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#support)
*   [执照](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#license)

## 装置

您可以克隆回购协议或安装来自 NPM 的代码:

```
npm install dsa.js
```

Enter fullscreen mode Exit fullscreen mode

然后您可以将它导入到您的程序或 CLI 中

```
const { LinkedList, Queue, Stack } = require('dsa.js');
```

Enter fullscreen mode Exit fullscreen mode

所有公开的数据结构和算法的完整列表[见](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/src/index.js)。

## 特征

算法是一种…

</article>

[View on GitHub](https://github.com/amejiarosario/dsa.js-data-structures-algorithms-javascript)

让我们从定义什么是“平衡的树”和“不平衡的树”的陷阱开始。

# 平衡与不平衡二叉查找树

正如在[上一篇文章](https://dev.to/amejiarosario/tree-data-structures-explained-with-javascript-1d7d)中所讨论的，对于 BST 来说，最糟糕的噩梦就是按顺序给出数字(例如，1、2、3、4、5、6、7、...).

[![](../Images/818f4a0751a3a1de3ce4ed39bba5e90e.png "Balanced vs unbalanced Tree")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--alv7RLsf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://adrianmejia.com/images/balanced-vs-non-balanced-tree.jpg)

如果我们最终得到一棵像左边这样的树，我们就有麻烦了，因为性能会下降。为了确定一个节点是否在树上，当树不平衡时，你必须访问每个节点。这需要 *O(n)* ，而如果我们在每次插入或删除时保持节点平衡，我们可以得到 *O(log n)* 。

同样，这可能看起来差别不大，但是当您有一百万个节点时，差别是巨大的！我们正在讨论访问`1,000,000`节点与访问`20`！

“好吧，我卖了。我如何保持树的平衡？”我很高兴你问了😉。那么，我们先来学习一下什么时候判断一棵树是不平衡的。

# 一棵树什么时候平衡/不平衡？

看一看下面的树，并告诉哪一个是平衡的，哪一个是不平衡的。

[![](../Images/874ace5a4d7683f1a5a24bd2874fe41f.png "Full vs. Complete vs. Perfect Binary Tree")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1C4dgmyL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://adrianmejia.com/images/full-complete-perfect-binary-tree.jpg)

当一棵树是一棵完美的树时，它绝对是平衡的(树上的所有层次都有最大数量的节点)。但是
[全树](https://adrianmejia.com/blog/2018/06/11/data-structures-for-beginners-trees-binary-search-tree-tutorial/#Full-Complete-and-Perfect-binary-trees)或者[全树](https://adrianmejia.com/blog/2018/06/11/data-structures-for-beginners-trees-binary-search-tree-tutorial/#Full-Complete-and-Perfect-binary-trees)呢？

“完整的树”看起来有点平衡，对吗？整棵树呢？嗯，事情开始变得棘手了。让我们来定义一下。

如果满足以下条件，则树是平衡的:

1.  左侧子树高度和右侧子树高度最多相差 1。
2.  访问每个节点，确保满足规则 **#1** 。

> 注意:节点的高度是从最远的子节点到其自身的距离(边数)。

例如，如果你有一个有七个节点的树:

```
 10
    /   \
   5    20
  /     / \
 4    15   30
      /
     12 
```

Enter fullscreen mode Exit fullscreen mode

如果你递归地检查子树的[高度](https://adrianmejia.com/blog/2018/06/11/data-structures-for-beginners-trees-binary-search-tree-tutorial/#Trees-basic-concepts)(边计数到最远的叶节点)
，你会注意到它们之间的差异不会超过 1。

*   `10`后代:
    *   左子树`5`的高度为 1，而右子树`20`的高度为`2`。差别就这么一个:**平衡**！
*   `20`后代:
    *   左子树`15`的高度为`1`，而右子树`30`的高度为 0。所以差是`1` : **平衡**！

另一方面，看看这棵树:

```
 40
    /   \
   35    60*
  /     /
 25    50
      /
     45 
```

Enter fullscreen mode Exit fullscreen mode

让我们递归地检查子树的高度:

*   `40`后代:
    *   左子树`35`的高度为 1，而右子树`60`的高度为`2`。差别就这么一个:**平衡**！
*   `60`后代:
    *   左子树`50`的高度为`2`，而右子树(无)的高度为`0`。`2`和`0`相差不止一个，所以:**不平衡**！

希望现在你可以计算平衡和不平衡的树。

当我们发现一棵不平衡的树时，我们能做什么？我们做轮换！

如果我们像以前一样拿同一棵树，把`50`移到`60`的位置，我们得到如下:

```
 40
    /   \
   35    50
  /     /   \
 25    45    60* 
```

Enter fullscreen mode Exit fullscreen mode

向右旋转`60`后，就平衡了！让我们在下一节了解这一切。

# 树轮转

在抛出任何一行代码之前，让我们花点时间考虑如何使用旋转来平衡小树。

## 向左旋转

假设我们有下面这个值升序排列的树:`1-2-3`

```
 1*                                        2
  \                                       /  \
   2     ---| left-rotation(1) |-->      1*   3
    \
     3 
```

Enter fullscreen mode Exit fullscreen mode

为了在节点`1`上执行左旋转，我们将其向下移动，因为它是子节点(`2` ) **的左**后代。

[![](../Images/285a5e7ced525bae9275762d4574c814.png "Left rotate on 2")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OpFaWwUk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://adrianmejia.com/images/left-rotation2.gif)

这叫做**单次左转**或**左-左(LL)旋转**。

对于编码部分，我们再做一个例子:

```
 1                                 1
  \                                 \
   2*                                3
    \    --left-rotation(2)->       / \
     3                             2*  4
      \
       4 
```

Enter fullscreen mode Exit fullscreen mode

为了定义这棵树，我们使用了在[上一篇文章](https://adrianmejia.com/blog/2018/06/11/data-structures-for-beginners-trees-binary-search-tree-tutorial/#BST-Implementation)中开发的 [TreeNode](https://github.com/amejiarosario/dsa.js/blob/master/src/data-structures/trees/tree-node.js)
。

```
 const n1 = new TreeNode(1);
  const n2 = new TreeNode(2);
  const n3 = new TreeNode(3);
  const n4 = new TreeNode(4);

  n1.right = n2;
  n2.right = n3;
  n3.right = n4;

  const newParent = leftRotation(n2);
  console.log(newParent === n3); // true 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们向左旋转 2。让我们实现`leftRotation`函数。

[tree-rotations . js-left rotation](https://github.com/amejiarosario/dsa.js/blob/master/src/data-structures/trees/tree-rotations.js)

```
function leftRotation(node) {
  const newParent = node.right; // e.g. 3
  const grandparent = node.parent; // e.g. 1

  // make 1 the parent of 3 (previously was the parent of 2)
  swapParentChild(node, newParent, grandparent);

  // do LL rotation
  newParent.left = node; // makes 2 the left child of 3
  node.right = undefined; // clean 2's right child

  return newParent; // 3 is the new parent (previously was 2)
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们使用了一个名为`swapParentChild`的实用函数来交换父节点。

[tree-rotations . js-swapParentChild](https://github.com/amejiarosario/dsa.js/blob/master/src/data-structures/trees/tree-rotations.js)

```
function swapParentChild(oldChild, newChild, parent) {
  if (parent) {
    const side = oldChild.isParentRightChild ? 'right' : 'left';
    // this set parent child AND also
    parent[side] = newChild;
  } else {
    // no parent? so set it to null
    newChild.parent = null;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用这个函数使`1`成为`3`的父节点。我们也将使用它的旋转权。

## 向右旋转

我们有以下降序值的树`4-3-2-1` :

```
 4                                        4
     /                                        /
    3*                                       2
   /                                        /  \
  2       ---| right-rotation(3) |-->      1    3*
 /
1 
```

Enter fullscreen mode Exit fullscreen mode

为了在节点`3`上执行右旋转，我们将其作为其子节点`2`的**右**后代向下移动。

[![](../Images/49d425e0568ed2eafa849b4a5164cca6.png "Right rotate on 2")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xuFP7aR5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://adrianmejia.com/images/right-rotation2.gif)

这叫做**单右旋转**或**右-右(RR)旋转**。

代码与我们在左旋转中所做的非常相似:

[tree-rotations . js-right rotation](https://github.com/amejiarosario/dsa.js/blob/master/src/data-structures/trees/tree-rotations.js)

```
function rightRotation(node) {
  const newParent = node.left;
  const grandparent = node.parent;

  swapParentChild(node, newParent, grandparent);

  // do RR rotation
  newParent.right = node;
  node.left = undefined;

  return newParent;
} 
```

Enter fullscreen mode Exit fullscreen mode

`rightRotation`执行以下操作:

1.  首先，我们交换`4`的孩子:之前是`3`，交换之后是`2`(第 5 行)。
2.  后来，我们让`3`成为 2(第 8 行)的**的右边**的孩子
3.  最后，我们清除对 null 的`3`右子引用(第 9 行)。

既然知道了向左和向右的单一旋转是如何工作的，我们可以将它们结合起来:左右和左右旋转。

## 左右旋转

如果我们按照 3-1-2 的顺序在 BST 上插入值。我们会得到一棵不平衡的树。为了平衡这棵树，我们必须做一个`leftRightRotation(3)`。

```
 3*                                       2*
   /                                        /  \
  1    --| left-right-rotation(3) |->      1    3
   \
    2 
```

Enter fullscreen mode Exit fullscreen mode

双循环是我们在(LL 和 RR)中讨论的其他两种循环的组合:

如果我们将`left-right-rotation`展开成两个单旋转，我们将得到:

```
 3*                          3*
 /                          /                            2
1   -left-rotation(1)->    2    -right-rotation(3)->    /  \
 \                        /                            1    3*
  2                      1 
```

Enter fullscreen mode Exit fullscreen mode

*   左旋转(1):我们对节点的左子节点进行左旋转。如`1`。
*   右旋转(3):在同一节点上右旋转。如`3`。

[![](../Images/175986f403c0ea8b1c7f1ec08a3926ee.png "Left-Right rotate on 2")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NmlEIyQr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://adrianmejia.com/images/left-right-rotation.gif)

这种双重旋转称为**左右(LR)旋转**。

[tree-rotations . js-left right rotation](https://github.com/amejiarosario/dsa.js/blob/master/src/data-structures/trees/tree-rotations.js)

```
function leftRightRotation(node) {
  leftRotation(node.left);
  return rightRotation(node);
} 
```

Enter fullscreen mode Exit fullscreen mode

代码很简单，因为我们利用了之前使用的`leftRotation`和`rightRotation`。

## 左右旋转

当我们按照下面的顺序插入节点时:`1-3-2`，我们需要执行一个`rightLeftRotation(1)`来平衡树。

```
 1*                           1*
   \                            \                              2
     3   -right-rotation(3)->    2   -left-rotation(1)->      /  \
   /                              \                          1*   3
  2                                3 
```

Enter fullscreen mode Exit fullscreen mode

to 的代码与 LR 旋转非常相似:

[tree-rotations . js-right left rotation](https://github.com/amejiarosario/dsa.js/blob/master/src/data-structures/trees/tree-rotations.js)

```
function rightLeftRotation(node) {
  rightRotation(node.right);
  return leftRotation(node);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们知道平衡任何二叉树所需的所有旋转。让我们继续，使用 AVL 算法来保持插入/删除的平衡。

# AVL 树概述

**AVL 树**是发明的第一棵自平衡树。它以两位发明者 **A** 德尔森- **V** 埃尔斯基和 **L** 安迪斯的名字命名。在他们的自平衡算法中，如果一个子树与另一个子树最多相差一个，那么使用旋转来完成再平衡。

从前面的章节中，我们已经知道了如何进行旋转；下一步是计算出子树的高度。我们将调用**平衡因子**，给定节点上左右子树之间的差异。

> balance factor = leftSubtreeHeight-rightSubtreeHeight

如果平衡因子大于`1`或小于`-1`，那么我们知道我们需要平衡那个节点。我们可以把平衡函数写成如下:

[tree-rotations . js-balance](https://github.com/amejiarosario/dsa.js/blob/master/src/data-structures/trees/tree-rotations.js)T2】

```
function balance(node) {
  if (node.balanceFactor > 1) {
    // left subtree is higher than right subtree
    if (node.left.balanceFactor > 0) {
      rightRotation(node);
    } else if (node.left.balanceFactor < 0) {
      leftRightRotation(node);
    }
  } else if (node.balanceFactor < -1) {
    // right subtree is higher than left subtree
    if (node.right.balanceFactor < 0) {
      leftRotation(node);
    } else if (node.right.balanceFactor > 0) {
      rightLeftRotation(node);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

基于平衡因素，我们可以做四种不同的旋转:右后、左中、左后和左后。要知道我们要做什么旋转:

1.  看一看给定的`node`的`balanceFactor`。
2.  如果平衡因子是`-1`、`0`或`1`，我们就完成了。
3.  如果节点需要平衡，那么我们使用节点的左平衡因子或右平衡因子来判断它需要哪种旋转。

请注意，我们还没有实现`node.balanceFactor`属性，但是我们接下来会这样做。

实现子树高度的最简单的方法之一是使用递归。让我们继续向`TreeNode`类添加与高度相关的属性:

[tree-rotations.js - height，leftSubtreeHeight 和 rightSubtreeHeight](https://github.com/amejiarosario/dsa.js/blob/master/src/data-structures/trees/tree-rotations.js#L125)

```
 get height() {
    return Math.max(this.leftSubtreeHeight, this.rightSubtreeHeight);
  }

  get leftSubtreeHeight() {
    return this.left ? this.left.height + 1 : 0;
  }

  get rightSubtreeHeight() {
    return this.right ? this.right.height + 1 : 0;
  }

  get balanceFactor() {
    return this.leftSubtreeHeight - this.rightSubtreeHeight;
  } 
```

Enter fullscreen mode Exit fullscreen mode

为了更好地理解发生了什么，让我们做一些例子。

## 只有一个节点的树

让我们从单个根节点开始:

```
 40*
   /     \ 
```

Enter fullscreen mode Exit fullscreen mode

*   由于这个节点既没有左子节点也没有右子节点，那么`leftSubtreeHeight`和`rightSubtreeHeight`将返回`0`。
*   身高是`Math.max(this.leftSubtreeHeight, this.rightSubtreeHeight)`也就是`Math.max(0, 0)`，所以身高是`0`。
*   从`0 - 0 = 0`开始，平衡系数也为零。

## 有多个节点的树

让我们用多个节点试试:

```
 40
   /   \
  35    60
 /     /
25    50
     /
    45 
```

Enter fullscreen mode Exit fullscreen mode

**平衡系数(45)**

*   正如我们看到的，叶节点没有左或右子树，所以它们的高度是 0，因此平衡因子是 0。

**平衡系数(50)**

*   `leftSubtreeHeight = 1`和`rightSubtreeHeight = 0`。
*   `height = Math.max(1, 0)`，所以是`1`。
*   平衡系数是`1 - 0`，所以也是`1`。

**平衡系数(60)**

*   `leftSubtreeHeight = 2`和`rightSubtreeHeight = 0`。
*   `height = Math.max(2, 0)`，所以是`2`。
*   平衡因子是`2 - 0`，所以是`2`而且不平衡！

如果我们在我们开发的节点`60`上使用我们的`balance`函数，那么它将在`60`上做一个`rightRotation`，树将看起来像:

```
 40
   /   \
  35    50
 /     /   \
25    45    60* 
```

Enter fullscreen mode Exit fullscreen mode

以前树的高度(从根部算起)是 3，现在只有 2。

让我们把这些放在一起，解释我们如何在插入和删除时保持二叉查找树平衡。

# AVL 树的插入和删除

AVL 树只是常规二叉查找树(BST)之上的一层。添加/删除操作与 BST 中的相同，唯一的区别是我们在每次更改后运行`balance`函数。

让我们实现 AVL 树。

[avl-tree.js](https://github.com/amejiarosario/dsa.js/blob/master/src/data-structures/trees/avl-tree.js)

```
const BinarySearchTree = require('./binary-search-tree');
const { balanceUptream } = require('./tree-rotations');

class AvlTree extends BinarySearchTree {
  add(value) {
    const node = super.add(value);
    balanceUptream(node);
    return node;
  }

  remove(value) {
    const node = super.find(value);
    if (node) {
      const found = super.remove(value);
      balanceUptream(node.parent);
      return found;
    }

    return false;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您需要查看依赖关系，这里有实现的链接:

*   [二叉搜索树](https://github.com/amejiarosario/dsa.js/blob/master/src/data-structures/trees/binary-search-tree.js)
*   [树旋转](https://github.com/amejiarosario/dsa.js/blob/master/src/data-structures/trees/tree-rotations.js)

在插入或删除后执行`balanceUpstream`函数。

[tree-rotations . js-balance upstream](https://github.com/amejiarosario/dsa.js/blob/master/src/data-structures/trees/tree-rotations.js#L121)

```
function balanceUptream(node) {
  let current = node;
  while (current) {
    balance(current);
    current = current.parent;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在节点的父节点上递归地使用`balance`函数，直到到达根节点。

在下面的动画中，我们可以看到 AVL 树的插入和删除操作:

[![](../Images/9a0ca9f3432202e68a5d788187a1169b.png "AVL tree insertions and deletions")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i9zSzbsk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://adrianmejia.com/images/avl-tree-insert-remove.gif)

你也可以查看
[测试文件](https://github.com/amejiarosario/dsa.js/blob/master/src/data-structures/trees/avl-tree.spec.js)
来查看如何使用 AVL 树的更详细的例子。

那都是乡亲们！

# 总结

在这篇文章中，我们探讨了 AVL 树，它是一种特殊的二叉查找树，在插入和删除节点后会自我平衡。平衡一棵树的操作包括旋转，它们可以是单旋转或双旋转。

单次旋转:

*   向左旋转
*   向右旋转

双旋转:

*   左右旋转
*   左右旋转

你可以在
[Github](https://github.com/amejiarosario/dsa.js/tree/master/src/data-structures/trees) 中找到这里开发的所有代码。
你可以把它放在手边。