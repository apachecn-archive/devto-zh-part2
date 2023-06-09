# 树.数据结构.第 1 部分

> 原文：<https://dev.to/mkhy19/trees---data-structure---part-1--9n3>

## 二叉查找树是计算机科学中非常常见的一种树。

二叉查找树(BST)是由二进制定义的，这意味着每个节点最多有两个子节点。我们有这样的属性，在根节点，根节点的值大于或等于左边子节点中的所有节点，它小于右边子节点中的节点。

[![Alt Stack](img/e53bb18ab3430e24cfeea47353138dba.png "example")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VEybbFcm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Iac1ASO.jpg)

### 在这个例子中

这里的小于或大于，我们说的是字母顺序而不是数字。所以 Les 比 Alex，Cathy 和 Frank 大，但是比 Nancy，Sam，Violet，Tony 和 Wendy 小。同样的道理也适用于树中的每个节点都有相同的东西。
**比如**，紫罗兰大于等于托尼，严格来说小于温迪。

### 为什么使用 BST？

二叉查找树可以让你快速搜索。
**例如:**

*   如果我们想在这棵树中寻找托尼，我们可以从 Les 开始。
*   注意托尼比莱斯大，所以我们要向右走。
*   请注意，托尼比萨姆大，所以我们要向右走。
*   注意，托尼比维奥莱特小，所以我们向左走，然后找到托尼。

我们只做了四次比较。这很像排序数组中的二分搜索法。所以树的实际定义是...

### 一棵树要么是空的，要么是一个有键的节点，并且有一个子树列表....这是一个递归定义。

所以如果我们回到这里的例子:

*   Les 是一个节点，它有键 Les 和两个子树，Cathy 子树和 Sam 子树。
*   Cathy 子树是一个节点，有一个 key Cathy 和两个子树，即 Alex 子树和 Frank 子树。
*   让我们来看看弗兰克的孩子树。这是一个节点，有一个键 Frank 和两个，它有子树吗？？？？？？不，它没有子树。

## 我们来看看其他一些树木的术语。

[![Alt Stack](img/3b80c6cf41c0209f673c44dfedb35c79.png "example")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--epHeuVmt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/I07SBz8.png)

*   根:是树的顶端节点，所以在这里，Fred 是树的根。
*   **一个孩子**:有一个父代的直系后代，Kate 是 Sam 的父代，Sam 是 Kate 的孩子。
*   弗雷德的孩子是凯特、莎莉和吉姆。
*   **一个祖先**:是父母或者父母的父母等等。所以萨姆的祖先是凯特和弗雷德。休的祖先也是凯特和弗雷德。莎莉的祖先只是弗雷德。
*   **后代**是祖先的倒数，所以它是 child 的 child 或 child，依此类推。所以弗雷德的后代是所有其他节点，因为它是根，山姆，休，凯特，莎莉和吉姆。凯特的后代只会是萨姆和休。
*   **兄弟姐妹**是共享同一个父节点的两个节点，所以凯特、莎莉和吉姆都是兄弟姐妹。萨姆和休也是兄弟姐妹。
*   **叶子**是没有孩子的节点。那就是萨姆、休、莎莉和吉姆。
*   **内部节点(非叶子)**:所有非叶子节点。这是凯特和弗雷德...另一种描述方式是所有节点都有子节点。

*   一个级别 : 1 加上根和一个节点之间的边数，
    1——让我们考虑一下。弗雷德，根和弗雷德节点之间有多少条边？？？因为弗雷德节点是根，所以没有边。所以它的级别是 0+1=1。
    2- Kate 在 Fred 和 Kate 之间有一条边，所以它的级别是 1+1=2，还有它的兄弟姐妹 Sally 和 Jim。
    3 级——山姆和休是 3 级。

*   **高度**:最远的叶子
    1 中的子树节点的最大深度——所以这里我们想看看，例如，如果我们想看看弗雷德的高度，我们想看看它最远的后代是什么。
    所以它的最下一代不是山姆就是休。
    它的高度应该是 3。
    2-所以叶(山姆或休)的高度是 1。3-凯特的身高是 2。弗雷德的身高是 3。

*   **森林**:我们也有森林的概念。所以这是一个树的集合..这里有两棵树，一个是凯特，一个是莎莉，它们形成了一片森林。

[![Alt Stack](img/7a1ea89d77339df5c359d5867729de84.png "Forest")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ylAJBR2l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/q6aBy6l.png)

## 二叉树非常常用。

所以一棵二叉树最多有两个子树。对于一棵二叉树，我们通常有一个明确的左右子树，它们中的任何一个都可以为零，而不是在这个一般的子树列表中。

## 让我们来看几个在树上运行的程序。

由于树是递归定义的，所以编写对自身递归的树进行操作的例程是很常见的。例如，如果我们想计算一棵树的高度，也就是一个根节点的高度:
我们可以继续递归地做，遍历整个树。所以我们可以说:

*   例如，如果我们有一棵零树，那么它的高度是 0。
*   否则，我们就是 1 加上左子树和右子树的最大值。
*   如果我们看一片叶子，它的高度是 1，因为左边孩子的高度是 0，右边孩子的高度也是 0。所以最大值是 0，然后 1 加 0。

### (树高)算法

```
if tree = nil:
    return 0
return 1 + Max(Height(tree.left),Height(tree.right)) 
```

下面是 C++中的代码

```
int maxDepth(struct node* node)
{
    if (node==NULL)
        return 0;
   else
   {
       int rDepth = maxDepth(node->right);
       int lDepth = maxDepth(node->left);

       if (lDepth > rDepth)
       {
           return(lDepth+1);
       }
       else
       {
            return(rDepth+1);
       }
   }
} 
```

2-我们也可以考虑计算一棵树的大小，也就是节点的数量。

*   同样，如果我们有一棵零树，我们有零个节点。
*   否则，我们将得到左侧子节点的数量加上 1，再加上右侧子节点的数量。所以 1 加上左边树的大小加上右边树的大小。

### (树型大小)算法

```
if tree = nil
    return 0
return 1 + Size(tree.left) + Size(tree.right) 
```

下面是 C++中的代码

```
int treeSize(struct node* node)
{
    if (node==NULL)
        return 0;
    else
        return 1+(treeSize(node->left) + treeSize(node->right));
} 
```

## 下面是完整的例子: