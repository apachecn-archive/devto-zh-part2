# 在窗口中绘制位图(叶-SDL #2)

> 原文：<https://dev.to/mortoray/drawing-a-bitmap-in-the-window-leaf-sdl-2--5dfk>

[https://www.youtube.com/embed/0N-qs8qv2dk](https://www.youtube.com/embed/0N-qs8qv2dk)

> 阅读[更新#1](https://mortoray.com/2018/02/28/new-game-a-quest-for-graphics-leaf-sdl-1/)

## 语法错误

自从上次更新以来，我解决了两个问题:一个错误消息和一个图像加载错误。

当在类型中使用未知符号时，会触发错误消息，例如:

```
var a : wacky 
```

Enter fullscreen mode Exit fullscreen mode

日志记录系统已经可以处理符号，但是这个转换代码没有正确地传递值。经过一点测试后，我让它在日志中正确地产生一个新的错误信息。

```
Unknown identifier `wacky` 
```

Enter fullscreen mode Exit fullscreen mode

## 图像加载和`as_abi_ptr`

在这段代码中，`sdl_blit_surface`的`check_sdl`报告了一个错误:

```
var image = sdl_load_bmp_rw( f, 1 )
//TODO: Check null `image`

q = sdl_blit_surface( image, 0, surface, 0 )
check_sdl( "sdl_blit_surface", q ) 
```

Enter fullscreen mode Exit fullscreen mode

暂时忽略那个`TODO`...我添加了代码来打印 SDL 错误消息。我们还没有一个库工具来打印或转换 ABI 空终止字符串；快速循环就行:

```
defn print_sdl_error = -> {
    var sx = sdl_get_error()
    var i = 0
    while sx#i != 0 {
        std.print( char_val(sx#i) )
        i += 1
    }
    std.println([])
} 
```

Enter fullscreen mode Exit fullscreen mode

错误是`SDL_UpperBlit: passed a NULL surface`。代码看起来是正确的，我认为值是没问题的，但是我没有办法测试它们。这就是`TODO`的意思。当打电话给`sdl_load_bmp_rw`时，我无法检查结果。为了查看它是否有效，我尝试打印图像的大小:

```
std.println(["image ", image.w, "x", image.h]) 
```

Enter fullscreen mode Exit fullscreen mode

这给出了一个 segfault，表明`image`肯定是无效的。

为了测试`null`,我需要一种方法来查看这个值。在 Leaf 中，a `value_ptr`是一个外部属性，不允许任何类型的直接操作(比如比较)。

我引入了`as_abi_ptr`内置函数来将一个`value_ptr`转换成一个`abi_ptr`，这是一个`binary`类型。我们可以比较这些，从而启用这个功能:

```
defn check_sdl_ptr = ( where : array｢char｣, item : abi_ptr ) -> {
    item != 0 then return
    std.println([where, " failed"])
    print_sdl_error()
    fail string_tag( "sdl-fail" )
} 
```

Enter fullscreen mode Exit fullscreen mode

称为:

```
var image = sdl_load_bmp_rw( f, 1 )
check_sdl_ptr( "sdl_load_bmp_rw", as_abi_ptr(image) ) 
```

Enter fullscreen mode Exit fullscreen mode

我现在可以看到神秘的错误。我在 Gimp 中重新保存了这个文件，它就消失了，留给我们的结果如下所示。

[![Screenshot](img/66baf49111305a9261ddbaa0d2cc86c4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7CtEQ299--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2018/03/screenshot_20180309_105012.png)

希望下一次我能画更多的矩形。

> 来看看[的](http://leaflang.org) [SDL 分店](https://code.launchpad.net/~eda-qa/leaflang/sdl)和的叶子吧。演示代码是 github 上的[，虽然我不能保证当前状态是什么🙈。一定要跟着我一起跳](https://github.com/mortoray/leaf-algorithms/tree/master/sdl) [Twitch](https://www.twitch.tv/mortoray) 。