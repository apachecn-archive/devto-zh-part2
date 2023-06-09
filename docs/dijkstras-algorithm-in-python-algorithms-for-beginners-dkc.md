# Dijkstra Python Dijkstra 在 Python 中的算法:初学者的算法

> 原文：<https://dev.to/mxl/dijkstras-algorithm-in-python-algorithms-for-beginners-dkc>

*Unsplash 上 Ishan @ seefromthesky 的照片*

Dijkstra 的算法可以为你找到一个图上两个节点之间的最短路径。这是任何程序员都必须知道的。它的[维基百科页面](https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm)里有好看的 gif 和历史。

在这篇文章中，我将使用来自 [Rosetta 代码](https://rosettacode.org/wiki/Dijkstra%27s_algorithm#Python)的经过时间考验的实现，只做了一点点修改，以便能够处理加权和未加权的图形数据，同时，我们将能够动态编辑图形。我将逐块解释代码。

## 算法

算法非常简单。Dijkstra 在 20 分钟内创建了它，现在你可以在同样的时间内学会编码。

1.  标记所有未访问的节点并存储它们。
2.  将初始节点的距离设置为零，将其他节点的距离设置为无穷大。
3.  选择距离最小的未访问节点，它现在是当前节点。
4.  查找当前节点的未访问邻居，并计算它们通过当前节点的距离。将新计算的距离与指定的距离进行比较，并保存较小的距离。例如，如果节点 A 的距离为 6，A-B 边的长度为 2，那么通过 A 到 B 的距离为 6 + 2 = 8。如果 B 之前标记的距离大于 8，则将其更改为 8。
5.  将当前节点标记为已访问，并将其从未访问的集合中删除。
6.  如果目的地节点已经被访问过(当规划两个特定节点之间的路径时)或者如果未被访问的节点之间的最小距离是无穷大，则停止。如果没有，重复步骤 3-6。

## Python 实现

首先，导入和数据格式。最初的实现建议使用 namedtuple 来存储边缘数据。我们会这样做，但是我们会在成本参数中添加一个默认值。有很多方法可以做到这一点，找到最适合你的。

```
from collections import deque, namedtuple

# we'll use infinity as a default distance to nodes. inf = float('inf')
Edge = namedtuple('Edge', 'start, end, cost')

def make_edge(start, end, cost=1):
    return Edge(start, end, cost) 
```

Enter fullscreen mode Exit fullscreen mode

让我们初始化我们的数据:

```
class Graph:
    def __init__(self, edges):
        # let's check that the data is right
        wrong_edges = [i for i in edges if len(i) not in [2, 3]]
        if wrong_edges:
            raise ValueError('Wrong edges data: {}'.format(wrong_edges))

        self.edges = [make_edge(*edge) for edge in edges] 
```

Enter fullscreen mode Exit fullscreen mode

让我们找到顶点。在最初的实现中，顶点是在 _ _ init _ _ 中定义的，但是我们需要它们在边改变时更新，所以我们将它们作为一个属性，它们将在我们每次处理属性时被重新计数。对于大图来说可能不是最好的解决方案，但是对于小图来说就可以了。

```
 @property
    def vertices(self):
        return set(
            # this piece of magic turns ([1,2], [3,4]) into [1, 2, 3, 4]
            # the set above makes it's elements unique.
            sum(
                ([edge.start, edge.end] for edge in self.edges), []
            )
        ) 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们添加添加和删除功能。

```
 def get_node_pairs(self, n1, n2, both_ends=True):
        if both_ends:
            node_pairs = [[n1, n2], [n2, n1]]
        else:
            node_pairs = [[n1, n2]]
        return node_pairs

    def remove_edge(self, n1, n2, both_ends=True):
        node_pairs = self.get_node_pairs(n1, n2, both_ends)
        edges = self.edges[:]
        for edge in edges:
            if [edge.start, edge.end] in node_pairs:
                self.edges.remove(edge)

    def add_edge(self, n1, n2, cost=1, both_ends=True):
        node_pairs = self.get_node_pairs(n1, n2, both_ends)
        for edge in self.edges:
            if [edge.start, edge.end] in node_pairs:
                return ValueError('Edge {} {} already exists'.format(n1, n2))

        self.edges.append(Edge(start=n1, end=n2, cost=cost))
        if both_ends:
            self.edges.append(Edge(start=n2, end=n1, cost=cost)) 
```

Enter fullscreen mode Exit fullscreen mode

让我们为每个节点寻找邻居:

```
 @property
    def neighbours(self):
        neighbours = {vertex: set() for vertex in self.vertices}
        for edge in self.edges:
            neighbours[edge.start].add((edge.end, edge.cost))

        return neighbours 
```

Enter fullscreen mode Exit fullscreen mode

算法时间到了！我重命名了变量，这样更容易理解。

```
 def dijkstra(self, source, dest):
        assert source in self.vertices, 'Such source node doesn\'t exist'

        # 1\. Mark all nodes unvisited and store them.
        # 2\. Set the distance to zero for our initial node 
        # and to infinity for other nodes.
        distances = {vertex: inf for vertex in self.vertices}
        previous_vertices = {
            vertex: None for vertex in self.vertices
        }
        distances[source] = 0
        vertices = self.vertices.copy()

        while vertices:
            # 3\. Select the unvisited node with the smallest distance, 
            # it's current node now.
            current_vertex = min(
                vertices, key=lambda vertex: distances[vertex])

            # 6\. Stop, if the smallest distance 
            # among the unvisited nodes is infinity.
            if distances[current_vertex] == inf:
                break

            # 4\. Find unvisited neighbors for the current node 
            # and calculate their distances through the current node.
            for neighbour, cost in self.neighbours[current_vertex]:
                alternative_route = distances[current_vertex] + cost

                # Compare the newly calculated distance to the assigned 
                # and save the smaller one.
                if alternative_route < distances[neighbour]:
                    distances[neighbour] = alternative_route
                    previous_vertices[neighbour] = current_vertex

            # 5\. Mark the current node as visited 
            # and remove it from the unvisited set.
            vertices.remove(current_vertex)

        path, current_vertex = deque(), dest
        while previous_vertices[current_vertex] is not None:
            path.appendleft(current_vertex)
            current_vertex = previous_vertices[current_vertex]
        if path:
            path.appendleft(current_vertex)
        return path 
```

Enter fullscreen mode Exit fullscreen mode

让我们使用它。

```
graph = Graph([
    ("a", "b", 7),  ("a", "c", 9),  ("a", "f", 14), ("b", "c", 10),
    ("b", "d", 15), ("c", "d", 11), ("c", "f", 2),  ("d", "e", 6),
    ("e", "f", 9)])

print(graph.dijkstra("a", "e"))
>>> deque(['a', 'c', 'd', 'e']) 
```

Enter fullscreen mode Exit fullscreen mode

## 整个代码来自上图:

```
from collections import deque, namedtuple

# we'll use infinity as a default distance to nodes. inf = float('inf')
Edge = namedtuple('Edge', 'start, end, cost')

def make_edge(start, end, cost=1):
  return Edge(start, end, cost)

class Graph:
    def __init__(self, edges):
        # let's check that the data is right
        wrong_edges = [i for i in edges if len(i) not in [2, 3]]
        if wrong_edges:
            raise ValueError('Wrong edges data: {}'.format(wrong_edges))

        self.edges = [make_edge(*edge) for edge in edges]

    @property
    def vertices(self):
        return set(
            sum(
                ([edge.start, edge.end] for edge in self.edges), []
            )
        )

    def get_node_pairs(self, n1, n2, both_ends=True):
        if both_ends:
            node_pairs = [[n1, n2], [n2, n1]]
        else:
            node_pairs = [[n1, n2]]
        return node_pairs

    def remove_edge(self, n1, n2, both_ends=True):
        node_pairs = self.get_node_pairs(n1, n2, both_ends)
        edges = self.edges[:]
        for edge in edges:
            if [edge.start, edge.end] in node_pairs:
                self.edges.remove(edge)

    def add_edge(self, n1, n2, cost=1, both_ends=True):
        node_pairs = self.get_node_pairs(n1, n2, both_ends)
        for edge in self.edges:
            if [edge.start, edge.end] in node_pairs:
                return ValueError('Edge {} {} already exists'.format(n1, n2))

        self.edges.append(Edge(start=n1, end=n2, cost=cost))
        if both_ends:
            self.edges.append(Edge(start=n2, end=n1, cost=cost))

    @property
    def neighbours(self):
        neighbours = {vertex: set() for vertex in self.vertices}
        for edge in self.edges:
            neighbours[edge.start].add((edge.end, edge.cost))

        return neighbours

    def dijkstra(self, source, dest):
        assert source in self.vertices, 'Such source node doesn\'t exist'
        distances = {vertex: inf for vertex in self.vertices}
        previous_vertices = {
            vertex: None for vertex in self.vertices
        }
        distances[source] = 0
        vertices = self.vertices.copy()

        while vertices:
            current_vertex = min(
                vertices, key=lambda vertex: distances[vertex])
            vertices.remove(current_vertex)
            if distances[current_vertex] == inf:
                break
            for neighbour, cost in self.neighbours[current_vertex]:
                alternative_route = distances[current_vertex] + cost
                if alternative_route < distances[neighbour]:
                    distances[neighbour] = alternative_route
                    previous_vertices[neighbour] = current_vertex

        path, current_vertex = deque(), dest
        while previous_vertices[current_vertex] is not None:
            path.appendleft(current_vertex)
            current_vertex = previous_vertices[current_vertex]
        if path:
            path.appendleft(current_vertex)
        return path

graph = Graph([
    ("a", "b", 7),  ("a", "c", 9),  ("a", "f", 14), ("b", "c", 10),
    ("b", "d", 15), ("c", "d", 11), ("c", "f", 2),  ("d", "e", 6),
    ("e", "f", 9)])

print(graph.dijkstra("a", "e")) 
```

Enter fullscreen mode Exit fullscreen mode

附言:对于那些和我一样，读了更多关于巫师而不是算法的书的人来说，是埃德格·迪克斯特拉，而不是西吉斯蒙德。