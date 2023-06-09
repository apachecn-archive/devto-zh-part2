# 快速提示- F#代码组织

> 原文：<https://dev.to/kspeakman/quick-tip----f-code-organization-4hoh>

当我开始用 F#编程时，我会遇到一个难题，即在哪里放置类型以及这些类型上的函数。今天，我突然想到，我已经习惯了这种方式，我想我应该分享一下。

目录结构如下所示:

*   MyLibrary(项目甚至文件夹)
    *   Types.fs
    *   Widget.fs
    *   Gismo.fs
    *   ...

在函数式编程中，类型是共享的东西。它们是不同函数之间的连接点——包括您自己的函数和使用您的库的代码。下面是类型文件的模式。注意模块上的`[<AutoOpen>]`属性。

```
// Types.fs
namespace MyLibrary

[<AutoOpen>]
module Types =

    // type defs go here, no functions

    type Widget =
        {
            ...
        }

    type Gismo =
        {
            ...
        }

    ... 
```

Enter fullscreen mode Exit fullscreen mode

这里是一个包含`Widget`类型函数的小部件模块。

```
namespace MyLibrary

module Widget =

    // the Widget type is automatically available in namespace
    let create ... : Widget =
        {
            ...
        }

    // so is the Gismo type
    let toGismo widget : Gismo =
        ...

    let fromGismo gismo : Widget =
        ...

    ... 
```

Enter fullscreen mode Exit fullscreen mode

下面是我使用这个库时发生的情况。

```
open MyLibrary

// Types.fs is automatically opened, exposing all types in it
// I can reference the Widget type without namespace
let returnsWidget ... : Widget =
    ...

// The Widget module is also exposed under the same name
let myFn ... =
    Widget.create ...
        |> ...
        |> Widget.toGismo 
```

Enter fullscreen mode Exit fullscreen mode

我们已经定义了名称空间、类型和模块，只需打开库，小部件类型和它上面的所有功能就会很好地打包在一起，命名为`Widget`。如果您试图在同一个名称空间或模块中创建小部件类型和小部件模块，您会得到一个编译器错误，因为它们具有相同的名称。*更新:前面这句话在 F#新版本中似乎不再成立。*

您可以通过在小部件类型上添加静态扩展方法来实现类似的东西，但是上面的方法更符合习惯。使用类型扩展，您也可能会遇到循环引用的问题。