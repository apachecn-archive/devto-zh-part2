# 用 JavaScript 解释图形数据结构

> 原文：<https://dev.to/amejiarosario/graph-data-structures-for-beginners-5edn>

在这篇文章中，我们将探索像图形这样的非线性数据结构。此外，我们将涵盖核心概念和典型应用。

你可能正在使用带有图形和树的程序。比方说，你想知道你的工作地点和家之间的最短路径；你可以用图算法得到答案！我们将研究这个和其他有趣的挑战。

在前一篇文章中，我们探索了线性数据结构，如数组、链表、集合、栈等等。这个建立在我们所学的基础上。

您可以在 Github repo:
中找到所有这些实现以及更多内容

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [阿梅加罗萨里奥](https://github.com/amejiarosario)/[DSA . js-数据-结构-算法-javascript](https://github.com/amejiarosario/dsa.js-data-structures-algorithms-javascript)

### 🥞JavaScript + eBook 中解释和实现的数据结构和算法

<article class="markdown-body entry-content container-lg" itemprop="text">

[![image](img/56b5d4dbac1233ee93c5155b15079c68.png)T2】](https://user-images.githubusercontent.com/418605/59557258-10742880-8fa3-11e9-84fb-4d66a9d89faa.png)

# JavaScript 中的数据结构和算法

[![CircleCI](img/d5922fc0cbf322196a6f5e893fcfcf37.png)](https://app.circleci.com/pipelines/github/amejiarosario/dsa.js-data-structures-algorithms-javascript)[![NPM version](img/2c26fb04f41469582f423d88af92342e.png)](https://badge.fury.io/js/dsa.js)[![chat](img/e028ff0be0803e8e9dbdd25d5a8c6ca4.png)](https://dsajs-slackin.herokuapp.com)

> 这是 DSA.js book 的编码实现和 NPM 包的回购。

> 在这个库中，您可以找到 JavaScript 中算法和数据结构的实现。这些材料可以作为开发人员的参考手册，或者您可以在面试前刷新特定主题。还有，你可以找到更高效解决问题的思路。

[![Interactive Data Structures](img/8f63557fae7adb265acdf574fa489c65.png)T2】](https://user-images.githubusercontent.com/418605/46118890-ba721180-c1d6-11e8-82bc-6a671428b422.png)

## 目录

*   [安装](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#installation)
*   [特性](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#features)
*   [里面有什么](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#whats-inside)
    *   [<g-emoji class="g-emoji" alias="chart_with_upwards_trend" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4c8.png">📈</g-emoji>算法分析](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#-algorithms-analysis)
    *   [<g-emoji class="g-emoji" alias="pancakes" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f95e.png">🥞</g-emoji>线性数据结构](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#-linear-data-structures)
    *   [<g-emoji class="g-emoji" alias="evergreen_tree" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f332.png">🌲</g-emoji>非线性数据结构](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#-non-linear-data-structures)
    *   [<g-emoji class="g-emoji" alias="hammer_and_pick" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2692.png">⚒</g-emoji> 算法技巧](https://raw.githubusercontent.com/amejiarosario/dsa.js-data-structures-algorithms-javascript/master/#%E2%9A%92-algorithms-techniques)
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

以下是我们将在本帖中涉及的操作的摘要:

|   | 邻接表 | 邻接矩阵 |
| --- | --- | --- |
| addVertex | [O(1)](//#Graph.addVertex) | [O(&#124;V&#124; <sup>2</sup> )](//#Matrix.addVertex) |
| 移除顶点 | [O(&#124;V&#124; + &#124;E&#124;)](//#Graph.removeVertex) | [O(&#124;V&#124; <sup>2</sup> )](//#Matrix.addVertex) |
| 添加边缘 | [O(1)](//#Graph.addEdge) | [O(1)](//#Matrix.addVertex) |
| removeEdge(使用数组) | [O(&#124;E&#124;)](//#Graph.removeEdge) | [O(1)](//#Matrix.addVertex) |
| removeEdge(使用 HashSet) | O(1) | [O(1)](//#Matrix.addVertex) |
| getAdjacents | [O(&#124;E&#124;)](//#Node.getAdjacents) | [O(&#124;V&#124;)](//#Matrix.getAdjacents) |
| isAdjacent(使用数组) | [O(&#124;E&#124;)](//#Node.getAdjacents) | [O(1)](//#Matrix.getAdjacents) |
| isAdjacent(使用 HashSet) | O(1) | [O(1)](//#Matrix.getAdjacents) |
| 空间复杂性 | [O(&#124;V&#124; + &#124;E&#124;)](//#List.space) | [O(&#124;V&#124; <sup>2</sup> )](//#Matrix.space) |

## 图表基础知识

在我们深入研究有趣的图形算法之前，让我们首先澄清命名约定和图形属性。

图是一种数据结构，其中一个**节点**可以有零个或多个相邻元素。

两个节点之间的连接称为**边**。节点也可以称为**顶点**。

[![](img/db7a8a1c47236d3f38092378ab4120bc.png "Graph is composed of vertices and edges")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NBsB8_rI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://adrianmejia.cimg/graph-parts.jpg)

**度**是连接到一个顶点的边的数量。例如，`purple`顶点的度数为 3，而`blue`顶点的度数为 1。

如果边是双向的，那么我们有一个无向图。但是，如果边有方向，那么我们就有了一个**有向图**(或者简称为**有向图**)。你可以把它想象成单行道(有向)或者双行道(无向)。

[![](img/c8a5ccfbbeb7b20e85bcd16d91b3e95a.png "Directed vs Undirected graph")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4gDDdkC---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://adrianmejia.cimg/directed-vs-undirected-graph.jpg)

顶点可以有通向自身的边(如`blue`节点)，这叫做**自循环**。

一个图可以有**个循环**，这意味着如果你遍历这个节点，你可以不止一次地得到同一个节点。没有圈的图称为**无圈图**。

[![](img/0ba8a55c2ae1d3a04c516b0806c49de7.png "Cyclic vs Acyclic directed graph")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w0cEdBfZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://adrianmejia.cimg/cyclic-vs-acyclic-directed-graph.jpg)

还有，无环无向图叫做**树**。我们将在下一篇文章中深入讨论树木。

图中并非所有顶点都必须相连。你可能有孤立的节点，甚至是分离的子图。如果所有节点至少有一条边，那么我们就有了一个**连通图**。当所有节点都连接到所有其他节点时，我们就有了一个**完整图**。

[![](img/3c493c8277ba09f5716b37fdcd554f36.png "Complete vs Connected graph")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--87PPbB8Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://adrianmejia.cimg/connected-vs-complete-graph.jpg)

对于一个完整的图，每个节点应该有`#nodes - 1`条边。在前面的例子中，我们有七个顶点，所以每个节点有六条边。

## 图形应用

当边被赋予价值/成本时，我们说我们有一个**加权图**。如果没有重量，我们可以假设它是 1。

[![](img/670dcf2e1eb46d5cd5f1e23c98364a37.png "Airports weighted graph")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--l4Q87zTs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://adrianmejia.cimg/airports-weighted-graph.jpg)

根据您需要解决问题的领域，加权图有许多应用。仅举几个例子:

*   航空交通(上图)

    *   节点/顶点=机场
    *   Edges =两个机场之间的直达航班
    *   重量=两个机场之间的英里数
*   GPS 导航

    *   节点=道路交叉口
    *   边缘=道路
    *   权重=从一个路口到另一个路口所需的时间
*   网络路由

    *   节点=服务器
    *   边缘=数据链路
    *   重量=连接速度

一般来说，图形在现实世界中有许多应用，例如:

*   电子电路
*   航班预订
*   驾驶方向
*   电信:手机发射塔频率规划
*   社交网络。例如，脸书使用图表来推荐朋友
*   推荐:亚马逊/网飞使用图表为产品/电影提供建议
*   图表有助于规划交付货物的物流

[![](img/de38c0cb36fdc8b245d5e093d629ae62.png "Graph applications: pathfinder")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--y5IP8ozn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://adrianmejia.cimg/map-graph.jpg)

我们刚刚学习了图形的基础知识和一些应用。让我们来看看如何用 JavaScript 表示图形。

## 代表图形

有两种主要的表示图形的方式:

1.  邻接表
2.  邻接矩阵

下面我们以下面的有向图(digraph)为例来解释一下:

[![](img/325071db3026dcf8636b73e73e7f6c41.png "digraph")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UwE0V3zU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://adrianmejia.cimg/digraph.png)

我们有 4 个节点的有向图。当一个顶点有一个到它自己的链接时(如`a`)称为**自环**。

### 邻接矩阵

邻接矩阵是使用二维数组(NxN 矩阵)表示图形的一种方式。在节点的交集中，如果它们是连通的，我们加 1(或其他权重)，如果它们是不连通的，我们加`0`或`-`。

使用与前面相同的示例，我们可以构建以下邻接矩阵:

```
 a b c d e
a 1 1 - - -
b - - 1 - -
c - - - 1 -
d - 1 1 - - 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，该矩阵横向和纵向列出了所有节点。如果连接很少，我们称之为**稀疏图**如果连接很多(接近最大链接数)，我们称之为**密集图**。如果所有可能的连接都达到了，那么我们就有了一个**完整图**。

需要注意的是，对于无向图，邻接矩阵将**总是**关于对角线对称。然而，在有向图中就不是这样了(就像我们的例子)。

寻找两个顶点的连接的时间复杂度是多少？

> 查询邻接矩阵中的两个节点是否相连需要一个常数时间或 *O(1)* 。

什么是空间复杂度？

> 将一个图存储为邻接矩阵，其空间复杂度为 *O(n <sup>2</sup> )* ，其中`n`为顶点数。还有，表示为 *O(|V| <sup>2</sup> )*

添加一个顶点的运行时间是多少？

这些顶点被存储为一个 *`V` *x* `V` *矩阵。所以，每增加一个顶点，矩阵都需要重构为一个 *`V+1` *x* `V+1` *。

> 在邻接矩阵上添加一个顶点是 *O(|V| <sup>2</sup> )*

如何获取相邻节点？

因为矩阵有一个 VxV 矩阵，要得到一个给定顶点的所有相邻节点，我们必须找到节点行并得到它与其他节点的所有边。

在我们之前的例子中，假设我们希望所有的相邻节点都到达`b`。我们必须得到 b 和所有其他节点所在的完整行。

```
 a b c d e
b - - 1 - - 
```

Enter fullscreen mode Exit fullscreen mode

我们必须访问所有节点，

> 获取邻接矩阵上的相邻节点是 *O(|V|)*

想象一下，你需要用一个图来表示脸书网络。你必须创建一个 20 亿 x 20 亿的矩阵，其中大部分是空的！没有人会认识其他人，最多只有几千人。

一般来说，我们处理稀疏图，所以矩阵会浪费很多空间。这就是为什么在大多数实现中我们会使用邻接表而不是矩阵。

### 邻接表

邻接表是最常用的表示图的方式之一。每个节点都有一个连接到它的所有节点的列表。

可以使用包含节点的数组(或 HashMap)将图表示为邻接表。这些节点条目中的每一个都包括一个列表(数组、链表、集合等)。)列出其相邻节点。

例如，在上图中，我们知道`a`与`b`有一个连接，并且还有一个自循环。反过来，`b`又与`c`有联系，以此类推:

```
a -> { a b }
b -> { c }
c -> { d }
d -> { b c } 
```

Enter fullscreen mode Exit fullscreen mode

可以想象，如果你想知道一个节点是否连接到另一个节点，你必须遍历这个列表。

> 查询邻接表中两个节点是否相连是 *O(n)* ，其中`n`是顶点数。也表示为 *O(|V|)*

空间复杂度呢？

> 将图存储为邻接表的空间复杂度为 *O(n)* ，其中`n`是顶点和边的总和。还有，表示为 *O(|V| + |E|)*

## 邻接表图 HashMap 实现

邻接表是最常见的表示图形的方式。实现邻接表有几种方法:

其中之一是使用散列表。`key`是节点的值，`value`是邻接的数组。

```
const graph = {
  a: ['a', 'b'],
  b: ['c'],
  c: ['d'],
  d: ['b', 'c']
} 
```

Enter fullscreen mode Exit fullscreen mode

图形通常需要以下操作:

*   添加和移除顶点
*   添加和移除边缘

添加和删除顶点涉及到更新邻接表。

假设我们要删除顶点`b`。我们可以做`delete graph['b'];`，但是，我们仍然必须删除“d”和“a”中邻接表上的引用。

每次我们删除一个节点，我们都必须遍历所有节点的列表 *O(|V| + |E|)* 。我们能做得更好吗？我们很快会回答这个问题，但是首先，让我们用一种更加面向对象的方式来实现我们的列表，这样我们就可以很容易地交换实现。

## 邻接表图 OO 实现

让我们从保存顶点值及其相邻顶点的`Node`类开始。我们还可以使用助手函数在列表中添加和删除附近的节点。

```
class Node {
  constructor(value) {
    this.value = value;
    this.adjacents = []; // adjacency list
  }

  addAdjacent(node) {
    this.adjacents.push(node);
  }

  removeAdjacent(node) {
    const index = this.adjacents.indexOf(node);
    if(index > -1) {
      this.adjacents.splice(index, 1);
      return node;
    }
  }

  getAdjacents() {
    return this.adjacents;
  }

  isAdjacent(node) {
    return this.adjacents.indexOf(node) > -1;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意`adjacent`运行时是 *O(1)* ，而`remove adjacent`是 *O(|E|)* 。如果我们用一个`HashSet` 🧐代替一个数组会怎么样？可能是 *O(1)* 。但是，让我们首先让它工作，然后我们可以让它更快。

> 让它工作。做正确的事。让它更快。

好了，现在我们有了`Node`类，让我们构建可以执行添加/删除顶点和边等操作的 Graph 类。

**图形.构造器**

```
class Graph {
  constructor(edgeDirection = Graph.DIRECTED) {
    this.nodes = new Map();
    this.edgeDirection = edgeDirection;
  }
  // ...
}

Graph.UNDIRECTED = Symbol('directed graph'); // one-way edges
Graph.DIRECTED = Symbol('undirected graph'); // two-ways edges 
```

Enter fullscreen mode Exit fullscreen mode

我们需要知道的第一件事是这个图是有向的还是无向的。当我们添加边时，这就有所不同了。

**Graph.addEdge**

要添加边，我们需要两个节点。一个是源头，一个是目的地。

```
 addEdge(source, destination) {
    const sourceNode = this.addVertex(source);
    const destinationNode = this.addVertex(destination);

    sourceNode.addAdjacent(destinationNode);

    if(this.edgeDirection === Graph.UNDIRECTED) {
      destinationNode.addAdjacent(sourceNode);
    }

    return [sourceNode, destinationNode];
  } 
```

Enter fullscreen mode Exit fullscreen mode

js

我们添加一条从源顶点到目的顶点的边。如果我们有一个无向图，那么我们也从目标节点添加到源节点，因为它是双向的。

> 从图邻接表添加边的运行时为: *O(1)*

如果我们试图添加一条边，而节点不存在，我们需要先创建它们。让我们接着做吧！

**Graph.addVertex**

我们创建一个节点的方法是将它添加到`this.nodes`图中。映射存储一个键/值对，其中`key`是顶点的值，而映射`value`是节点类的实例。看一下第 5-6 行:

```
 addVertex(value) {
    if(this.nodes.has(value)) {
      return this.nodes.get(value);
    } else {
      const vertex = new Node(value);
      this.nodes.set(value, vertex);
      return vertex;
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

如果节点已经存在，我们不想覆盖它。因此，我们首先检查它是否已经存在，如果不存在，我们就创建它。

> 从图邻接表中添加顶点的运行时为: *O(1)*

**Graph.removeVertex**

从图中删除一个节点，会稍微复杂一点。我们必须检查要删除的节点是否被用作相邻节点。

```
 removeVertex(value) {
    const current = this.nodes.get(value);
    if(current) {
      for (const node of this.nodes.values()) {
        node.removeAdjacent(current);
      }
    }
    return this.nodes.delete(value);
  } 
```

Enter fullscreen mode Exit fullscreen mode

我们必须遍历每个顶点，然后遍历每个相邻的节点(边)。

> 从图邻接表中删除顶点的运行时间是 *O(|V| + |E|)*

最后，让我们删除工具删除边缘！

**Graph.removeEdge**

移除边缘非常简单，类似于`addEdge`。

```
 removeEdge(source, destination) {
    const sourceNode = this.nodes.get(source);
    const destinationNode = this.nodes.get(destination);

    if(sourceNode && destinationNode) {
      sourceNode.removeAdjacent(destinationNode);

      if(this.edgeDirection === Graph.UNDIRECTED) {
        destinationNode.removeAdjacent(sourceNode);
      }
    }

    return [sourceNode, destinationNode];
  } 
```

Enter fullscreen mode Exit fullscreen mode

`addEdge`和`removeEdge`的主要区别在于:

*   如果顶点不存在，我们就不会创建它们。
*   我们用`Node.removeAdjacent`代替`Node.addAdjacent`。

由于`removeAdjacent`必须遍历所有相邻的顶点，我们有以下运行时间:

> 从图邻接表中删除边的运行时间是 *O(|E|)*

我们将探索如何从节点中搜索值。

## 广度优先搜索(BFS) -图形搜索

广度优先搜索是一种通过首先访问所有相邻节点来从初始顶点导航图的方法。

[![](img/e7a8752f29caba5829ecdfaaad679ead.png "Breadth First Search in a graph")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C3uid7FX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/5/5d/Breadth-First-Search-Algorithm.gif)

让我们看看如何在代码中实现这一点:

```
 *bfs(first) {
    const visited = new Map();
    const visitList = new Queue();

    visitList.add(first);

    while(!visitList.isEmpty()) {
      const node = visitList.remove();
      if(node && !visited.has(node)) {
        yield node;
        visited.set(node);
        node.getAdjacents().forEach(adj => visitList.add(adj));
      }
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们使用了一个`Queue`，其中第一个节点也是第一个被访问的节点(FIFO)。

我们也使用了 [JavaScript 生成器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator)，注意函数前面的`*`。这个生成器一次迭代一个值。这对大型图表(数百万个节点)很有用，因为在大多数情况下，您不需要访问每个节点。

这是一个如何使用我们刚刚创建的 BFS 的例子:

```
 const graph = new Graph(Graph.UNDIRECTED);

  const [first] = graph.addEdge(1, 2);
  graph.addEdge(1, 3);
  graph.addEdge(1, 4);
  graph.addEdge(5, 2);
  graph.addEdge(6, 3);
  graph.addEdge(7, 3);
  graph.addEdge(8, 4);
  graph.addEdge(9, 5);
  graph.addEdge(10, 6);

  bfsFromFirst = graph.bfs(first);

  bfsFromFirst.next().value.value; // 1
  bfsFromFirst.next().value.value; // 2
  bfsFromFirst.next().value.value; // 3
  bfsFromFirst.next().value.value; // 4
  // ... 
```

Enter fullscreen mode Exit fullscreen mode

你可以在[测试用例](https://github.com/amejiarosario/dsa.js/blob/master/src/data-structures/graphs/graph.spec.js)中找到更多的用法说明。让我们继续前进到 DFS！

## 深度优先搜索(DFS) -图形搜索

深度优先搜索是另一种通过递归找到每个顶点的第一个相邻节点来从初始顶点导航图的方法。

[![](img/b9fd33cb89efb2c5ef5def4c32ef99e4.png "Depth First Search in a graph")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oZj4k3xs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/7/7f/Depth-First-Search.gif)

DFS 的迭代实现与 BFS 是相同的，但是不使用`Queue`，而是使用`Stack`:

```
 *dfs(first) {
    const visited = new Map();
    const visitList = new Stack();

    visitList.add(first);

    while(!visitList.isEmpty()) {
      const node = visitList.remove();
      if(node && !visited.has(node)) {
        yield node;
        visited.set(node);
        node.getAdjacents().forEach(adj => visitList.add(adj));
      }
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

我们可以如下测试我们的图表。

```
 const graph = new Graph(Graph.UNDIRECTED);

  const [first] = graph.addEdge(1, 2);
  graph.addEdge(1, 3);
  graph.addEdge(1, 4);
  graph.addEdge(5, 2);
  graph.addEdge(6, 3);
  graph.addEdge(7, 3);
  graph.addEdge(8, 4);
  graph.addEdge(9, 5);
  graph.addEdge(10, 6);

  dfsFromFirst = graph.dfs(first);
  visitedOrder = Array.from(dfsFromFirst);
  const values = visitedOrder.map(node => node.value);
  console.log(values); // [1, 4, 8, 3, 7, 6, 10, 2, 5, 9] 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，该图在 BFS 和 DFS 上是相同的，但是，节点的访问顺序有很大不同。BFS 按照从 1 到 10 的顺序，而 DFS 在每个节点上尽可能地深入。

## 图形时空复杂度

我们已经看到了图的一些基本运算。如何添加和删除顶点和边？以下是我们到目前为止所学内容的总结:

|   | 邻接表 | 邻接矩阵 |
| --- | --- | --- |
| 空间 | [O(&#124;V&#124; + &#124;E&#124;)](//#List.space) | [O(&#124;V&#124; <sup>2</sup> )](//#Matrix.space) |
| addVertex | [O(1)](//#Graph.addVertex) | [O(&#124;V&#124; <sup>2</sup> )](//#Matrix.addVertex) |
| 移除顶点 | [O(&#124;V&#124; + &#124;E&#124;)](//#Graph.removeVertex) | [O(&#124;V&#124; <sup>2</sup> )](//#Matrix.addVertex) |
| 添加边缘 | [O(1)](//#Graph.addEdge) | [O(1)](//#Matrix.addVertex) |
| removeEdge(使用数组) | [O(&#124;E&#124;)](//#Graph.removeEdge) | [O(1)](//#Matrix.addVertex) |
| removeEdge(使用 HashSet) | O(1) | [O(1)](//#Matrix.addVertex) |
| getAdjacents | [O(&#124;E&#124;)](//#Node.getAdjacents) | [O(&#124;V&#124;)](//#Matrix.getAdjacents) |
| isAdjacent(使用数组) | [O(&#124;E&#124;)](//#Node.getAdjacents) | [O(1)](//#Matrix.getAdjacents) |
| isAdjacent(使用 HashSet) | O(1) | [O(1)](//#Matrix.getAdjacents) |

正如你所看到的，邻接表在几乎所有的操作中都更快。邻接矩阵优于邻接表的唯一作用是检查一个节点是否与其它节点相邻。但是，如果我们将实现从 Array 改为 HashSet，我们也可以在常量时间内得到它:)

## 总结

正如我们所见，图可以帮助建模许多现实生活场景，如机场、社交网络、互联网等。我们讨论了一些最基本的算法，比如广度优先搜索(BFS)和深度优先搜索(DFS)。此外，我们还研究了邻接表和矩阵等实现的折衷。订阅我的简讯，不要错过我的任何帖子，因为还有很多其他的应用我们很快就要学习，比如求节点间的最短路径，不同的精彩图算法！