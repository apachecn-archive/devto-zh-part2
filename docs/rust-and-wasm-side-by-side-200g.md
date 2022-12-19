# Rust 和 Wasm 并排

> 原文：<https://dev.to/jwir3/rust-and-wasm-side-by-side-200g>

## 背景

[![Rust and Wasm Side-by-Side](img/eaf3bd2fdb54c3f52e627c031e33a1ca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XrP8DB-O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.jwir3.com/conteimg/2018/10/crab-298346_1280.jpg)

在我的工作中，我们正在考虑使用 [WebAssembly](https://webassembly.org/) (以下缩写为 WASM)，因为它允许我们交叉编译几乎任何在网络上使用的语言。WebAssembly 是“基于堆栈的虚拟机的二进制指令格式”。基本上，这意味着它是一种设计成可以在任何地方运行的二进制语言，但一般来说，它现在被用在 web 浏览器中作为 Javascript 模块的替代品。我们考虑用作源语言的语言之一是 [Rust](https://doc.rust-lang.org/book/2018-edition/index.html) ，一种新的(ish)语言，为类型安全、性能和并发性而设计。

因此，我一直在阅读 Rust 编程语言，更重要的是，如何[将 Rust 编译成 WebAssembly](https://rustwasm.github.io/book/) 。在学习了 RustWasm 书中给出的示例模块后，我决定将我个人的一个 npm 库从 JS->Rust<sup>[【1】](#fn1)</sup>转换过来。这一努力的第一步是将我在 mocha/chai 中的现有测试转换为 Rust 的原生测试格式，以便当我添加代码时，我可以验证它是否如我预期的那样工作<sup>[【2】](#fn2)</sup>。然而，当我转移到 WASM 目标时，运行这些测试的能力成了一个问题。

## 问题

将 Rust 编译成 WASM 的一个很大的缺点是，没有一个好的方法来调试浏览器中的代码，并让它链接回 Rust 源代码。拉斯特-WASM 的书在关于调试的章节中提到了这一点:

> 不幸的是，WebAssembly 的调试还不成熟。在大多数 Unix 系统上，DWARF 用于对调试器提供正在运行的程序的源代码级检查所需的信息进行编码。在 Windows 上有一种替代的编码类似信息的格式。目前，WebAssembly 还没有对等的功能。

相反，他们建议使用测试(特别是自动化测试)来识别回归*在*进入构建之前。这是一个很好的想法，我显然支持这个想法，因为我在实现我的库之前就已经开始编写测试了。然而，当编译到 WASM 时，在 Rust 中编写测试有一个很大的缺点，正如 Rust-WASM 的书的[的下一小节所详述的:](https://rustwasm.github.io/book/game-of-life/debugging.html#avoid-the-need-to-debug-webassembly-in-the-first-place)

> 请注意，为了在没有编译器和链接器错误的情况下运行#[test ],您需要注释掉 wasm-game-of-life/Cargo.toml 中的 crate-type = "cdylib "位。

等待...什么？为了运行我的 Rust 测试，我需要*对我的源代码库进行修改，以便构建*？这对我来说有点不切实际，因为我想在一个持续集成系统上运行，在那里我能够构建 Rust 包，运行测试，构建 WASM 二进制，然后部署到 NPM，所有的*都自动*。

换句话说，我希望能够做的事情是这样的:

```
# These commands compile (and subsequently test) the native Rust code
$ cargo build
$ cargo test

# This command builds the wasm module
$ cargo build --target=wasm32-unknown-unknown 
```

然而，这里的关键是我希望能够在不需要对源文件或构建文件做任何修改的情况下完成这个*。*

## 可能的解决方案

我花了相当多的时间试图确定我在这里能做些什么。我想到的一个想法是，我可以运行一个预构建脚本来手动注释掉`#[wasm_bindgen]`属性。其工作原理是，在构建之前，脚本会将本地目录中的所有内容复制到一个子目录中。然后它会将`subdirectory/Cargo.toml`中的`crate-type`切换到`lib`，而不是`cdylib`，并注释掉`#[wasm_bindgen]`的任何实例。不用说，我不想走这条路——它看起来非常脆弱而且容易出错。

接下来，我的一位同事建议我将本机 Rust 代码从代码的 WASM 方面分离出来，并让 WASM 库利用来自本机库的代码，类似于 [geotoy](https://www.github.com/fitzgen/geotoy) 如何处理这个<sup>[【4】](#fn4)</sup>。这是一个很好的解决方案，但是，当我建立我的库时，我怎么也想不出如何将一个枚举或结构导入到库的 WASM 部分。

基本上，geotoy 有一个设置，它使用 WASM 库来公开一组作为库的 API 的*函数*。这些函数然后利用`src/lib.rs`中的数据结构。然而，数据结构*本身*并没有公开。用`cratchit`，我要暴露的是`Account`和`AccountsChart`的数据结构，以及`Currency`和`AccountType`的枚举*直接*，而不是一套利用这些数据结构<sup>[【5】](#fn5)</sup>的网关函数。我怀疑可能有*或*方法让它公开结构，但我没能搞清楚。

## 最终解决

我最终达成的解决方案本身并没有分离库。相反，它使`#[wasm_bindgen]`属性取决于你是否为 WASM 目标建造。

你需要做的第一件事是确保在你的`Cargo.toml` :
中同时使用 `cdylib` *和* `rlib`(或`lib`)

```
[lib]
crate-type = ["cdylib", "rlib"] 
```

接下来，(这可能是针对我的)，我必须将`#![feature(custom_attribute)]`添加到我的箱子属性的顶部(即`src/lib.rs`的顶部:

```
#![feature(custom_attribute)]

extern crate cfg_if;
extern crate json;
extern crate wasm_bindgen;

use wasm_bindgen::prelude::*;
use cfg_if::cfg_if;
use std::collections::HashMap;

... 
```

接下来，在您之前使用`#[wasm_bindgen]`的地方，使它以目标架构为条件:

```
/// Use this instead of #[wasm_bindgen]
#[cfg_attr(target_arch = "wasm32", wasm_bindgen)] 
```

最后，这可能是我的用例特有的，你需要将类型从子模块中取出，放入根箱`src/lib.rs`。换句话说，在我的`src/lib.rs`中，我有如下:

```
pub mod accounts;
pub mod currency; 
```

我有两个文件，`src/currency.rs`和`src/accounts.rs`，它们分别定义了与账户和货币相关的类型。`wasm_bindgen`似乎不喜欢这样，所以我把这段代码移到了`src/lib.rs`中，并删除了测试中对模块的引用。

我所做的测试最初并没有使用`wasm-pack`。相反，我运行`cargo +nightly build --target=wasm32-unknown-unknown`来确保不存在错误，并且它在适当的`target`目录中创建了一个. wasm 文件:

```
$ cargo +nightly build --target=wasm32-unknown-unknown
...
$ ls -al target/wasm32-unknown-unknown/debug
total 9520
drwxr-xr-x@ 13 scottj staff 416 Oct 26 10:15 .
drwxr-xr-x 3 scottj staff 96 Oct 26 10:14 ..
-rw-r--r-- 1 scottj staff 0 Oct 26 10:14 .cargo-lock
drwxr-xr-x 8 scottj staff 256 Oct 26 10:14 .fingerprint
drwxr-xr-x 3 scottj staff 96 Oct 26 10:14 build
-rw-r--r-- 1 scottj staff 122 Oct 26 10:15 cratchit.d
-rwxr-xr-x 2 scottj staff 3549051 Oct 26 10:15 cratchit.wasm
drwxr-xr-x 13 scottj staff 416 Oct 26 10:15 deps
drwxr-xr-x 2 scottj staff 64 Oct 26 10:14 examples
drwxr-xr-x 3 scottj staff 96 Oct 26 10:15 incremental
-rw-r--r-- 1 scottj staff 125 Oct 26 10:15 libcratchit.d
-rw-r--r-- 2 scottj staff 1311774 Oct 26 10:15 libcratchit.rlib
drwxr-xr-x 2 scottj staff 64 Oct 26 10:14 native 
```

当然，我确保测试运行时没有任何变化:

```
$ cargo test
running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

     Running target/debug/deps/test_accounts-54475044840749bd

running 6 tests
test account_type_from_integer ... ok
test account_creation ... ok
test account_type_from_string ... ok
test adding_top_level_accounts_to_accounts_chart ... ok
test getting_all_account_ids_in_a_chart ... ok
test creating_accounts_chart_from_json ... ok

test result: ok. 6 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

     Running target/debug/deps/test_currency-d2d59a6d3436c103

running 1 test
test currency_translation_from_string ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

   Doc-tests cratchit

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out 
```

现在，当您想要构建您的 WASM 模块时，您可以运行:

```
wasm-pack build 
```

并且，它将在`pkg`中为您创建打包的 WASM 库。

## 奖励:对崔维斯进行测试，并将 WASM 部署到 NPM

这是另一个对我来说需要一点理解的领域。我在 Travis-CI 上遇到的问题是，默认情况下它不会安装`wasm-pack`。因此，您需要在脚本中手动安装它。但是，如果您启用了`cache: cargo`，如果之前安装了`wasm-pack`，它将会挂起。因此，我在我的`.travis.yml`文件中添加了以下内容，以便有条件地安装`wasm-pack` :

```
before_script: |
  if hash wasm-pack 2>/dev/null; then
      echo "Wasm-pack already is installed"
  else
      curl https://rustwasm.github.io/wasm-pack/installer/init.sh -sSf | sh
  fi 
```

现在，您可以将以下内容添加到您的`script`部分，构建 WASM 模块和本机 Rust 模块:

```
script:
- cargo clean
- cargo build
- cargo test
- wasm-pack build --target=nodejs 
```

注意调用`wasm-pack build`的部分有一个额外的参数:`--target=nodejs`。如果您想使用`npm link`进行本地测试，您将需要这个参数，因为它在`package.json`中用一个`main`参数打包了 WASM 模块。

最后，添加部署逻辑<sup>[【6】](#fn6)</sup>:

```
before_deploy:
- cd pkg
deploy:
  provider: npm
  email: <your email address>
  on:
    tags: true
  skip_cleanup: true
  api_key:
    secure: <YOUR_API_KEY> 
```

请注意，这个部署逻辑将只部署在带标签的版本上，所以如果您想要不同的行为，您可能需要更改它。

最后要意识到的一件事:这目前需要*每夜*生锈。所以，你需要确保在本地使用 nightly Rust，如果你在 travis 上构建，你需要确保允许`stable`和`beta` Rust 失败:

```
rust:
- stable
- beta
- nightly
matrix:
  allow_failures:
    - rust: stable
    - rust: beta 
```

* * *

1.  这个库叫做 [Cratchit](http://www.github.com/jwir3/cratchit) ，是一个为运行在电子系统中的会计应用程序封装会计数据的库。熟悉这个库并不重要，但是如果您想继续学习，请自便。 [↩︎](#fnref1)

2.  关于我移植到 Rust 的其余冒险经历，我将一笔带过，因为它并不是非常有趣，而且涉及到我与借货检查器的多次斗争以使事情正常工作。 [↩︎](#fnref2)

3.  如果你对我对这个问题的思考和我如何得出解决方案的*不感兴趣，请随意跳到我使用的解决方案部分，在这篇文章的后面。 [↩︎](#fnref3)*

4.  这个有问题的同事是 Johannes Hoff，他也是 geotoy repo 的贡献者。有趣的是，geotoy 的原作者[尼克·菲茨杰拉德](http://www.github.com/fitzgen)，当我刚开始在 Mozilla 工作时，是那里的实习生。有时我会惊讶于这个世界是如此之小，因为我已经很多年没有见到菲茨根了，而我的一个同事碰巧有一个解决我的问题的办法，与我密切合作的这个*另一个*人合作。有时命运之轮令我震惊。 [↩︎](#fnref4)

5.  可能有一个很好的理由来公开网关函数，而不是底层数据结构本身，我将在将来的某个时候发现这一点，但是现在，我选择的解决方案似乎更干净一些。 [↩︎](#fnref5)

6.  确保您加密了 api 密钥。您可以通过从 travis-ci.org 检索一个 api 密钥，然后运行:`travis encrypt <api_key> --add deploy.api_key`来实现这一点。我一直等到有了我想要的配置，才开始这样做。 [↩︎](#fnref6)