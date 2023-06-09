# 新游戏:对图形的追求(利夫-SDL #1)

> 原文：<https://dev.to/mortoray/new-game-a-quest-for-graphics-leaf-sdl-1-n16>

我开始了一段旅程，让 Leaf 更具视觉效果。你可能已经看过我的一些关于复杂性的视频，用抖动的鼠标画出线条。这还不够好，尤其是如果我想展示树的旋转是如何工作的。我想把那些做得更好，但是我想在[叶子](http://leaflang.org/)里做。

用 Leaf 编写完整的图形解决方案还为时过早；我需要使用现有的解决方案。最好是尽可能简单的语言，因为集成还不是这种语言的强项。进入 SDL - [简单 DirectMedia 层](https://www.libsdl.org/)。

## 定义 SDL_Init

第一步是初始化 SDL 库。这看起来很简单，但却是编写语言的重要一步。

为了调用一个外部函数，我们需要将它导入到 Leaf 中，看起来像这样:

```
@import( "SDL_Init" ) multi sdl_init : ( flags : binary 32bit ) ->  ( : abi_int ) raw no_throw 
```

Enter fullscreen mode Exit fullscreen mode

`@import( "SDL_Init")`代码通过名称创建一个导入。链接器需要解析此符号。

`multi`只是一个叶子，它允许`sdl_init`有多个重载。这里有其他的选择，像`var`和`defn`，并且有可能`multi`不能长期生存。

下一位`( flags : binary 32bit ) -> ( : abi_int ) raw no_throw`是函数的类型。第一部分要明确；这是一个采用 32 位整数标志参数并返回整数结果的函数。`abi_int`中的`abi`是指操作系统的应用二进制接口:该系统上类型的通用定义。它可能和一片叶子的大小不一样。

`raw`有点难懂。在 Leaf 中，函数变量可以表示绑定、未绑定或原始函数。绑定指的是调用函数所需的上下文(想象一个带有`this`变量的类函数)。在 Leaf 代码中，您很少考虑这种类型，但是当使用外部库时，这很重要。这些将永远是`raw`类型，因为它们没有上下文或绑定。

`no_throw`表示不产生任何错误。至少在 Leaf 报告错误的意义上是这样的:返回的`abi_int`仍然是一个错误代码。术语`no_throw`可能会被替换为`no_error`，因为`throw`是一个专用于异常的术语，Leaf 不再使用(至少不再使用)。

## 调用 SDL_Init

我们现在可以调用这个函数:

```
var q = sdl_init( SDL_INIT_VIDEO )
std.println([ "sdl_init: ", q]) 
```

Enter fullscreen mode Exit fullscreen mode

SDL _ 初始化 _ 视频是库中的一个标志值:

```
literal SDL_INIT_VIDEO =  0x0000_0020 
```

Enter fullscreen mode Exit fullscreen mode

注意这里没有类型:这是 Leaf 的一个特性，常量可以是有理数。当您使用该值时，它将检查是否可以安全地转换为所需的类型。对于简单的旗帜，就没那么有趣了。

所以我们可以走了。运行它，然后...`SEGFAULT`。

```
#0  __strcmp_ssse3 () at ../sysdeps/x86_64/multiarch/../strcmp.S:173
#1  0x00007fffeff1a667 in llvm::cl::generic_parser_base::findOption(char const*) () from /home/src/leaf/sdl/dist/libLLVM-3.8.so
#2  0x00007ffff02f291f in llvm::RegisterPassParser<llvm::MachineSchedRegistry>::NotifyAdd(char const*, void* (*)(), char const*) () from /home/src/leaf/sdl/dist/libLLVM-3.8.so
#3  0x00007fffe3ab4754 in llvm::MachinePassRegistry::Add(llvm::MachinePassRegistryNode*) () from /usr/lib/x86_64-linux-gnu/libLLVM-5.0.so.1
#4  0x00007fffe36fe616 in ?? () from /usr/lib/x86_64-linux-gnu/libLLVM-5.0.so.1
#5  0x00007ffff7de76ba in call_init (l=<optimised out>, argc=argc@entry=4, argv=argv@entry=0x7fffffffdac8, env=env@entry=0x7fffffffdaf0) at dl-init.c:72
#6  0x00007ffff7de77cb in call_init (env=0x7fffffffdaf0, argv=0x7fffffffdac8, argc=4, l=<optimised out>) at dl-init.c:30
#7  _dl_init (main_map=main_map@entry=0x86b1b0, argc=4, argv=0x7fffffffdac8, env=0x7fffffffdaf0) at dl-init.c:120
#8  0x00007ffff7dec8e2 in dl_open_worker (a=a@entry=0x7fffffffc470) at dl-open.c:575
#9  0x00007ffff7de7564 in _dl_catch_error (objname=objname@entry=0x7fffffffc460, errstring=errstring@entry=0x7fffffffc468, mallocedp=mallocedp@entry=0x7fffffffc45f, 
    operate=operate@entry=0x7ffff7dec4d0 <dl_open_worker>, args=args@entry=0x7fffffffc470) at dl-error.c:187
#10 0x00007ffff7debda9 in _dl_open (file=0x7fffffffc6f0 "/usr/lib/x86_64-linux-gnu/dri/r600_dri.so", mode=-2147483390, caller_dlopen=0x7fffe9580a39, nsid=-2, argc=<optimised out>, 
    argv=<optimised out>, env=0x7fffffffdaf0) at dl-open.c:660
#11 0x00007ffff53c4f09 in dlopen_doit (a=a@entry=0x7fffffffc6a0) at dlopen.c:66
#12 0x00007ffff7de7564 in _dl_catch_error (objname=0x69d830, errstring=0x69d838, mallocedp=0x69d828, operate=0x7ffff53c4eb0 <dlopen_doit>, args=0x7fffffffc6a0) at dl-error.c:187
#13 0x00007ffff53c5571 in _dlerror_run (operate=operate@entry=0x7ffff53c4eb0 <dlopen_doit>, args=args@entry=0x7fffffffc6a0) at dlerror.c:163
#14 0x00007ffff53c4fa1 in __dlopen (file=<optimised out>, mode=<optimised out>) at dlopen.c:87
#15 0x00007fffe9580a39 in ?? () from /usr/lib/x86_64-linux-gnu/mesa/libGL.so.1
#16 0x00007fffe95838f3 in ?? () from /usr/lib/x86_64-linux-gnu/mesa/libGL.so.1
#17 0x00007fffe955ba54 in ?? () from /usr/lib/x86_64-linux-gnu/mesa/libGL.so.1
#18 0x00007fffe9557c2b in ?? () from /usr/lib/x86_64-linux-gnu/mesa/libGL.so.1
#19 0x00007fffe9558062 in glXQueryExtensionsString () from /usr/lib/x86_64-linux-gnu/mesa/libGL.so.1
#20 0x00007fffef080caf in ?? () from /usr/lib/x86_64-linux-gnu/libSDL2.so
#21 0x00007fffef07337f in ?? () from /usr/lib/x86_64-linux-gnu/libSDL2.so
#22 0x00007fffef07528c in ?? () from /usr/lib/x86_64-linux-gnu/libSDL2.so
#23 0x00007fffef074f35 in ?? () from /usr/lib/x86_64-linux-gnu/libSDL2.so
#24 0x00007fffeefdc397 in ?? () from /usr/lib/x86_64-linux-gnu/libSDL2.so
#25 0x00007ffff7ff55b5 in _init_module_main_5 ()
#26 0x00007ffff7ff516f in _entry ()
#27 0x00007ffff7ff65dc in main ()
#28 0x00007ffff0d7f9bd in llvm::MCJIT::runFunction(llvm::Function*, llvm::ArrayRef<llvm::GenericValue>) () from /home/src/leaf/sdl/dist/libLLVM-3.8.so
#29 0x00007ffff4c843d9 in ir_llvm::gen::execute (this=this@entry=0x7fffffffcd90, m=..., sc="") at src/ir/llvm/gen.cpp:249
#30 0x00007ffff6bd9e90 in runner::execute (this=0x7fffffffd8b0, m=std::shared_ptr (count 3, weak 0) 0x6c9d40, args=std::vector of length 0, capacity 0) at src/runner/runner.cpp:339
#31 0x00007ffff6bda264 in runner::execute (this=this@entry=0x7fffffffd8b0, m=std::shared_ptr (count 3, weak 0) 0x6c9d40) at src/runner/runner.cpp:264
#32 0x0000000000413687 in main (argc=4, argv=0x7fffffffdac8) at src/bin/leaf.cpp:256 
```

Enter fullscreen mode Exit fullscreen mode

啊哦！那是在 LLVM 执行引擎内部。注意，失败的并不是 SDL 的加载，而是一个可传递的库，通过`libGL.so.1`，尽管`dri/r600_dri.so`。失败的也不是库，而是 LLVM 的运行时工具。

我在邮件列表上询问过，但是到目前为止还没有解决这个问题的方法。也许 LLVM 升级会有所帮助。不然我就要自己调试了。

幸运的是，Leaf 不必那样跑，这是为了更快周转的方便。我们可以用传统的方式来编译程序。

```
leaf  --library SDL2 --exe /tmp/demo . 
```

Enter fullscreen mode Exit fullscreen mode

当然，这失败了:没有什么是允许简单的！原来我只为 LLVM JIT 引擎实现了`--library`。对链接器命令行的一个小小的补充就完成了。

屏幕上显示运行`/tmp/demo`和`0`。`0`是来自`SDL_Init`的成功值。太好了！

## SDL _ 创建 _ 窗口

我选择了 SDL 而不是 GL 库，因为我不想创建一个本地窗口。这并不难，但它是特定于平台的，通常涉及许多定义、结构和函数。

```
typedef SDL_Window : abi_pack [
] 

literal SDL_WINDOW_SHOWN = 0x0000_0004

@import( "SDL_CreateWindow" ) multi sdl_create_window : ( title : raw_array｢abi_char｣,
    x : abi_int, y : abi_int, w : abi_int, h : abi_int, flags : binary 32bit ) -> ( : SDL_Window value_ptr ) raw no_throw 
```

Enter fullscreen mode Exit fullscreen mode

`SDL_Window`是库中的一个不透明结构(或者它不是，但是我不需要它的任何部分)。出于安全原因，我还是声明了一个类型。return 上的`value_ptr`表示一个 C 风格的指针:这些在 Leaf 中被认为是“不安全的”引用，但是对于调用库是必要的。

> Leaf 确实在许多需要安全的情况下使用了`value_ptr`。我目前正在研究 Rust 是如何借用的——生命周期跟踪将成为 Leaf 的一部分。我将能够检测到`value_ptr`的使用何时无效。尽管如此，这些外来功能仍将是不安全的。

```
var window = sdl_create_window( std.u8_encode("Leaf SDL Window"),  0, 0, 400, 300, SDL_WINDOW_SHOWN )
//TODO: null check on `window` 
```

Enter fullscreen mode Exit fullscreen mode

Leaf 中的字符串目前只是一个`array｢char｣`，其中`char`是一个 Unicode 码位(一个完整的 32 位值)。调用 ABI API 需要将其转换为 ABI 形式。这里我只是假设那是`utf8`。最终，标准库将需要一个`abi_encode`函数来正确完成它(尽管 utf-8 是最常见的)。

> 为什么我放了一个关于 null 的`TODO`而不是只检查它？现在在 Leaf 中有一个限制，你不能直接检查指针。那个`window`是一个`SDL_Window`；那是它的固有类型。Leaf 主要处理内部类型，但是这里我需要一种方法来检查外部部分`value_ptr`。

运行这段代码会给我们一个窗口。

[![empty window graphic](img/3c56007e570c1f40eeadf38dd750a7dd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t4JDMpVn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2018/02/sdl_window_empty.png)

可以看到标题，但是内容好像不对。它看起来是透明的，只是在启动时显示它后面的内容。我们没有画任何东西，甚至没有清除它。这是一个未初始化的区域(尽管捕捉已经存在的内容看起来可能是一个安全问题)。

## SDL _ 获取 _ 窗口 _ 表面

接下来是`SDL_Get_Window_Surface`、`SDL_Fill_Rect`和`SDL_Update_Window_Surface`。这些都只是之前的变化。我能让窗户变成灰色。但是我不想要灰色，我想要一种 RGB 颜色。

为此我们使用了`SDL_Map_RGB`函数，从 RGB 空间映射到表面使用的任何颜色空间。它有这样的定义:

```
typedef SDL_PixelFormat : abi_pack [
]
@import( "SDL_MapRGB" ) multi sdl_map_rgb : ( format : SDL_PixelFormat value_ptr, r : octet, b : octet, c : octet ) -> ( : binary 32bit ) raw no_throw 
```

Enter fullscreen mode Exit fullscreen mode

嗯，它需要一个`SDL_PixelFormat`。在 SDL 的例子中，这来自表面。

```
@import( "SDL_GetWindowSurface" ) multi sdl_get_window_surface : ( window : SDL_Window value_ptr ) -> ( : SDL_Surface value_ptr ) raw no_throw 
```

Enter fullscreen mode Exit fullscreen mode

在`SDL_Surface`里面是一个名为`format`的属性。为了让`map_rgb`工作，我需要深入了解这个结构——不再像`SDL_Window`那样晦涩难懂。

```
typedef SDL_Surface : abi_pack [
    _flags : binary 32bit,
    format : SDL_PixelFormat value_ptr,
    w : abi_int,
    h : abi_int,
    pixels : abi_ptr,
    userdata : abi_ptr,
    _locked : abi_int,
    _lock_data : abi_ptr,
    //more
] 
```

Enter fullscreen mode Exit fullscreen mode

在 Leaf 中使用元组来表示任何纯数据类型(没有函数的类型，比如类)。注意`abi_pack`的使用，它表示这里字段的排序和对齐必须符合操作系统 ABI。Leaf 可以对字段进行重新排序，并具有不同的对齐方式，因此像原生库一样安排这种结构非常重要。*“*”前缀字段是那些被 docs_ 标记为内部的字段。

在进入 RGB 映射之前，我可以使用大小字段来检查这是否有效:

```
var surface = sdl_get_window_surface( window )
std.println(["surface ", surface.w, "x", surface.h]) 
```

Enter fullscreen mode Exit fullscreen mode

一开始并不是。我忘记了`_flags`字段，正在获取随机值。一旦修复，我可以继续创建一个彩色矩形。

```
ignore sdl_fill_rect( surface, 0, sdl_map_rgb(surface.format, 0, 100, 0) )
ignore sdl_update_window_surface( window ) 
```

Enter fullscreen mode Exit fullscreen mode

> `ignore`是必需的，因为我忽略了这些函数的返回值。默认情况下，Leaf 不允许忽略值。这里的形式也不好:我应该检查这些返回值是否有错误。

[![a green window](img/d96f3d1ff73c236648b998922f806457.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3Xg4clAl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mortoray.files.wordpress.com/2018/02/sdl_window_green.png)

我们看到了:一扇绿色的窗户！

> 来看看[的](http://leaflang.org) [SDL 分店](https://code.launchpad.net/~eda-qa/leaflang/sdl)和的叶子吧。演示代码是 github 上的[，虽然我不能保证当前状态是什么🙈。一定要跟着我一起跳](https://github.com/mortoray/leaf-algorithms/tree/master/sdl) [Twitch](https://www.twitch.tv/mortoray) 。