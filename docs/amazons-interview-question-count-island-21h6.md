# 亚马逊的面试问题:数岛

> 原文：<https://dev.to/rattanakchea/amazons-interview-question-count-island-21h6>

去年我在亚马逊面试的时候，被问到的第一个问题是写一个函数`get_number_of_islands(matrix)`。这是我第一次看到这个问题。对我来说很难。回想起来，我想分享我所学到的和解决这个问题的方法。

## 问题

给定一个由`0s`和`1s`组成的二维数组`matrix`，计算`1s`的岛屿数。一个岛被一组都是`1s`的相邻细胞所包围。一个单元格只能水平和垂直相邻。

### 举例

```
input:  binaryMatrix = [ [0,    1,    0,    1,    0],
                         [0,    0,    1,    1,    1],
                         [1,    0,    0,    1,    0],
                         [0,    1,    1,    0,    0],
                         [1,    0,    1,    0,    1] ]

output: 6  # there are six islands 
```

## 逼近

首先，你应该向面试官提问以澄清问题，解释问题以确保你理解正确，和/或陈述任何假设。

*   矩阵的列数或行数是否相同？(面试官:应该没关系)
*   我们假设单元格只能水平和垂直相邻，**不能对角**？(正确)
*   该函数应该正确处理空矩阵和越界数据访问。

第二，现在你可以大声地说出解决这个问题的过程。

好的，所以我们想要*遍历矩阵中的每个单元格*。然后我们可以检查单元格是`1`还是`0`。

这是一个`0`，它不是一个岛，我们可以继续。如果是`1`，那就保证我们找到了 **1** 岛。(听起来像是基础案例？)在这一点上，我们需要一种机制来使这个单元成为**访问过的**，所以下一次我们可以跳过它以避免重复计数。之后，我们要查看所有的**相邻的**单元格来重复这个过程(递归)。

**伪代码**

```
def main():
    visited [] # array to keep track of visited cell
    num_islands = 0 # start with zero
    for each row, col in matrix
          num_island += get_number_of_islands(matrix, row, col) #return 1 if it is an island return num_island
def get_number_of_islands(matrix, row, col, visited):
    check if row and col is out of bound of the matrix
    check if we already visited the cell with row, col
    check if the cell is 0
    => return 0

    mark the cell as visited in the visited array
    recursive call get_number_of_islands() on each adjacent cell
    return 1 
```

## 解(Python)

```
def get_number_of_islands(binaryMatrix):
    rows = len(binaryMatrix)
    cols = len(binaryMatrix[0])
    # you can use Set if you like
    # or change the content of binaryMatrix as it is visited
    visited = [[0 for col in range(cols)] for r in range(rows)]
    number_of_island = 0
    for row in range(rows):
        for col in range(cols):
            number_of_island += get_island(binaryMatrix, row, col, visited)
    return number_of_island

# get a continuous island def get_island(binaryMatrix, row, col, visited):
    if not is_valid(binaryMatrix, row, col)
        or visited[row][col] == 1 or binaryMatrix[row][col] == 0:
        return 0

    # mark as visited
    visited[row][col] = 1
    get_island(binaryMatrix, row, col + 1, visited)
    get_island(binaryMatrix, row, col - 1, visited)
    get_island(binaryMatrix, row + 1, col, visited)
    get_island(binaryMatrix, row - 1, col, visited)
    return 1

def is_valid(binaryMatrix, row, col):
    rows = len(binaryMatrix)
    cols = len(binaryMatrix[0])
    return row >= 0 and row < rows and col >= 0 and col < cols 
```

## 额外

*   时间和空间的复杂性是什么？
*   问题变型:找到面积最大的岛？在上述情况下，返回`5`
*   这个问题也可以在不使用递归的情况下解决。但是需要一个队列或堆栈来跟踪要访问的相邻单元。

## 备注

这个问题可能会很难，会让被采访者(包括我自己)因为很多原因而被卡住。

*   不熟悉基本的**图**数据结构和 **DFS** 或 **BFS** 遍历的概念。这被认为是一个有邻接矩阵的图。
*   卡在处理基本情况和递归情况。
*   面试时间限制

*封面图片来自 unsplash.com