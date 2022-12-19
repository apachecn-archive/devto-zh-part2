# 生锈的 FFI 机箱和 CI

> 原文：<https://dev.to/jeikabu/rust-ffi-crate-and-ci-2n05>

对于项目的下一阶段，我需要 Rust 绑定到 [nng](https://github.com/nanomsg/nng) 。在这篇文章中，我将用绑定创建一个 Rust crate，并设置 Appveyor 进行 Windows 构建，Travis 进行 OSX/Linux 构建。

源代码: [Github/jeikabu/runng-sys](https://github.com/jeikabu/runng-sys) 。

## 锈 FFI

外来函数接口(FFI)是你可以在 Rust 和另一种编程语言之间调用[的方式。它类似于 Java 的](https://github.com/alexcrichton/rust-ffi-examples)[Java-Native-Interface(JNI)](https://en.wikipedia.org/wiki/Java_Native_Interface)。Net 的 [C++/CLI](https://en.wikipedia.org/wiki/C%2B%2B/CLI) 或 [PInvoke](https://docs.microsoft.com/en-us/cpp/dotnet/how-to-call-native-dlls-from-managed-code-using-pinvoke) ，以及其他许多编程语言中的其他机制。

[Bindgen](https://github.com/rust-lang-nursery/rust-bindgen) 可以自动生成 Rust FFI 绑定到 C(和一些 C++)库。幸运的是，nng 是 C. [这篇博文](http://fitzgeraldnick.com/2016/12/14/using-libbindgen-in-build-rs.html)被多次引用，看起来像是 [bindgen 教程](https://rust-lang-nursery.github.io/rust-bindgen)的出处。

但是首先我们需要一个供 rustc 链接的库。我们又一次幸运了，因为 nng 使用了 [cmake](https://cmake.org/) 和 [cmake-rs](https://github.com/alexcrichton/cmake-rs) 允许我们运行它免于生锈。

`cargo new --lib runng-sys`创建一个新项目，并在`./Cargo.toml` :

```
[build-dependencies]
cmake = "0.1"
bindgen = "0.40" 
```

Enter fullscreen mode Exit fullscreen mode

遵循 bindgen 教程，`./wrapper.h`包括 nng 头文件:

```
#include "nng/src/nng.h"  
// protocols
#include "nng/src/protocol/bus0/bus.h" #include "nng/src/protocol/pipeline0/pull.h" #include "nng/src/protocol/pipeline0/push.h" #include "nng/src/protocol/pubsub0/pub.h" #include "nng/src/protocol/pubsub0/sub.h" #include "nng/src/protocol/reqrep0/rep.h" #include "nng/src/protocol/reqrep0/req.h"  
// transports
#include "nng/src/transport/inproc/inproc.h" #include "nng/src/transport/ipc/ipc.h" #include "nng/src/transport/tcp/tcp.h" #include "nng/src/transport/ws/websocket.h" 
```

Enter fullscreen mode Exit fullscreen mode

`./build.rs`将在剩余的板条箱:
之前构建并执行

```
extern crate bindgen;
extern crate cmake;

use cmake::Config;
use std::{
    env,
    path::PathBuf,
};

fn main() {
    // Run cmake to build nng
    let dst = Config::new("nng")
        .generator("Ninja")
        .define("CMAKE_BUILD_TYPE", "Release")
        .define("NNG_TESTS", "OFF")
        .define("NNG_TOOLS", "OFF")
        .build();

    // Check output of `cargo build --verbose`, should see something like:
    // -L native=/path/runng/target/debug/build/runng-sys-abc1234/out
    // That contains output from cmake
    println!("cargo:rustc-link-search=native={}", dst.join("lib").display());
    // Tell rustc to use nng static library
    println!("cargo:rustc-link-lib=static=nng");

    let bindings = bindgen::Builder::default()
        .header("wrapper.h")
        // This is needed if use `#include <nng.h>` instead of `#include "path/nng.h"`
        //.clang_arg("-Inng/src/")
        .generate()
        .expect("Unable to generate bindings");
    let out_path = PathBuf::from(env::var("OUT_DIR").unwrap());
    bindings
        .write_to_file(out_path.join("bindings.rs"))
        .expect("Couldn't write bindings");
} 
```

Enter fullscreen mode Exit fullscreen mode

`src/lib.rs`包含:

```
// Suppress the flurry of warnings caused by using "C" naming conventions
#![allow(non_upper_case_globals)]
#![allow(non_camel_case_types)]
#![allow(non_snake_case)]

// This matches bindgen::Builder output
include!(concat!(env!("OUT_DIR"), "/bindings.rs")); 
```

Enter fullscreen mode Exit fullscreen mode

运行`cargo build`至:

1.  使用 cmake 和[忍者](https://ninja-build.org/)建造 nng。这类似于运行:

    ```
    cd nng && cmake -G Ninja -DCMAKE_BUILD_TYPE=Release . && ninja install 
    ```

2.  运行 bindgen 在`$OUT_DIR/bindings.rs`中生成本地库绑定

注意事项:

*   [cmake 文档](https://docs.rs/cmake/)和 [bindgen 文档](https://docs.rs/bindgen)。
*   Ninja 是推荐给 nng 的 cmake 生成器。
*   Cmake 和 Ninja 都必须在`PATH`环境变量中(或者设置`CMAKE`和`CMAKE_MAKE_PROGRAM`
*   使用`cargo build --verbose`进行调试
*   Cmake 生成静态库:`target/CONFIG/build/runng-sys-abc123/out/lib/libnng.a`(在 OSX 上，可能在 Linux 上)。
*   如果决定使用`#include <nng.h>`等。相反，需要通过`clang_arg()`提供包含路径(参见本 [SO](https://stackoverflow.com/questions/42741815/setting-the-include-path-with-bindgen) )。
*   `$OUT_DIR`由货物设定。

## 测试绑定

要编写一个简单的测试，需要看一下刚刚生成的是 VS 代码(在 OSX 上必须先[在路径](https://code.visualstudio.com/docs/setup/mac#_launching-from-the-command-line)中安装‘Code’命令):

```
code `find . -name bindings.rs` 
```

Enter fullscreen mode Exit fullscreen mode

这将是“缩小”的 Rust 源代码。为了便于阅读，**查看>命令面板> `format document`** 。

写一个简单的 nng 请求/回复客户端/服务器:

```
use std::ffi::CString;

#[cfg(test)]
mod tests {
    use super::*;
    #[test]
    fn it_works() {
        unsafe {
            let url = CString::new("inproc://test").unwrap();
            let url = url.as_bytes_with_nul().as_ptr() as *const i8;

            // Reply socket
            let mut rep_socket = nng_socket { id: 0 };
            assert_eq!(0, nng_rep0_open(&mut rep_socket));
            assert_eq!(0, nng_listen(rep_socket, url, std::ptr::null_mut(), 0));

            // Request socket
            let mut req_socket = nng_socket { id: 0 };
            assert_eq!(0, nng_req0_open(&mut req_socket));
            assert_eq!(0, nng_dial(req_socket, url, std::ptr::null_mut(), 0));

            // Send message
            let mut req_msg = nng_msg { _unused: [] };
            let mut req_msg = &mut req_msg as *mut nng_msg;
            assert_eq!(0, nng_msg_alloc(&mut req_msg, 0));
            // Add a value to the body of the message
            let val = 0x12345678;
            assert_eq!(0, nng_msg_append_u32(req_msg, val));
            assert_eq!(0, nng_sendmsg(req_socket, req_msg, 0));

            // Receive it
            let mut recv_msg = nng_msg { _unused: [] };
            let mut recv_msg = &mut recv_msg as *mut nng_msg;
            assert_eq!(0, nng_recvmsg(rep_socket, &mut recv_msg, 0));
            // Remove our value from the body of the received message
            let mut recv_val: u32 = 0;
            assert_eq!(0, nng_msg_trim_u32(recv_msg, &mut recv_val));
            assert_eq!(val, recv_val);
            // Can't do this because nng uses network order (big-endian)
            //assert_eq!(val, *(nng_msg_body(recv_msg) as *const u32));

            nng_close(req_socket);
            nng_close(rep_socket);
        }

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 [`CString`](https://doc.rust-lang.org/std/ffi/struct.CString.html) 创建以空字符结尾的字符串，如[所建议的。要在 Rust 中得到一个`const char*`到`"abc\0"`需要:](https://stackoverflow.com/questions/28649311/what-is-the-proper-way-to-go-from-a-string-to-a-const-i8) 

```
let cstring = std::ffi::CString::new("abc").unwrap().as_bytes_with_nul().as_ptr() as *const i8; 
```

Enter fullscreen mode Exit fullscreen mode

纠结于`Type**`在 c .中的用途例如， [`nng_msg_alloc()`](https://nanomsg.github.io/nng/man/v1.0.0/nng_msg_alloc.3.html) 绑定的是:

```
pub fn nng_msg_alloc(arg1: *mut *mut nng_msg, arg2: usize) -> ::std::os::raw::c_int; 
```

Enter fullscreen mode Exit fullscreen mode

获取`*mut *mut` :

```
// NO; can't convert &mut &mut to *mut *mut
let msg = &mut &mut recv_msg as *mut *mut nng_msg;
// Yes
let msg = &mut (&mut recv_msg as *mut nng_msg) as *mut *mut nng_msg;
// Yes
let msg = &mut (&mut recv_msg as *mut nng_msg); 
```

Enter fullscreen mode Exit fullscreen mode

最后一行是浮雕，`as *mut *mut type`是 y 字铸的。

已经在[的另一个项目](//./a-potpourri-of-net-core-cicd-lite-24ba#appveyor)上使用[供应商](https://www.appveyor.com/)，所以决定从那个项目开始。

以[这个`appveyor.yml`为锈](https://github.com/starkat99/appveyor-rust)为起点:

```
image: 
- Visual Studio 2017
- Ubuntu

# Build configurations
environment:
  matrix:
    # Stable 64-bit MSVC
    - target: x86_64-pc-windows-msvc
      channel: stable
    # Stable 32-bit MSVC
    - target: i686-pc-windows-msvc
      channel: stable
    # Dummy target so linux always has something to build
    - target: ubuntu-dummy
      channel: stable

matrix:
  exclude:
    # Linux ignore windows builds
    - image: Ubuntu
      target: x86_64-pc-windows-msvc
    - image: Ubuntu
      target: i686-pc-windows-msvc
    # Windows should ignore dummy linux configuration
    - image: Visual Studio 2017
      target: ubuntu-dummy

# Platform-specific configuration
for:
-
  matrix:
    only:
      - image: Visual Studio 2017
  install:
    # Download rustup and install rust
    - appveyor DownloadFile https://win.rustup.rs/ -FileName rustup-init.exe
    - rustup-init -yv --default-toolchain %channel% --default-host %target%
    - set PATH=%PATH%;%USERPROFILE%\.cargo\bin
    # Install ninja (used to build nng)
    - choco install ninja
-
  matrix:
    only:
      - image: Ubuntu
  install:
    - sudo apt-get update
    # Need cmake/ninja/clang to build nng
    - sudo apt-get --yes install cmake ninja-build build-essential clang-3.9
    # Download and run rustup to install Rust (need "-y" to avoid waiting for input)
    - curl https://sh.rustup.rs -sSf > rustup-init.sh
    - sh rustup-init.sh -y
    # Add cargo to PATH
    - source $HOME/.cargo/env

# Skip build step since `cargo test` does it
build: false

test_script:
  # Run only our tests (bindgen also generates tests)
  - cargo test --verbose -- "tests::" 
```

Enter fullscreen mode Exit fullscreen mode

所有的事情都是不幸的。我试图将`environment`节点放在`for`中，但是它没有导致构建置换。因此，将它移出并使用`matrix: exclude:`删除 Linux 上的 Windows 版本，反之亦然。

使用`for`节点为每个构建矩阵作业提供[不同的配置。](https://www.appveyor.com/docs/build-configuration/#specializing-matrix-job-configuration)

`x86_64-pc-windows-msvc`构建失败:

```
--- stderr
CMake Error at CMakeLists.txt:30 (project):
  Generator
    Ninja
  does not support toolset specification, but toolset
    host=x64
  was specified. 
```

Enter fullscreen mode Exit fullscreen mode

果然，上面那个是:

```
running: "cmake" "C:\\projects\\runng\\nng" "-Thost=x64" "-G" "Ninja" ... 
```

Enter fullscreen mode Exit fullscreen mode

这是由于[最近的一个变化](https://github.com/alexcrichton/cmake-rs/pull/57)在用`x86_64-pc-windows-msvc`编译时增加了`-Thost=x64`。[提交了一份 PR](https://github.com/alexcrichton/cmake-rs/pull/63) 来躲避`-Thost`与忍者。

`i686-pc-windows-msvc` (32 位)构建进一步失败:

```
CMake Warning:
  Manually-specified variables were not used by the project:
    CMAKE_CXX_COMPILER
thread 'main' panicked at 'Unable to find libclang: "couldn\'t find any of [\'clang.dll\', \'libclang.dll\'], set the LIBCLANG_PATH environment variable to a path where one of these files can be found (skipped: [(C:\\Program Files\\LLVM\\bin\\libclang.dll: invalid DLL (64-bit))])"', libcore\result.rs:945:5 
```

Enter fullscreen mode Exit fullscreen mode

[llvm 已经安装了](https://www.appveyor.com/docs/windows-images-software/#llvm)，它找到了`C:\Program Files\LLVM\bin\libclang.dll`，但它是 64 位的，而我们需要的是 32 位。看来我得手动安装 32 位 llvm 了…

## 特拉维斯

在调查 Rust 代码覆盖率的过程中，我发现 [Travis CI](https://travis-ci.org/) 有[对 Rust](https://docs.travis-ci.com/user/languages/rust/) 的明确支持。

通过使用类似于
的`.travis.yml`，可以很容易地配置一个构建，而不会弄乱 rustup-init

```
language: rust
rust:
  - stable
  #- nightly

os:
  - linux
  - osx

sudo: false
addons:
  apt:
    packages:
      # To build nng
      - cmake
      - ninja-build

script:
  - cargo build
  - cargo test -- "tests::" 
```

Enter fullscreen mode Exit fullscreen mode

我明确地避免使用`sudo`来支持[容器化的环境](https://docs.travis-ci.com/user/reference/overview/#Virtualization-environments)。特别是用 [`addons: apt:`](https://docs.travis-ci.com/user/installing-dependencies/#installing-packages-on-container-based-infrastructure) 安装包而不是`sudo apt-get install`。

使用`-- "tests::"`和`cargo test`来过滤掉 bindgen 的测试。出于某种原因其中一项测试没有通过。

这种构建完成得很快；只需要 2 分多钟，而 Appveyor 需要 4 分多钟(主要是安装 clang/llvm)。

## 代码覆盖率

代码覆盖率会让事情变得混乱。

有两个很好的帖子详细介绍了 Rust CI 与 Travis 的关系:[第一个](https://sunjay.ca/2016/07/25/rust-code-coverage) , [第二个](https://medium.com/@Razican/continuous-integration-and-code-coverage-report-for-a-rust-project-5dfd4d68fbe5)。使用 [kcov](https://github.com/SimonKagstrom/kcov) 进行代码覆盖，这两者都值得一读和详述。Travis 使用的是 Ubuntu 14.04 (Trusty Tahr)，所以可安装 apt 的 kcov 版本太旧，无法与 Rust 一起工作。

在`.travis.yml` :

```
language: rust
rust:
  - stable

os:
  - linux
  - osx

# Force full VM (kcov doesn't work in default container environment)
sudo: required
addons:
  apt:
    packages:
      # To build nng
      - cmake
      - ninja-build
      # To build kcov
      - libcurl4-openssl-dev
      - libelf-dev
      - libdw-dev
      - gcc
      - binutils-dev
      - libiberty-dev

before_install:
  # Using `source` so can update PATH environment variable
  - source ./scripts/install.sh

script:
  - cargo build
  - cargo test -- "tests::"

after_success:
  - ./scripts/after_success.sh 
```

Enter fullscreen mode Exit fullscreen mode

`install.sh`手动在 OSX 上安装忍者[以避免使用自制软件](https://docs.travis-ci.com/user/reference/osx#homebrew) :

```
#!/usr/bin/env bash

if [["$TRAVIS_OS_NAME" == "osx"]]; then
    # `brew install ninja` requires `brew update` which takes ages....
    wget https://github.com/ninja-build/ninja/releases/download/v1.8.2/ninja-mac.zip
    unzip ninja-mac.zip
    export PATH=`pwd`:$PATH
fi 
```

Enter fullscreen mode Exit fullscreen mode

步骤只在成功的构建上运行代码覆盖。`after_success.sh`来自[codecov Rust 和 travis](https://github.com/codecov/example-rust) 的例子(另见[关于 codecov](https://github.com/SimonKagstrom/kcov/blob/master/doc/codecov.md) 的 kcov 注释):

```
#!/usr/bin/env bash

TARBALL="v36.tar.gz"
KCOV_DIR="kcov-36"

if [["$TRAVIS_OS_NAME" == "linux"]]; then
    # https://github.com/codecov/example-rust
    wget https://github.com/SimonKagstrom/kcov/archive/$TARBALL
    tar xzf $TARBALL
    cd $KCOV_DIR
    mkdir build
    cd build
    cmake ..
    make
    make install DESTDIR=../../kcov-build
    cd ../../
    rm -rf $KCOV_DIR
    for file in target/debug/runng_sys-*[^\.d]; do mkdir -p "target/cov/$(basename $file)"
        # Arguments at the end are what would be passed to `cargo test`
        ./kcov-build/usr/local/bin/kcov --exclude-pattern=/.cargo,/usr/lib --verify "target/cov/$(basename $file)" "$file" -- "tests::"
    done
    # Upload reports in current directory
    # https://github.com/SimonKagstrom/kcov/blob/master/doc/codecov.md
    bash <(curl -s https://codecov.io/bash)
fi 
```

Enter fullscreen mode Exit fullscreen mode

这与 [codecov 示例](https://github.com/codecov/example-rust)略有不同:

1.  安装一个 kcov 版本而不是 github master
2.  使用`&&`避免长链
3.  运行`cargo test`时传递`-- "tests::"`参数

第一次运行时，覆盖率报告为空，构建日志包含:

```
Can't set personality: Operation not permitted
kcov: error: Can't start/attach to /home/travis/build/jeikabu/runng/target/debug/runng_sys-ee3c607355e4b215
Child hasn't stopped: ff00
kcov: error: Can't start/attach to /home/travis/build/jeikabu/runng/target/debug/runng_sys-ee3c607355e4b215 
```

Enter fullscreen mode Exit fullscreen mode

参见[本期 github](https://github.com/SimonKagstrom/kcov/issues/151)(以及[上文提到的博客](https://sunjay.ca/2016/07/25/rust-code-coverage#docker-security-settings))。基本上，除非使用特定选项运行 docker，否则 kcov 无法在容器中工作。似乎没有办法让 Travis 这样做，所以`sudo: required`使用一个完整的 VM 来避免这个问题。

## 带天赋的板条箱

将[打包和项目元数据](https://doc.rust-lang.org/cargo/reference/manifest.html)添加到`Cargo.toml` :

```
[package]
name = "runng-sys"
version = "0.1.1"
description = "Bindings to nng (Nanomsg-Next-Generation) aka Nanomsg2"
keywords = ["nng", "nanomsg"]
license = "MIT"
repository = "https://github.com/jeikabu/runng-sys"

[badges]
appveyor = { repository = "jake-ruyi/runng-sys", branch = "master", service = "github" }
travis-ci = { repository = "jeikabu/runng-sys", branch = "master" }
codecov = { repository = "jeikabu/runng-sys", branch = "master", service = "github" } 
```

Enter fullscreen mode Exit fullscreen mode

`repository`值分别是来自[供应商](https://www.appveyor.com/)、 [Travis](https://travis-ci.org/) 和 [codecov](https://codecov.io) 的`OWNER/PROJECT`值。

`cargo publish`将装帧打包成一个板条箱，并上传到[板条箱。io](https://crates.io/crates/runng-sys) :

[![](../Images/07615f822ba86f2f92e5a433eff9e9fe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uff1HNrF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/crates-runng-sys.png)

## 鳍

我很想在 OSX 上运行 kcov，这样我就可以使用容器化的 Linux 环境。但是我质疑仅仅因为 neat-o 因素而在一个“不太推荐”的平台上运行 kcov 是否明智。

我想为[的 nng-sys 机箱](https://crates.io/crates/nng-sys)做一个 PR 。

需要在 nng 绑定周围制作一个高级包装器来隐藏不安全的/指针诡计。可能会在 [nng 之后进行建模。网芯](https://github.com/subor/nng.NETCore)。