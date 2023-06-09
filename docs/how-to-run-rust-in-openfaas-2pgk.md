# 如何在 openfans 中运行 rust

> 原文：<https://dev.to/booyaa/how-to-run-rust-in-openfaas-2pgk>

我对 Kubernetes 很感兴趣，部分原因是它被捆绑在 Mac Edge 的 Docker 版本中。

一旦我通过 [Kubernetes by Example](http://kubernetesbyexample.com/) 了解了基础知识，我想了解更多关于 Docker 附带的 Kubernetes 的细节。我发现 Alex Ellis 的[博客帖子](https://blog.alexellis.io/docker-for-mac-with-kubernetes/)非常有用。

我也相信这篇博文让我进入了 OpenFaaS 和无服务器功能。

这篇博客文章是对我上周日写的一篇推文的扩展。

我刚刚为 OpenFaas 创建了一个概念验证 Rust 函数模板。

从那时起，我已经能够清理模板，并提出一个更实质性的例子。当我在推特上发布我的成功时，Alex (OpenFaaS 维护者)讲述了 Erik Stoekl 的[版本](https://github.com/ericstoekl/faas-custom-templates)。令人欣慰的是我们没有太大的不同。

## 什么是功能

在功能即服务或无服务器功能的背景下，功能成为继平台即服务(PaaS)之后的下一个抽象级别。如果您认为 PaaS 是简化软件发布流程的一种方式:您对代码进行更改，将它们合并到您的主分支中，PaaS 处理软件的打包和交付，并提供为您的应用量身定制的服务器。

函数进一步深化了这种抽象，但是消除了对软件如何与其环境交互的关注。你不再考虑 RBAC、表示(webserver、stdio)的问题，你只需编写最少的代码就能完成工作。

伊恩·库珀(Ian Cooper)对 FaaS 和微服务做了最好的总结。套用他的话:你的单元测试就是你的功能。你不必关心职能任务之外的事情。

## 什么是 OpenFaaS

OpenFaaS 是 Function as a Service(无服务器功能，微服务)的开源实现，您可以自行托管。我不会列出这一领域的所有产品，我会向您推荐[云本地计算基金会](https://landscape.cncf.io/)，特别是[互动平台](https://landscape.cncf.io/)。

您可以部署现有的[功能](https://github.com/openfaas/store)或者创建新的功能。如果你创建新的语言，有一个官方支持的语言的大列表。或者，你可以将一个[命令行界面转换成函数](https://blog.alexellis.io/cli-functions-with-openfaas/)。

一旦我尝试了 Python 和 Ruby 作为入门，我就想看看创建 Rust 模板有多简单。

## 剖析一个 OpenFaaS 模板

模板需要满足以下条件:

*   作为函数驱动程序的脚本/程序。使用您的模板的最终用户不会看到或使用此代码
*   一个带有`handle`功能的脚本/程序，它将为您的最终用户提供样板文件
*   一个`Dockerfile`，它将捆绑和构建驱动程序和函数，并安装一个看门狗进程(稍后会详细介绍)。
*   一个`template.yml`，它至少应该提供模板的编程语言和启动处理程序的代码。

## 决定生锈模板背后的设计

为了创建 Rust 模板，我创建了两个板条箱(我们稍后将讨论其功能):

*   `main`机箱充当该功能的驱动程序
*   `function`箱包含具有`handle`功能的库

我用`cargo new`创造了新的板条箱，并为 Rust 项目提供了所有必要的管道。

关于`function`板条箱的重要说明。OpenFaaS 希望找到一个名为`function`的文件夹，如果你称它为其他名称，当使用模板创建新函数时，它不会复制样板代码。

使用相对路径，`main`板条箱将`function`板条箱拉入作为依赖。

`Cargo.toml`(部分章节省略)

```
[dependencies]
handler = { path = "../function" } 
```

Enter fullscreen mode Exit fullscreen mode

为了`Dockerfile`，我去找了[官方](https://hub.docker.com/_/rust/)稳定的铁锈形象。

### 功能的流程

驱动程序读取标准输入并传递给函数。

```
use std::io::{self, Read};

extern crate handler;

fn main() -> io::Result<()> {
    let mut buffer = String::new();
    let stdin = io::stdin();
    let mut handle = stdin.lock();

    handle.read_to_string(&mut buffer)?;

    Ok(())
} 
```

Enter fullscreen mode Exit fullscreen mode

我从标准库[文档](https://doc.rust-lang.org/std/io/struct.Stdin.html#examples)中复制了这段代码。

该函数解析输入并将其输出返回给驱动程序。

```
pub fn handle(req : String) -> String {
    req
} 
```

Enter fullscreen mode Exit fullscreen mode

## 演示

```
faas-cli template pull https://github.com/booyaa/openfaas-rust-template
faas-cli new trustinrust --lang rust 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的文件夹结构的样子。

```
.
├── trustinrust
│   ├── Cargo.toml
│   └── src
│       └── lib.rs
└── trustinrust.yml 
```

Enter fullscreen mode Exit fullscreen mode

您想要编辑`trustinrust.yml`并更新以下值(代码的其他部分已被省略):

```
provider:
  gateway: http://127.0.0.1:31112

functions:
  shuffle:
    image: DOCKER_ID/shuffle 
```

Enter fullscreen mode Exit fullscreen mode

`gateway`假设您运行在端口 8080 上，kubernetes 运行在不同的端口上。

我还发现容器找不到本地存储库，所以将你的 docker id 添加到`image`可以确保它上传到 [Docker Hub](https://hub.docker.com/) 。

`trustinrust`文件夹包含来自`function`箱的样板代码。

在我们的演示中，我们将创建一个函数来打乱逗号分隔的项目列表。

我们将把`rand`箱添加到`Cargo.toml`中的家属中。正如你所看到的，我们正在编写标准的 Rust，在需要的时候添加一些库。

```
[dependencies]
rand = "0.5.1" 
```

Enter fullscreen mode Exit fullscreen mode

这是将要放入`src/lib.rs`(为我糟糕的生锈道歉)
的代码

```
extern crate rand;

use rand::{thread_rng, Rng};

pub fn handle(req : String) -> String {
    let mut rng = thread_rng();
    let split = req.split(",");
    let mut vec: Vec<&str> = split.collect();
    rng.shuffle(&mut vec);
    let joined = vec.join(", ");
    format!("{:?}", joined)
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您愿意，您可以运行`cargo build`来查看代码是否会构建。如果你有测试，这可以通过`cargo test`来测试。

现在我们可以构建、将映像推送到 Docker Hub 并部署到 OpenFaaS。

```
faas-cli build -f trustinrust.yml
faas-cli push -f trustinrust.yml    # pushes image to docker
faas-cli deploy -f trustinrust.yml 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以测试我们的功能！

```
curl -d 'alice,bob,carol,eve' \
> http://localhost:31112/function/trustinrust

"bob, alice, eve, carol" 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

尝试无服务器功能和 Rust 非常有趣。我可以看到无服务器功能的吸引力，将功能减少到绝对最小。我想我在这个领域的下一个尝试是看看我是否能把我的 Slack slash 命令转换成无服务器函数(它们目前托管在 Heroku 上)。

如果你必须自己去，模板和演示功能可以在下面找到:

*   模板:[github.com/booyaa/openfaas-rust-template](https://github.com/booyaa/openfaas-rust-template)
*   对 rust 函数的信任:[github.com/booyaa/trustinrust](https://github.com/booyaa/trustinrust)

## 进一步阅读

如果你想了解更多，你应该去看看 Kubernetes 传奇人物 Kelsey Hightower 的视频。我可以强烈推荐 Pythonistas 的 [Kubernetes，它的特色是现在著名的 DevOps 的俄罗斯方块类比。还可以在 youtubes 上找到 Alex Ellis 和他收藏的优秀的](https://youtu.be/u_iAXzy3xBA) [Docker、Kubernetes 和 OpenFaaS](https://www.youtube.com/watch?v=0DbrLsUvaso) 视频。