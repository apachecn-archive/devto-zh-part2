# 队列就是一个有 4 个不发音字母的 Q

> 原文：<https://dev.to/jackmarchant/a-queue-is-just-a-q-with-4-silent-letters-3k0i>

队列是一种集合数据结构，它使用 FIFO(先进先出)方法。这意味着当您将项目添加到队列中时(通常称为入队)，该项目将位于队列的末尾。当您将一个项目出队时，我们会将该项目从队列的前面移除。这两种方法都会改变队列的长度。可以实现一个 peek 方法来查看队列中的第一项是什么，而不删除该项，使队列保持不变。

队列通常用列表数据结构来实现，比如链表。在 Elixir 中，列表是幕后的单链表。我们能够访问列表的头部和尾部，它们分别指列表中的第一项和列表的其余部分。

```
[head | tail] = [1, 2, 3]
> head
1
> tail
[2, 3] 
```

如果考虑在 Elixir 中实现队列，我们需要实现以下方法:

*   使…入队
*   出列
*   偷看
*   让我们分别来看看这些。

## 入队

在向 Elixir 中的列表添加项目时，通常会在列表前添加项目，然后在访问列表时反转项目以保持顺序。由于 Elixir 中的列表是单向链接的，所以添加到列表的前面要比添加到列表的末尾并重新创建列表中的所有链接要快得多。

出于这个原因，您可以这样实现一个入队函数:

```
@spec enqueue(list(), any()) :: list()
def enqueue(queue, item) do
  [item | queue]
end 
```

## 出列

既然列表中有了条目，我们需要一种方法在调用出列时删除一个条目。我开始查看 Elixir 是如何从列表中删除条目的，并发现了[List.delete/2](https://github.com/elixir-lang/elixir/blob/v1.6.5/lib/elixir/lib/list.ex#L123)——我们可以在那里看到一些函数头，但这里有两行你需要欣赏:

```
def delete([item | list], item), do: list  
def delete([other | list], item), do: [other | delete(list, item)] 
```

第一个参数是列表，第二个参数是要移除的项目。Elixir 将第二个参数名称绑定为与列表头相同的值，如果调用该函数，它将返回尾部(从而从列表中删除该项)。否则，如果这两个项目变量不匹配，头部将被前置，而`delete/2`将在尾部被递归调用。

这可能有点难以接受，但我建议在交互式的 Elixir shell 中尝试一下。

虽然在这种情况下我们不需要`List.delete/2`，但是我们可以像这样实现一个出列函数:

```
@spec dequeue(list()) :: {any(), list()}
def dequeue([]), do: nil
def dequeue(queue) when length(queue) <= 2 do
 [item | tail] = Enum.reverse(queue)
 {item, tail}
end
def dequeue(queue) do 
 {Enum.at(queue, -1), Enum.drop(queue, -1)}
end 
```

我们在这个函数中返回一个 tuple，因为我们既想知道出列的项，又想知道队列中剩余的项(这样我们可以在以后让更多的项入队)。同样值得注意的是，这不是最有效的解决方案，因为它使用的是`O(n)`算法，因为我们使用的`Enum`方法总是要枚举列表以获得最后一项。

## 偷看

peek 函数是一个简单的工具，允许查看队列的前面，而不改变队列本身。虽然，你可能想要添加一些额外的函数头来满足空列表的需要。

```
@spec peek(list()) :: any() | nil
def peek([]), do: nil
def peek(queue) do
  [h | _ ] = queue
  h
end 
```

## 计数

类似地，count 是仍在队列中的项目数，可以这样实现:

```
@spec count(list()) :: integer()
def count([]), do: 0
def count(queue), do: length(queue) 
```

这些函数在理论上都很好，但是当我们开始考虑在 Elixir 中实现队列时，我们不能将它封装在一个知道自己状态的类中。相反，我们可以将这些功能实现为 GenServer 的一部分，它将保持自己的状态，并可以随着时间的推移进行更新。

## 优先级队列

当简单的 FIFO 不能满足要求，并且你需要能够在其他项目之前处理队列中的项目时，我们可以实现优先级队列。这意味着当一个项目入队时，它不一定会排在队列的后面(或在 Elixir 中排在列表的前面)，每个新项目都需要与其他项目进行比较，直到我们根据它的优先级为它找到合适的位置。

优先级可以是整数值，例如，数字 10 的优先级高于 5，因为它是较高的值。想象一下下面的队列:

```
head -> 7 - 3 - 1 <- tail 
```

如果我们将优先级基于较高的整数值，并且我们将 10 添加到这个队列中，我们将期望它优先于所有其他值，因为它是最高的。所以我们只剩下下面的队列:

```
head -> 10 - 7 - 3 - 1 <- tail 
```

如果新项目的优先级不高于任何现有项目，它将被简单地添加到队列的末尾。

队列在现实世界中有多种应用，比如调度异步工作或处理大量请求。高优先级的请求可以先处理，低优先级的请求稍后处理。

我决定用一个`GenServer`来实现一个简单的[队列库](https://hex.pm/packages/pex_queue)，并且可选优先级，你可以看看[文档](https://hex.pm/packages/pex_queue)或者直接进入[代码](https://github.com/jackmarchant/pex_queue/blob/master/lib/pexqueue.ex)