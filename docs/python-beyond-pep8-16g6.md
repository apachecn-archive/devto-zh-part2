# 超越 PEP8 的 Python

> 原文：<https://dev.to/edelvalle/python-beyond-pep8-16g6>

如果你写 Python 不知道什么是 [PEP8](https://www.python.org/dev/peps/pep-0008/) 去查一下[T3】现在 T5】。](https://www.python.org/dev/peps/pep-0008/)

PEP8 是 Python 代码的风格指南，我认为它非常好，我非常鼓励人们在 CI/CD 系统中运行测试之前，首先使用 linter + static analyzer 作为冒烟测试，并保持事情的整洁(在微观层面上，我在这里不讨论架构)。

但是这条指导方针在一些地方缺乏，因为它提倡的风格是脆弱的，可以改进的:

# 易碎压痕

这是 PEP8 能接受的风格，但是很脆弱。

```
# Aligned with opening delimiter. foo = long_function_name(var_one, var_two,
                         var_three, var_four=4) 
```

有些东西很容易破碎，通过改变`foo`或`long_function_name`的名称，很容易破坏这个缩进，并且第二行中的参数会立即错位。

```
# Aligned with opening delimiter. foo = refactoring(var_one, var_two,
                         var_three, var_four=4) 
```

有一些编辑器会自动为你纠正这个错误，但不是每个人都有。有时我也会发现像这样被抛弃的代码，这些代码来自不得不逃离着火的大楼或其他地方的人。

当长时间的函数调用在一起看起来像这样时，就不太好了

```
# Aligned with opening delimiter. foo = long_function_name(var_one, var_two,
                         var_three, var_four=4)
barf = another_long_function_name(var_one, var_two,
                                  var_three, var_four=4) 
```

其中即使在语义上两行处于相同的级别，空格的数量也是不规则的。只是视觉上的误导。

如果我们只是每行列出一个函数的参数，并以逗号结尾:

```
# Aligned with opening delimiter. foo = long_function_name(
    var_one, 
    var_two,
    var_three, 
    var_four=4,
) 
```

在这种情况下，当您更改函数的名称、删除或添加参数或者更改参数的名称时，缩进的其余部分不会断开。如果您添加了一个新的参数或者删除了一个参数，代码审查的差异将显示为每个修改的参数的单行变化:

```
 # Aligned with opening delimiter.
 foo = long_function_name(
     var_one, 
     var_two,
-    var_three, 
     var_four=4,
+    another_var=5,
 ) 
```

当你进行并排比较时，很容易发现变化。

# 线的延续和中断

在 Python 中，如果一行太长，你可以使用`\`继续下一行。在我个人看来，这看起来有点丑陋，你需要时刻记住所有的进口突破，例如:

```
from django.db.models.expressions import Case, Exists, Expression, \
    ExpressionList, ExpressionWrapper, F, Func, OuterRef, RowRange, Subquery, \
    Value, ValueRange, When, Window, WindowFrame 
```

如果你不得不删除或重命名某些内容，这些行的缩进长度会看起来很滑稽，你可能不得不将整个文本换行并重新排列所有的反斜杠。

那么，为什么不使用括号作为分组机制，就像在数学中一样:

```
from django.db.models.expressions import (
    Case, Exists, Expression, ExpressionList, ExpressionWrapper, F, Func,
    OuterRef, RowRange, Subquery, Value, ValueRange, When, Window, WindowFrame,
) 
```

或者也:

```
from django.db.models.expressions import (
    Case, 
    Exists, 
    Expression, 
    ExpressionList, 
    ExpressionWrapper,  
    F, 
    Func,
    OuterRef, 
    RowRange, 
    Subquery, 
    Value, 
    ValueRange, 
    When, 
    Window, 
    WindowFrame,
) 
```

我不喜欢最后一种，因为它太长了，通常导入不会经常改变，但最后一种风格也是可以接受的，并且具有我上面展示的示例 1 的所有优点，是重构证明。

当调用的东西太长时

```
posts = (
   Posts.objects
   .exclude(author=user)
   .filter(title__startswith='hellow')
   .first()
) 
```

当有一个长`if` :

```
if (not previous_is_grouped
        and not prev.has_two_images
        and nextone
        and not nextone.has_two_images):
    do_evil_stuff() 
```

在这里，我不喜欢 if 的第一行和其余部分之间的中断，但这很好。

同样在做长列表理解的时候:

```
double_of_evens = [
    x * 2
    for x in range(100)
    if x % 2 == 0
] 
```

这些行是映射、迭代和过滤。

# 关闭

一门语言和整个社区的通用风格指南是惊人的，我喜欢它。但是不要盲目，如果你真的认为有一个真正的理由把事情做得更好，那就迈出这一步。

想想那些不得不逃离着火建筑的废弃代码。英雄联盟😂。