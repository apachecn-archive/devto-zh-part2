# 在 travis 中测试 LLVM Go 绑定

> 原文：<https://dev.to/dannypsnl/test-llvm-go-binding-in-travis-1292>

基本上，问题是因为我使用 LLVM 的 Go 绑定，然后很难使用预装的 LLVM。

这篇文章是关于我如何一步一步解决问题的。

首先，我们需要通过 apt-get 安装依赖项(如果你没有指定任何东西，travis 使用 Ubuntu 14.04)

```
dist: trusty
addons:
  apt:
    sources:
      - ubuntu-toolchain-r-test
      - llvm-toolchain-trusty
    packages:
      - g++-4.9
      - clang-8
      - clang-tools-8
      - libclang-common-8-dev
      - libclang-8-dev
      - libclang1-8
      - libllvm8
      - llvm-8
      - llvm-8-dev
      - llvm-8-runtime
      - libfuzzer-8-dev
      - lldb-8
      - lld-8 
```

现在我们得到了特拉维斯的 11 月 8 日。

然后我们需要设置环境变量:

```
env:
  - CC=clang-8 CGO_CXXFLAGS=-std=c++11 CGO_LDFLAGS_ALLOW='Wl,(search_paths_first|headerpad_max_install_names)' CGO_CPPFLAGS="`llvm-config-8 --cppflags`" CGO_LDFLAGS="`llvm-config-8 --ldflags --libs --system-libs all`" 
```

`llvm-config`是 LLVM 的一个助手工具，它会为你的程序生成元数据，例如`llvm-config --cppflags`包含 LLVM 的已安装头文件路径的包含路径。

这里的后缀`-8`是因为它是这样安装的。

但是如果你现在编译你的 Go 绑定，你会得到一些`run_build_sh`没有定义的东西。

这是因为 at `llvm.org/llvm/bindings/go/llvm/llvm_dep.go`包含类型检查绑定，以确保生成类型`run_build_sh`。它只能通过运行`llvm.org/llvm/bindings/go/build.sh`生成

但是如果我们深究构建流，我们会发现`llvm.org/llvm/tools/llvm-go/llvm-go.go`生成了这个东西！它的工作原理是:

```
go build $GOPATH/src/llvm.org/llvm/tools/llvm-go/llvm-go.go
./llvm-go print-config $GOPATH/src/llvm.org/llvm/bindings/go/llvm/llvm_config.go 
```

现在我们基本上可以使用最新版本的 LLVM 了，但是如果你在你的机器上安装了一些旧的 LLVM，它就不能编译了！

因为 Go binding 需要一些变量，但是 C 没有(旧版本)。

解决方法是去[http://releases.llvm.org](http://releases.llvm.org)下载规范版本(应该和你系统中安装的一样)来获得绑定源码。

以 LLVM 6.0 为例:

```
mkdir -p $GOPATH/src/llvm.org && cd $GOPATH/src/llvm.org
curl -XGET http://releases.llvm.org/6.0.0/llvm-6.0.0.src.tar.xz >> llvm.tar.xz
tar -xf llvm.tar.xz
mv llvm-6.0.0.src llvm 
```

现在，您可以通过预装的 LLVM 来测试您的代码！