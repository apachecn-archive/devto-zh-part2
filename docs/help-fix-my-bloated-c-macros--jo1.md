# 帮我修复我臃肿的 C++宏！

> 原文：<https://dev.to/mortoray/help-fix-my-bloated-c-macros--jo1>

我一直在处理[叶](http://leaflang.org/)中的错误信息，这些使用了大量宏来减少代码大小并保持速度。我对他们不太满意。我甚至开始写一篇关于如何最小化它们的文章，但是意识到我不知道怎么做。有 C++高手有什么想法吗？

## 宏

测试用户代码错误的一个宏调用如下所示(检查元组索引是否在范围内)。

```
CHECK_RETURN( ctx, ex, index < tup->sub.size(), "index-out-of-range" ); 
```

Enter fullscreen mode Exit fullscreen mode

宏是这样的:

```
#define CHECK_RETURN( ctx_, stmt_, cond_, reason_, ... )    \
    if( !(cond_) ) { (ctx_).mark_failed(*(stmt_)).error( *(stmt_), (reason_),\
        { LOGGER_COND(cond_) }, { __VA_ARGS__ } ); \
        return (stmt_); } 
```

Enter fullscreen mode Exit fullscreen mode

其中`LOGGER_COND`添加了更多信息(假设`S__LINE__`是`__LINE__`的字符串版本)

```
#define LOGGER_COND(cond_) util::logger::item_debug_loc( __FILE__ "@" S__LINE__ ), \
    util::logger::item_debug_cond( #cond_ ) 
```

Enter fullscreen mode Exit fullscreen mode

## 原因

该宏满足几个要求:

*   它减少了冗余代码(这种类型的宏在源代码中使用了数百次)
*   除非条件失败，否则为记录器创建条目这一代价高昂的工作不会完成
*   它向记录器报告公共信息
*   它以一致的方式标记编译失败

## 但是讨厌！

但我就是不喜欢这样。我已经把宏保持得很短了，但不知何故，我觉得它可能会变短。延迟求值的需要和最终的`return`语句阻止我写一个做同样事情的函数——除非，这是我所希望的，有一个 C++14/17 的特性可以帮助我。

## 备注

宏中的`...` / `__VA__ARGS__`部分用于传递额外信息的地方:

```
CHECK_RETURN( ctx, field, msym, "unknown-field", util::logger::item_symbol(field->field) ); 
```

Enter fullscreen mode Exit fullscreen mode

宏有几个冗余的变体，一些抛出，另一些采用稍微不同的参数。我想要更干净的溶液。