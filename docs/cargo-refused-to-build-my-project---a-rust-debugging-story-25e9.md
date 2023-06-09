# Cargo 拒绝构建我的项目——一个 Rust 调试的故事！

> 原文：<https://dev.to/rrampage/cargo-refused-to-build-my-project---a-rust-debugging-story-25e9>

### 背景

今天，我重新开始了我的一个`rust`项目。为了检查我最后的工作代码，我运行了`cargo run`。但是它拒绝运行，并显示错误信息:

```
...."-Wl,-Bdynamic" "-l" "dl" "-l" "rt" "-l" "pthread" "-l" "pthread" "-l" "gcc_s" "-l" "c" "-l" "m" "-l" "rt" "-l" "pthread" "-l" "util" "-l" "util"
= note: /usr/bin/ld: cannot find Scrt1.o: No such file or directory
          collect2: error: ld returned 1 exit status 
```

Enter fullscreen mode Exit fullscreen mode

### 链接器问题！

`ld`用于链接库。所以，错误不在代码中，而在链接阶段。我谷歌了这个错误，解决方案是安装`build-essential`包。但是这个包已经安装在我的机器上了(它是我在任何开发机器上安装的第一个包之一)。

更多的谷歌搜索显示`cargo`使用系统`cc`链接器链接到 C 运行时。跑`which cc`给了我`$HOME/anaconda3/bin/cc`。这个`cc`是我的 Anaconda 根环境的一部分。(Anaconda 是科学计算包的包管理器。这是在不同环境中安装多个版本的软件包的一种便捷方式)。

在 Linux 上，链接器知道在哪里使用共享库缓存找到所需的库。我运行`ldconfig -v`来刷新它，然后再试一次。同样的错误！

使用`LD_LIBRARY_PATH`环境变量可以明确列出要包含的目录。我尝试设置`LD_LIBRARY_PATH`指向所需的目录，然后以`LD_LIBRARY_PATH=DIR cargo build -v`的身份运行 cargo。但是它给出了同样的错误。

我认为`cargo`一定是在某个地方设置了链接器值，所以让我尝试用`rustc`直接编译。即使这样也会产生同样的错误。这样，我排除了某些环境变量只影响`cargo`的可能性。

### 更谷歌！

进一步搜索添加了“rust”的错误，我看到了人们在交叉编译时遇到困难的结果。由此，我了解到 cargo 有不同的目标，即不同的指令集(例如 x86-64，arm，x86，mips)，不同的操作系统(例如 linux，windows，freebsd)和不同的 C 运行时(例如 glibc，musl，msvc)。货物上的铁锈文件提到这叫做目标三联体。 [**Cargo book**](https://doc.rust-lang.org/cargo/reference/) 提到你可以使用 RUSTFLAGS 环境变量直接 Cargo 显式地使用一个特定的链接器。

因为我只为我的机器构建，所以我必须找出目标的确切值。Rust 通过运行`rustc --print target-list`给出了所有受支持目标的详尽列表。我的目标是`x86_64-unknown-linux-gnu`。

将链接器显式地作为`RUSTFLAGS="-C linker=x86_64-unknown-linux-gnu" cargo build -v`传递给`cargo`是可能的。成功了！

### 空前修复！

我不想每次运行`cargo`时都这样。Cargo book 说 Cargo 在`$HOME/.cargo/config`中使用了一个全局配置文件。

我在文件中添加了以下内容:

```
# Specify which linker to use for this target
[target.x86_64-unknown-linux-gnu]
linker = "x86_64-linux-gnu-gcc" 
```

Enter fullscreen mode Exit fullscreen mode

现在，`cargo build`工作起来没有问题。