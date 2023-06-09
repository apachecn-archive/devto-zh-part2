# TIL:建立一个 Rust 开发环境

> 原文：<https://dev.to/jonasbn/til-setting-up-a-rust-development-environment-2f95>

刚开始评估学习**锈**。我有一些*的问题*，其中 **Rust** 可能是一个很好的解决方案，但是为了到达那里，我必须为 **Rust** 设置一个开发环境。

本指南是为 macOS 编写的，旨在使用 T2 的 Visual Studio 代码。

通过【https://rustup.rs/】T2 开始安装基本**防锈**工具链。

️️⚠️因为你永远不应该从一个未经验证的 URL 直接在 shell 中执行任何东西，所以帮你自己一个忙，检查 URL 背后的源代码。

```
$ curl -vs https://sh.rustup.rs 2>&1 | less 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经解决了这个问题，让我们安装 **Rust** 。

```
$ curl https://sh.rustup.rs -sSf | sh 
```

Enter fullscreen mode Exit fullscreen mode

请检查[https://rustup.rs/](https://rustup.rs/)的替代安装方式。

该 URL 实际上安装了一个名为`rustup`的工具，这是处理您的 **Rust** 安装、`rustup` installs(以及其他)的推荐方式:

*   `rustc`、**锈**编译器
*   `cargo`、**锈**包经理

现在我们已经安装了 Rust T1，我们可以开始开发我们的第一个应用程序了。

用`cargo`启动一个**防锈**程序。`cargo`是 **Rust 的**包管理器，你用它做*一切*。

```
$ cargo new -bin «application» 
```

Enter fullscreen mode Exit fullscreen mode

示例:

```
$ cargo new —bin helloworld
     Created library `helloworld` project 
```

Enter fullscreen mode Exit fullscreen mode

创建库时的变化:

```
$ cargo new —lib helloworldlibrary
     Created library `helloworldlibrary` project 
```

Enter fullscreen mode Exit fullscreen mode

这会生成以下文件:

*   `Cargo.toml`，我们的应用程序/库的元数据
*   `Cargo.lock`，关于我们的应用程序/库的依赖性的元数据
*   `src/`，我们的源目录

并且我们准备编译，可以用`cargo`
来完成

```
$ cargo build
    Finished dev [unoptimized + debuginfo] target(s) in 0.10s 
```

Enter fullscreen mode Exit fullscreen mode

这将生成名为`target/`的 are 目录，内容如下:

```
$ tree target/
target/
└── debug
    ├── build
    ├── deps
    │   ├── helloworld-e1702fb45f5d0052
    │   ├── helloworld-e1702fb45f5d0052.d
    │   └── helloworld-e1702fb45f5d0052.dSYM
    │       └── Contents
    │           ├── Info.plist
    │           └── Resources
    │               └── DWARF
    │                   └── helloworld-e1702fb45f5d0052
    ├── examples
    ├── helloworld
    ├── helloworld.d
    ├── helloworld.dSYM -> deps/helloworld-e1702fb45f5d0052.dSYM
    ├── incremental
    │   └── helloworld-15v45zkxrmyrm
    │       ├── s-f52pub8bwa-1t86tbf-23d93ip4735d7
    │       │   ├── 1y16o1qfye96o7m0.o
    │       │   ├── 37u9wm6hxf6h6j0m.o
    │       │   ├── 3rngp6bm2u2q5z0y.o
    │       │   ├── 4oc10dk278mpk1vy.o
    │       │   ├── 4xq48u46a1pwiqn7.o
    │       │   ├── dep-graph.bin
    │       │   ├── oa3rad818d8sgn4.o
    │       │   ├── query-cache.bin
    │       │   └── work-products.bin
    │       └── s-f52pub8bwa-1t86tbf.lock
    └── native 
```

Enter fullscreen mode Exit fullscreen mode

该版本没有按照编译器的指示进行优化，旨在用于开发和调试。

*目标*可以运行为:

```
$ ./target/debug/helloworld
Hello, world! 
```

Enter fullscreen mode Exit fullscreen mode

如果你想为发布而构建，你需要添加额外的标志:`--release`

```
$ cargo build —release
    Finished release [optimized] target(s) in 0.10s 
```

Enter fullscreen mode Exit fullscreen mode

这将更多文件添加到`target/`目录:

```
$ tree target/
target/
├── debug
│   ├── build
│   ├── deps
│   │   ├── helloworld-e1702fb45f5d0052
│   │   ├── helloworld-e1702fb45f5d0052.d
│   │   └── helloworld-e1702fb45f5d0052.dSYM
│   │       └── Contents
│   │           ├── Info.plist
│   │           └── Resources
│   │               └── DWARF
│   │                   └── helloworld-e1702fb45f5d0052
│   ├── examples
│   ├── helloworld
│   ├── helloworld.d
│   ├── helloworld.dSYM -> deps/helloworld-e1702fb45f5d0052.dSYM
│   ├── incremental
│   │   └── helloworld-15v45zkxrmyrm
│   │       ├── s-f52pub8bwa-1t86tbf-23d93ip4735d7
│   │       │   ├── 1y16o1qfye96o7m0.o
│   │       │   ├── 37u9wm6hxf6h6j0m.o
│   │       │   ├── 3rngp6bm2u2q5z0y.o
│   │       │   ├── 4oc10dk278mpk1vy.o
│   │       │   ├── 4xq48u46a1pwiqn7.o
│   │       │   ├── dep-graph.bin
│   │       │   ├── oa3rad818d8sgn4.o
│   │       │   ├── query-cache.bin
│   │       │   └── work-products.bin
│   │       └── s-f52pub8bwa-1t86tbf.lock
│   └── native
└── release
    ├── build
    ├── deps
    │   ├── helloworld-599221bdb1c77978
    │   └── helloworld-599221bdb1c77978.d
    ├── examples
    ├── helloworld
    ├── helloworld.d
    ├── incremental
    └── native 
