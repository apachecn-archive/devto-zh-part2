# Python for 循环的 Else 子句

> 原文：<https://dev.to/mxl/else-for-for-loop-44lc>

*照片由[凯利·西克玛](https://unsplash.com/@kellysikkema)在[Unsplash](https://unsplash.com)T5 拍摄*

不是每个人都知道，但是有一个 for 循环的“else”子句。
经常看到这个:

```
did_something = False
for element in elements:
    if element.something:
        do_something()
        did_something = True
        break

if not did_something:
    do_something_else() 
```

Enter fullscreen mode Exit fullscreen mode

但是应该是这样的:

```
for element in elements:
    if element.something:
        do_something()
        break
else:
    do_something_else() 
```

Enter fullscreen mode Exit fullscreen mode

Else 子句仅在 for 循环自然退出时执行，没有任何 break 语句。就这么简单。