# 继续前往戈朗

> 原文：<https://dev.to/trknhr/continue-goto-in-golang-31lk>

有时我们需要跳出循环，或者不想在循环中执行不必要的部分。
那个时候，我们用`continue`或者`break`。

*   `break`从内部使出循环。
*   `continue`使得不执行后一行

但是在嵌套循环中，情况会像这样复杂

```
list := []int{1,2,3,4,5,6,7,8,9,4,5,3,2,1}

anothers := []int{5,4,3,}
for _, item := range list {
    found := false
    for _, another := range anothers {
        if another == item {
            found = true
            break
        }

    }
    if found {
        continue;
    }
    fmt.Println("bingo", item)
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://play . golang . org/p/7q 0 umjvhcf-](https://play.golang.org/p/7Q0UMjVHcF-)

是的，当然有点丑。

所以你可以试着用`goto`
换清洁剂

```
list := []int{1,2,3,4,5,6,7,8,9,4,5,3,2,1}

anothers := []int{5,4,3,}
LabelLabel:
for _, item := range list {
    for _, another := range anothers {
        if another == item {
            continue LoopLabel
        }
    }
    fmt.Println("bingo", item)
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://play.golang.org/p/AB6ASJaL7TR](https://play.golang.org/p/AB6ASJaL7TR)

`continue Loop`出门到 LoopLabel 循环，是外循环，不是内循环。

那就比前一个干净。所以你可以在嵌套循环中使用`goto`作为`continue`。