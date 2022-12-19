# 具有当前函数名的宏-_ _ func _ _ vs _ _ func _ _

> 原文：<https://dev.to/reg__/macro-with-current-function-name---func-vs-function-4k0j>

今天，在用 C++编程时，我想写一个类似 assert 的宏，当给定的条件不满足时，它会抛出一个异常。我想在消息字符串中包含尽可能多的信息。我知道条件表达式，也就是我的宏的参数，可以通过使用`#`预处理操作符变成一个字符串。

接下来，我寻找了一个也能获得当前函数名称的方法。一开始我找到了`__func__`，如这里描述的(c++ 11)，这里描述的(C99)。不幸的是，以下代码无法编译:

```
#define CHECK(cond) if(!(cond)) { \
    throw std::runtime_error("ERROR: Condition " #cond " in function " __func__);

void ProcessData()
{
    CHECK(itemCount > 0); // Compilation error!
    // (...)
} 
```

这是因为这个标识符实际上是一个隐式的局部变量`static const char __func__ [] = "..."`。

然后我想起 Visual Studio 将`__FUNCTION__`宏定义为自定义的微软扩展。在这里看文档[。这个如我所料地工作——它可以与其他字符串连接，因为它是一个字符串文字。以下宏定义解决了该问题:](https://docs.microsoft.com/en-us/cpp/preprocessor/predefined-macros?view=vs-2017)

```
#define CHECK(cond) if(!(cond)) \
    { throw std::runtime_error("ERROR: Condition " #cond " in function " __FUNCTION__); } 
```

当`itemCount`为 0 时，抛出异常，`ex.what()`返回如下字符串:

> 错误:函数 ProcessData 中的条件 itemCount > 0

良好的...对于任何有经验的 C++开发人员来说，C++标准委员会提出在实践中远远没有用的解决方案应该是不足为奇的:)