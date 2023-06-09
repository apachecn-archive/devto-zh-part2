# 静态类型与测试==苹果与橘子

> 原文：<https://dev.to/rvprasad/static-typing-vs-testing--apples-vs-oranges-5cbn>

我听说过关于为什么静态类型作为降低系统中 bug 数量的方法优于测试(反之亦然)的讨论，我认为这样的讨论是徒劳的，因为它们是苹果和橘子的比较。

考虑下面的程序片段(用 Python 和类型提示编写)。

```
def foo(i: int, j: int) -> bool:
    return i < j

def bar(i: int, j: int) -> bool:
    return i == j 
```

如果我们只考虑这些函数的类型签名，那么我们有如下。

```
foo: (int, int) -> bool
bar: (int, int) -> bool 
```

仅使用类型签名，我们可以将以下调用标记为错误。

```
foo("1", 3)   # first argument is not of int type bar(3, [1,3]) # second argument is not of int type 
```

但是，类型签名不足以将下面的调用标记为错误。

```
if foo(3, 3):
    print("val1 is equal to val2")

if bar(3, 5):
    print("val1 is less than val2") 
```

现在，我们可能会认为，如果这些函数被命名为`equals`和`less_than`，这些错误就不会发生。通过重命名，观察到我们不依赖静态类型来避免错误:)

然而，让我们继续重命名这些函数。

```
def equals(i: int, j: int) -> bool:
    return i < j

def less_than(i: int, j: int) -> bool:
    return i == j

if equals(3, 3):
    print("val1 is equal to val2")

if less_than(3, 5):
    print("val1 is less than val2") 
```

即使现在，代码片段也是错误的。

问题是(至少)有两种类型，而我们只指定了一种类型。对于另一种类型，我们依赖于函数名和预期函数行为之间的关联。

为了理解这个问题，考虑对`equals`函数的描述:

1.  接受两个整数值 I 和 j，并返回一个布尔值。
2.  如果输入相等，则返回 true 否则为假。

描述的第一部分约束作为函数输入和输出的有效值。这个描述是函数的值域类型，我们使用类型提示(例如:int)来指定。

描述的第二部分约束了函数的行为。这种描述就是函数的行为类型(可以用不同方式实现的一种)。在上面的例子中，我们没有指定这种类型的信息。

> 当今大多数编程语言中可用的静态类型只能指定值类型(而不是行为类型)。

如果你仍然不相信行为类型，那么考虑只依赖函数签名`(List[int]) -> List[int]`并选择一个函数来对给定的整数列表进行升序排序。

# 测试呢？

与静态类型不同(正如现在普遍使用的那样)，测试与值类型无关。

> 测试是关于指定和检查行为类型的。

具体而言，测试用例捕捉功能的特定行为(例如，`equals(3,3)`应评估为真，`equals(3,5)`应评估为假)，测试套件捕捉功能的行为类型(即，预期行为的集合)，并且测试检查功能的实现是否是行为类型正确的(即，功能展示预期行为)。

为了与静态类型进行比较，测试套件类似于类型提示，测试类似于类型检查。

虽然测试看起来比静态类型要好，但事实并非如此。与静态类型不同，测试在指定被测函数的可能行为时既不简洁也不详尽(一般而言)。即使使用增强的测试技术，如基于属性的测试，也是如此。此外，通常不允许使用测试来确保值类型的正确性。

# 那么，这给我们留下了什么？

直到有一天我们有了静态类型系统，允许我们简单明了地指定值和行为类型，并使用它们来检查类型的正确性，我们将需要静态类型和测试来降低系统中的错误数量。

简而言之，静态类型和测试是互补的。现在，我们需要能够指定和推理类型，并创建和执行测试。