```

Enter fullscreen mode Exit fullscreen mode

上面的树显示了我们的两个*目标*，您可以使用`cargo`命令清理`target/`目录。

```
$ cargo clean 
```

Enter fullscreen mode Exit fullscreen mode

您可以运行目标可执行文件，如下所示:

```
$ ./target/release/helloworld
Hello, world! 
```

Enter fullscreen mode Exit fullscreen mode

或者您可以使用`cargo`来运行可执行文件，我可以在前面提到这一点，因为这同样适用于常规构建，但是我想一步一步来。

```
$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.03s
     Running `target/debug/helloworld`
Hello, world! 
```

Enter fullscreen mode Exit fullscreen mode

不要以为默认是开发版本，它是运行的。如果您想运行发布版本，请执行:

```
$ cargo run --release
    Finished release [optimized] target(s) in 0.03s
     Running `target/release/helloworld`
Hello, world! 
```

Enter fullscreen mode Exit fullscreen mode

如果更改已保存到`src/`文件，则`cargo run`在执行前自动构建。

如果使用 **Visual Studio 代码**和 [RLS 扩展](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust)为 **Rust** 。您的`target/`目录填充了更多的数据，请参见`rls/`目录。请注意，这是从 **Visual Studio 代码**初始化`cargo clean`后的`target/`目录。该设置用于通过 **RLS** (Rust 语言服务器)与 **Visual Studio 代码**集成。

```
$ tree target
target
└── rls
    └── debug
        ├── build
        ├── deps
        │   ├── helloworld-da127327fd97b6ec.d
        │   ├── helloworld-fe97a0dcc4f47622.d
        │   ├── libhelloworld-da127327fd97b6ec.rmeta
        │   └── libhelloworld-fe97a0dcc4f47622.rmeta
        ├── examples
        ├── incremental
        │   ├── helloworld-3cgahs8hwxsms
        │   │   ├── s-f5woledziu-7esk1o-3jq9t7fc4fb3p
        │   │   │   ├── dep-graph.bin
        │   │   │   ├── query-cache.bin
        │   │   │   └── work-products.bin
        │   │   └── s-f5woledziu-7esk1o.lock
        │   └── helloworld-uq5esv9yxxqk
        │       ├── s-f5wolec88s-fleft9-1dg0efqmqtyu2
        │       │   ├── dep-graph.bin
        │       │   ├── query-cache.bin
        │       │   └── work-products.bin
        │       └── s-f5wolec88s-fleft9.lock
        ├── libhelloworld-da127327fd97b6ec.d
        ├── libhelloworld-da127327fd97b6ec.rmeta
        ├── libhelloworld.d
        ├── libhelloworld.rmeta
        └── native 
```

Enter fullscreen mode Exit fullscreen mode

**RLS** 是为了支持其他*前端*(编辑器)，所以 **RLS** 并不是针对 **Visual Studio 代码**。如果您想了解更多信息，请参见 [RLS 知识库](https://github.com/rust-lang-nursery/rls)。

现在你已经准备好尝试用 **Rust** 或者用一些 **Rust** 来解决你当前的问题。

我现在很难消化掉所有关于 **Rust** 的指南、教程、文章和博客，只要看看 [Rust 学习资源](https://github.com/ctjhoa/rust-learning)就知道了。

检查[主锈点](https://www.rust-lang.org/en-US/)的[文档部分](https://www.rust-lang.org/en-US/documentation.html)。

💡最后，我给你最后一个提示——当你找到你想在 [crates.io](https://crates.io/) 上试用的库时，它们通常有一个文档链接，可以引导你找到例子等。

这本指南和一些视频教程是我第一个“ [Hacktoberfest 童话](https://dev.to/jonasbn/blog-post-a-little-hacktoberfest-fairytale-3k96)的开始，所以你还在等什么。

这个胡麻是从我的[胡麻收藏](http://jonasbn.github.io/til/)里拿的。

## 资源

*   [rustup.rs -防锈工具链安装程序](https://rustup.rs/)
*   [Rust 编程语言](https://www.rust-lang.org/en-US/)
*   [维基百科](https://en.wikipedia.org/wiki/Rust_(programming_language))
*   [铁锈操场](https://play.rust-lang.org/)
*   [crates . io:Rust 的软件包库](https://crates.io/)
*   [RLS](https://github.com/rust-lang-nursery/rls)