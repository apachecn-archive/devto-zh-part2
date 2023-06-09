# 直到为什么和应该有两个表示。

> 原文：<https://dev.to/kspeakman/til-why-and-should-have-two-representations-4f7n>

在大多数语言中，概念和有两种不同的表示法。例如，在 F#中有*逻辑*和:

```
let awesome = good && great 
```

Enter fullscreen mode Exit fullscreen mode

然后就是*按位*和:

```
// this is pretty common when working with Flags
let hasRed = someColors &&& Color.Red = Color.Red 
```

Enter fullscreen mode Exit fullscreen mode

但是还有 VB，这两个概念都使用关键字`And`。所以我在 VB 中实现了一个功能，克隆一个图像。我有一段代码。

```
 Public Shared Function CloneImage(ByVal sourceImage As Bitmap) As Bitmap
        If sourceImage.PixelFormat And PixelFormat.Indexed <> 0 Then
            Return CreateIndexedImage(sourceImage)
        Else
            Return CreateNonIndexedImage(sourceImage)
        End If
    End Function 
```

Enter fullscreen mode Exit fullscreen mode

这段代码总是调用`CreateIndexedImage`。我花了一点时间才明白为什么。这是因为该语言将关键字`And`视为优先级最低的操作符，并最后对其求值。它这样做是因为它只考虑了*逻辑*和`And`的优先性。因此，实际的评估类似于`(sourceImage.PixelFormat) And (PixelFormat.Indexed <> 0)`。VB 强制 PixelFormat 的类型为 true，第二个条件将始终为 true。

修复方法是添加括号

```
 If (sourceImage.PixelFormat And PixelFormat.Indexed) <> 0 Then 
```

Enter fullscreen mode Exit fullscreen mode

但是这可以通过简单地在两种使用之间使用不同的操作符来避免。那么每一个都可以有不同的优先顺序。