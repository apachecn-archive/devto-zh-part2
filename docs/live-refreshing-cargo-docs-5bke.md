# 实时刷新货物单据

> 原文：<https://dev.to/benrcongdon/live-refreshing-cargo-docs-5bke>

是一个很棒的工具，但是到目前为止，还没有一个官方的方法可以让它生成的文档在你编辑的时候刷新。使用`--open`参数运行`cargo doc`将在浏览器窗口中打开生成的文档。如果您对您的源代码进行了更改，您将需要重新运行`cargo doc`以使更改反映在您的浏览器中。通过将其他一些 Rust 工具链接在一起，我们可以很容易地获得实时重载文档的功能。

我们将成为两个工具:

*   [cargo-watch](https://github.com/passcod/cargo-watch) 观察我们项目的源代码何时改变，并触发文档重建。
*   [http](https://github.com/thecoshman/http) 作为生成文档的静态服务器。
    *   严格来说，*没有必要这样做:如前所述，您可以使用带有`cargo doc`的`--open`参数来查看使用`file://`协议的文档，但是我喜欢有一个`localhost`地址来访问。*

我假设您已经安装了`cargo`。如果你没有，那么你可以使用 [rustup](https://rustup.rs/) 来安装它。

### [T3 第一步:安装`cargo-watch`和`https`](#step-1-install-raw-cargowatch-endraw-and-raw-https-endraw-)

首先，安装我们需要的两个命令:

```
cargo install cargo-watch https 
```

Enter fullscreen mode Exit fullscreen mode

### 第二步:运行手表命令

现在，导航到包含您的`Cargo.toml`文件的项目目录。然后，运行以下命令:

```
cargo watch -s 'cargo doc && http target/doc' 
```

Enter fullscreen mode Exit fullscreen mode

您现在应该会看到如下所示的内容:

```
[Running cargo doc && http target/doc]
 Documenting docs-example v0.1.0 (file:///path/to/project/docs-example)
    Finished dev [unoptimized + debuginfo] target(s) in 0.43s
Hosting "target/doc" on port 8000 without TLS...
Ctrl-C to stop. 
```

Enter fullscreen mode Exit fullscreen mode

您可以在浏览器中访问`localhost:8000`来查看生成的文档。(注意:您最初会看到一个目录页面，因此您必须导航到其中一个包列表才能看到实际的文档)

如果您对项目中的任何源文件进行任何更改，您将会看到`cargo-watch`会自动重新运行`cargo doc`并重启文件服务器。您应该能够刷新浏览器并实时看到文档的变化。

## 奖励:自动刷新

这种方法的一个缺点是编辑源文件不会触发浏览器的刷新(也就是说，*您仍然需要手动刷新页面)。*

如果你喜欢使用基于节点的工具，[浏览器同步](https://browsersync.io/)可以解决这个问题。

安装方式:

```
npm install -g browser-sync 
```

Enter fullscreen mode Exit fullscreen mode

…然后编辑您的`cargo-watch`命令，使用`browser-sync`而不是`http` :

```
cargo watch -s 'cargo doc && browser-sync start --ss target/doc -s target/doc --directory --no-open' 
```

Enter fullscreen mode Exit fullscreen mode

现在，访问`localhost:3000`(或启动时列出的任何端口)。对 Rust 源代码的任何后续更改都会触发任何打开的浏览器自动刷新更新后的文档。

* * *

我发现使用这种自动刷新文档的方法可以让我更愉快地记录我的 Rust crates。我很想知道是否有更简单的方法来自动刷新文档——一定要告诉我！😄

## 更多信息

*   要学习`rustdoc`(为`cargo doc`提供能量)的基础知识，请查阅铁锈书[这一章](https://doc.rust-lang.org/rustdoc/what-is-rustdoc.html)。
*   这些问题给出了为什么该功能没有得到官方支持的背景(截至目前): [cargo#1472](https://github.com/rust-lang/cargo/issues/1472) ， [cargo#4966](https://github.com/rust-lang/cargo/issues/4966)