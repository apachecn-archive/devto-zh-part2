# 让我们建立一个锈前端与紫杉-第 1 部分

> 原文：<https://dev.to/deciduously/lets-build-a-rust-frontend-with-yew---part-1-3k2o>

# 万普斯季节

在这个帖子系列中，我们将在[紫杉](https://github.com/DenisKolodin/yew)中重现经典的[狩猎 Wumpus](https://en.wikipedia.org/wiki/Hunt_the_Wumpus) 游戏。原来是在命令行播放的，我们要用一个网页。紫杉让我们用铁锈来定义我们的前端。我们的应用程序将被编译到 [WebAssembly](https://webassembly.org/) 中执行。

这个 app 需要这个吗？*号*

有没有*的 app 需要这个？有争议，但可能。在评论中讨论吧！*

我们到底要不要做？太好了！

这是一个初级教程-熟悉阅读 Rust 是有帮助的，但这里没有什么太花哨的东西。任何命令式语言中的安慰都应该是足够的。

我把它分成了三部分。第一部分旨在作为开始你自己的空白项目的有用指南。还没有搜索 wumpus，只是用适合你的应用的东西替换填充文本。第二部分设置我们在洞穴中移动的基本 UI 和机制[第三部分](https://dev.to/deciduously/lets-build-a-rust-frontend-with-yew---part-3-ch3)讨论游戏逻辑。

编辑:可以在这里播放完成的 app [！](https://deciduously.github.io/hunt-the-wumpus/)

## 设置

Rust 提供了一些很棒的工具，使得编译过程相对容易。我们使用的紫杉只是已经存在的几种方法之一。如果你喜欢你在这里找到的东西，接下来我向你推荐 [RustWasm 的书](https://rustwasm.github.io/book/introduction.html)。它带你构建一个生活游戏`<canvas>`应用程序，而不使用任何花哨的框架或工具——从那里你可以挑选你需要的。你可以决定你想要多低或多高的水平。另外，一定要看看 [draco](https://github.com/utkarshkukreti/draco) ，一个替代的客户端 Rust- > Wasm 框架。

你将需要一个夜间 Rust 编译器。如果需要，请参见 [rustup](https://rustup.rs/) 开始——这很简单。你还需要 [`cargo-web`](https://github.com/koute/cargo-web) : `cargo install cargo-web`。

一旦安装完毕，导航到项目目录并在终端发出`cargo new hunt-the-wumpus`。在您选择的文本编辑器中打开该文件夹。我们将从添加足够的东西来编译和运行一切开始。

首先让我们设置我们的项目文件夹来使用内置的 Rust 目标。发出以下命令:

```
$ rustup override set nightly
$ echo 'default-target = "wasm32-unknown-unknown"' > Web.toml 
```

Enter fullscreen mode Exit fullscreen mode

这将确保`cargo web`命令总是使用正确的目标。`rustup override`命令是特定于目录的——使用`rustup default nightly`对其进行全局更改。我更喜欢默认为稳定，只在必要时在夜间使用。

现在让你的`Cargo.toml`看起来如下:

```
[package]
authors = ["Hunter Wumpfrey <hw@bottomlesspit.net>"]
edition = "2018"
name = "hunt-the-wumpus"
version = "0.1.0"
[[bin]]
name = "hunt"
path = "src/main.rs"

[dependencies]
stdweb = "0.4"

[dependencies.yew]
version = "0.9.2"

[lib]
name = "hunt_the_wumpus"
path = "src/lib.rs" 
```

Enter fullscreen mode Exit fullscreen mode

我们的大部分代码将存储在库中，二进制代码将把应用程序安装到页面上。

接下来，用以下内容替换您的`main.rs`:

```
extern crate hunt_the_wumpus;
extern crate yew;

use hunt_the_wumpus::Model;
use yew::prelude::App;

fn main() {
    yew::initialize();
    let app: App<Model> = App::new();
    app.mount_to_body();
    yew::run_loop();
} 
```

Enter fullscreen mode Exit fullscreen mode

这个存根将找到我们的挂载点，并将我们的程序附加到它上面。说到这里，我们来创建一个挂载点。问题:

```
$ mkdir static
$ touch static/index.html 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要一个存根。将以下内容添加到该文件并保存:

```
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="Hunt the wumpus!">
  <meta name="author" content="YOU">
  HUNT THE WUMPUS
  <link rel="stylesheet" type="text/css" href="hunt.css">
  <script src="hunt.js"></script>
</head>

<body>
</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

我们不再需要它了——它只是加载了我们编译的 JS 和样式表。这个`static`目录也是你最喜欢的图标所在的地方——我喜欢[这个](https://www.favicon.cc/?action=icon&file_id=701981)。

现在，让我们添加基本的紫杉轮廓-我们要渲染的东西。问题:

```
$ touch src/lib.rs 
```

Enter fullscreen mode Exit fullscreen mode

用下面的模板填充:

```
extern crate stdweb;
#[macro_use]
extern crate yew;

use yew::prelude::*;

pub struct Model {
  arrows: u8,
}

#[derive(Debug, Clone)]
pub enum Msg {}

impl Component for Model {
  type Message = Msg;
  type Properties = ();

  fn create(_: Self::Properties, _: ComponentLink<Self>) -> Self {
    Model { arrows: 5 }
  }

  fn update(&mut self, _msg: Self::Message) -> ShouldRender {
    true
  }
}

impl Renderable<Model> for Model {
  fn view(&self) -> Html<Self> {
    html! {
        <div class="hunt",>
            <div class="header",>{"Hunt the Wumpus"}</div>
            <div class="body",>
              <span class="arrows",>{&format!("Arrows: {}", self.arrows)}</span>
            </div>
        </div>
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是我们大部分组件的外观。如果您使用过其他前端框架，这应该看起来有些熟悉。有一个`Component`特征，我们可以定义像`create`和`update`这样的状态转换，还有一个`Renderable<T>`特征，用一个类似 JSX 的`html!`宏来定义视图。然后，它从 Elm 等工具中汲取灵感，提供一个`Msg`类型，以`update`方法驱动我们的事件。我们还没有任何要处理的消息，所以我们只是包含一个存根。开始`update`将总是为`ShouldRender`返回`true`，触发重绘。

在我们开始编码之前，我们需要设置构建管道的其余部分。我们将使用[`yarn`](https://yarnpkg.com/en/)——毕竟这是一个网络应用。

```
$ yarn init
// answer the questions
$ yarn add -D @babel/core @babel/preset-env autoprefixer node-sass nodemon npm-run-all postcss postcss-cli rollup rollup-plugin-babel rollup-plugin-postcss rollup-plugin-uglify rollup-plugin-wasm serve 
```

Enter fullscreen mode Exit fullscreen mode

然后将这些脚本添加到您的`package.json` :

```
 "scripts":  {  "build:js":  "rollup -c",  "build:rs":  "cargo web deploy --release",  "build:scss":  "node-sass --include-path scss scss/hunt.scss css/hunt.css",  "build:css":  "postcss --use autoprefixer -o static/hunt.css css/hunt.css",  "build:style":  "run-s build:scss build:css",  "build:copy":  "cp target/deploy/hunt.css release/ && cp target/deploy/hunt.wasm release/ && cp target/deploy/index.html release/ && cp target/deploy/favicon.ico release/",  "build":  "run-s clean:deploy build:rs build:js build:style build:copy",  "clean:deploy":  "rm -rf /release",  "prod":  "run-s build serve",  "serve":  "serve -p 8080 release",  "watch:rs":  "cargo web start --release",  "test":  "echo \"Error: no tests!\" && exit 1"  }, 
```

Enter fullscreen mode Exit fullscreen mode

要设置我们应用程序范围的样式表，发出:

```
$ mkdir scss
$ touch scss/hunt.scss 
```

Enter fullscreen mode Exit fullscreen mode

为了确保所有的都连接好了，把下面的内容放进去:

```
.arrows {
  font-weight: bold;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们按下大按钮。打开你的终端，发出

```
$ yarn build:style
$ yarn watch:rs 
```

Enter fullscreen mode Exit fullscreen mode

最后，将你的浏览器指向`localhost:8000`。您应该看到以下内容:

猎杀巫女
**箭头:5**

我们开始运行了！开发配置工作正常。让我们完成我们的`.gitignore` :

```
/target
**/*.rs.bk
/node_modules
yarn-*.log
/css
/static/*.css
/release 
```

Enter fullscreen mode Exit fullscreen mode

让我们测试一下我们的产品包。首先创建`rollup.config.js`并保存以下内容:

```
import babel from "rollup-plugin-babel"
import uglify from "rollup-plugin-uglify"

export default {
    input: './target/deploy/hunt.js',
    output: {
        name: 'hunt',
        file: './release/hunt.js',
        format: 'es',
    },
    plugins: [
        babel({
            exclude: 'node_modules/**'
        }),
        uglify
    ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在确保退出`watch:rs`进程，然后尝试`yarn prod`。当构建完成时，您应该在`localhost:8080`看到相同的输出。

一旦一切正常，就提交吧！`git init && git commit -m "Initial commit`。"

第 1 部分末尾的完整代码见[此处](https://github.com/deciduously/hunt-the-wumpus/tree/master/part1)。

如果你准备好继续构建，请参见[第 2 部分](https://dev.to/deciduously/lets-build-a-rust-frontend-with-yew---part-2-1ech)。