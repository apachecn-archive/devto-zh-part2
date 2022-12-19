# 代码一致性与使用新特性

> 原文：<https://dev.to/valenc3x/code-consistency-vs-using-new-features-38p2>

因此，假设您向一个现有的 Java 项目中添加了一个新特性，并且当添加单元测试时，您看到其他类似的测试正在使用空检查:

```
if(value != null){
    assertEquals("Value should be 10", 10, value);
} 
```

Enter fullscreen mode Exit fullscreen mode

但是你知道选项，所以你的测试应该是这样的:

```
Optional.ofNullable(value).ifPresent(value ->
    assertEquals("Value should be 10", 10, value)
); 
```

Enter fullscreen mode Exit fullscreen mode

另一个例子是 Python 中的类型注释

哪个对你更重要？保持代码一致，但不使用新特性，或者使用新特性，即使代码最终看起来不一致？

*仅考虑使用或不使用新功能不是基于性能、安全性或任何其他考虑的决定*

我最初的方法是保持它的一致性，但是在必要的时候添加一个低优先级的任务来重构代码，但是好奇的想知道其他人是怎么想的。