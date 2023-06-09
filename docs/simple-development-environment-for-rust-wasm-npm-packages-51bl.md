# Rust-WASM-npm 软件包的简单开发环境

> 原文：<https://dev.to/stereobooster/simple-development-environment-for-rust-wasm-npm-packages-51bl>

> Paola Aguilar 在 Unsplash 上拍摄的照片

本教程只有一个先决条件——安装 Docker。嗯，你还需要一个良好的互联网连接，因为铁锈图像重 1.7Gb

```
docker run -v "$PWD":/usr/src/myapp -w /usr/src/myapp --rm --interactive --tty stereobooster/rust-wasm
USER=stereobooster cargo generate --git https://github.com/rustwasm/wasm-pack-template
cd wasm-nanoid
wasm-pack init 
```

Enter fullscreen mode Exit fullscreen mode

用你的 GitHub 手柄代替我的(`USER=stereobooster`)。用你的项目名代替我的(`wasm-nanoid`)。

**就是它了**。您的开发环境准备就绪。继续阅读，了解如何编写和测试包。

## 养成

编辑`Cargo.toml` :

```
description = "nanoid implemented in wasm"
repository = "https://github.com/stereobooster/wasm-nanoid"
license = "MIT" 
```

Enter fullscreen mode Exit fullscreen mode

编辑`README.md`。添加到`.gitignore` :

```
*.log
pkg/* 
```

Enter fullscreen mode Exit fullscreen mode

提交(您可能希望在 OS shell 中这样做，因为 Docker 中的 git 没有配置)。

```
git add .
git commit -m "initial commit" 
```

Enter fullscreen mode Exit fullscreen mode

我想构建简单的东西，所以我将重用现有的 Nano ID 包(板条箱)。

将依赖关系添加到`Cargo.toml` :

```
js-sys = "0.2.6"
nanoid = "0.2.0" 
```

Enter fullscreen mode Exit fullscreen mode

编辑`src/lib.rs` :

```
extern crate cfg_if;
extern crate wasm_bindgen;
extern crate js_sys;
// import nanoid module
extern crate nanoid;

// for [wasm_bindgen] instruction
use wasm_bindgen::prelude::*;

// the function itself
#[wasm_bindgen]
pub fn simpleNanoid() -> js_sys::JsString {
    // generate nanoid and convert value (str) to JsString
    js_sys::JsString::from(nanoid::simple())
} 
```

Enter fullscreen mode Exit fullscreen mode

在 docker shell 中构建运行:

```
wasm-pack init 
```

Enter fullscreen mode Exit fullscreen mode

## 设置测试环境

在 OS shell 中运行:

```
npm init wasm-app example 
```

Enter fullscreen mode Exit fullscreen mode

在项目的根目录下创建`package.json`:

```
{  "private":  true,  "workspaces":  ["*"]  } 
```

Enter fullscreen mode Exit fullscreen mode

编辑`.gitignore`(添加):

```
node_modules 
```

Enter fullscreen mode Exit fullscreen mode

编辑`example/package.json` :

```
"devDependencies": {
  "wasm-nanoid": "^0.1.0",
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

现在运行这个例子:

```
cd example
yarn
yarn start 
```

Enter fullscreen mode Exit fullscreen mode

## 发布

如果要在配置了 npm 凭据的 OS shell 中发布运行:

```
cd pkg
npm publish 
```

Enter fullscreen mode Exit fullscreen mode

## 结果

在我的例子中，我得到了(在示例应用程序控制台中):

```
Error importing `index.js`: RuntimeError: unreachable
    at __rust_start_panic (wasm-function[79]:1) 
```

Enter fullscreen mode Exit fullscreen mode

和`wasm_nanoid_bg.wasm`是 50Kb，但至少它很容易创建，测试和发布我的第一个(不工作)WASM npm 包。将回到[教程](https://rustwasm.github.io/book/game-of-life/setup.html)。

这篇文章的代码在这里发布。

* * *

在 [twitter](https://twitter.com/stereobooster) 和 [github](https://github.com/stereobooster) 关注我。