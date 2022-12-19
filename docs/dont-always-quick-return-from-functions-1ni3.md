# 不要总是从函数中快速返回

> 原文：<https://dev.to/benwinding/dont-always-quick-return-from-functions-1ni3>

从程序中的函数返回有两种主要的方式。这两种风格几乎截然相反，但都有其用途。

### 单程返程

嵌套单返回的一个例子。这很难阅读和跟踪范围的级别。

```
int GetCost(request) {
    int cost = 0;
    if (request.Important) {
        if (request.HasDiscount) {
            if (request.IsMember) {
                cost = 10;
            }
            else {
                cost = 12;
            }
        }
        else {
            cost = 14;
        }
    }
    else {
        cost = 20;
    }
    return cost;
} 
```

### 快速返回

现在可以说代码可以通过使用预言性的“快速返回”变得更简单方法。这具有尽快从函数中返回的效果，使得代码更容易推理。此外，通过将 if 语句分解成一个简洁的堆栈，代码变得更容易阅读。

```
int GetCost(request) {
    if (!request.Important) 
        return 20;
    else if (!request.HasDiscount) 
        return 14;
    else if (!request.IsMember) 
        return 12;
    else
        return 10;
} 
```

# 然而...内存泄漏

编程早期不鼓励快速返回的原因是缺少垃圾收集。在函数执行期间，可能会分配内存。简单来说，函数只有一个出口点，以确保在函数返回之前，函数中的所有内存都被释放。

下面是另一个版本的 single return 函数(增加了一个检查对象)。更干净、更容易阅读，仍然只有一个 return 语句(并且稍微整理了一下)。

```
int GetCost(request) {
    Checker checker = new Checker(request);
    int cost = 0;
    if (!checker.Important) 
        cost = 20;
    else if (!checker.HasDiscount) 
        cost = 14;
    else if (!checker.IsMember) 
        cost = 12;
    else 
        cost = 10;
    delete checker;
    return cost;
} 
```

但是在函数执行结束时，内存可能不是唯一需要清理的东西。还有其他可能需要“释放”的东西，例如:

*   硬件资源
*   对象变异锁
*   网络连接

通过在一个函数中只返回一次，这些资源之一保持打开/锁定的机会大大减少。

所以，老一点的单退方式也不是完全没用！根据语言和环境的不同，快速返回并不总是最好的解决方案。