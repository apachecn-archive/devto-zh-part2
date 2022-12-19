# 探测机箱的 Deps:缓存 Rust Docker 构建

> 原文：<https://dev.to/mgattozzi/plumbing-the-deps-of-the-crate-caching-rust-docker-builds-2e48>

[![Plumbing the Deps of the Crate: Caching Rust Docker Builds](img/3471fdc956e8c75dc482d7d354f0e3e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uW4xrBMA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mgattozzi.com/conteimg/2018/10/514570034-612x612.jpg)

我最近一直在用 Docker 做 Rust 代码，但是其中一个问题是使用 Docker 文件像下面这样:

```
COPY . /opt/my_build_dir
RUN cargo build 
```

最终导致容器下载依赖性板条箱。每一个。单身。时间。太可怕了。如果你曾经为一个 Rust 项目进行过 15 分钟的干净构建，你会发现每次只是为了测试一些变化而从头开始重建是多么糟糕。

我们该如何应对？有什么解决办法？让我们看一下 Docker 如何使用`COPY`指令和文件系统缓存，然后转到我用来破解不能只构建一个机箱的依赖项的限制的解决方案。

## 副本、文件系统和你

Docker 做的事情之一就是在不同的层缓存容器。命令`FROM`、`COPY`、`RUN`和`CMD`各创建一个不同的层。这很好！这意味着如果我的 Docker 文件中直到第 20 行的所有内容都是相同的，Docker 可以跳过这些构建步骤。`COPY`如何知道它是“无效的”并且命令需要再次改变？它检查在`COPY`命令中引用的文件是否已经改变。这很好！如果我更改了一个文件，那么我希望这个新文件在容器中，这样它就可以正确地构建了。

现在看看你是否能发现我上面分享的 Dockerfile 文件的第一个片段的问题。你觉得你明白了吗？如果你不确定，那么想想这个:如果我使用`COPY .`，我是说，“如果这个目录中的任何东西改变了，就把数据复制进来”。你改了源文件？因为必须重新运行命令，所以会制作目录的全新副本。这也是一个新的操作系统，所以即使你复制到一个`target`目录，Rust 仍然会重建它，因为你的`target`目录中的缓存文件将不再有效编译。

这就是为什么我们一遍又一遍地下载所有的变化。我们还没有创建一个只构建 dep 并缓存它的层。我们真正想要的是只在`Cargo.toml` / `Cargo.lock`改变时重建那些 dep！我们怎么做呢？

## 她说，我知道建立 deps 是什么感觉

要明确的是，我们要做的绝对是一个黑客。不好看。绝对不优雅，但很管用。我们将使用`Cargo.toml`文件的一个小特性，一点`sed`的魔力，以及一些对`COPY`的巧妙使用来完成它。好的，我们开始吧。首先，您需要打开目录中的 docker 文件。我假设容器中已经有了`cargo`，并且正在做类似于
的事情

```
# The beginning stuff up to this point
COPY . /your_work_dir
RUN cargo build
# The rest of your file 
```

我们要做的第一件事是将我们的文件改为:

```
# We'll get to what this file is below!
COPY dummy.rs /your_work_dir
# If this changed likely the Cargo.toml changed so lets trigger the
# recopying of it anyways
COPY Cargo.lock /your_work_dirCOPY Cargo.toml /your_work_dir
# We'll get to what this substitution is for but replace main.rs with
# lib.rs if this is a library
RUN sed -i 's/src/main.rs/dummy.rs/' Cargo.toml
# Drop release if you want debug builds. This step cache's our deps!
RUN cargo build --release
# Now return the file back to normal
RUN sed -i 's/dummy.rs/src/main.rs/' Cargo.toml
# Copy the rest of the files into the container
COPY . /your_work_dir
# Now this only builds our changes to things like src
RUN cargo build --release 
```

现在在目录的顶层创建一个文件`dummy.rs`，它应该是这样的:

```
fn main() {} 
```

就是这样！这是一个存根，让货物来构建 deps，而不是构建我们的实际代码！如果你正在构建一个库，那么使用一个空文件。好了，最后一点小魔术是修改你的`Cargo.toml`，把它包含在`[package]`部分下:

```
[[bin]]
name = "your_project_name"
path = "src/main.rs" 
```

如果你正在创建一个库，那么把:

```
[lib]
name = "your_project_name"
path = "src/lib.rs" 
```

还记得那些`sed`命令吗？他们实际上会改变有路径的线。它将把它改为指向虚拟文件，编译它(它基本上是空的，所以不需要太多时间)，并且所有的 dep 都列在`Cargo.toml`文件中。然后它变回实际的入口点来构建正确的项目！

就像我说的，这是一个绝对的黑客，但嘿，它工作得很好！尝试一下，看看你能提高多少速度。

## 关闭思绪

理想情况下，`cargo`应该足够聪明，只做`cargo build --dependencies-only`(有一个[问题](https://github.com/rust-lang/cargo/issues/2644)为此打开)，然后让 Docker 设置只重建对`src`目录的更改。不过，就目前而言，上述观点虽然脆弱，却是这个问题的一个足够好的替代方案。如果能看到 Rust 出色的工具以满足生产需求的方式工作，那就太好了。我希望你现在花更多的时间在代码和部署配置上，而不是担心你的构建时间！