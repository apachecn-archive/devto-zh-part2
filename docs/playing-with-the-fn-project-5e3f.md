# 玩 Fn 项目

> 原文：<https://dev.to/peterj/playing-with-the-fn-project-5e3f>

我不是那种可以简单地阅读白皮书/代码/文档，不需要在实践中尝试就能快速弄清楚事情是如何工作的幸运儿。我需要安装一些东西，运行一些东西，玩一些东西，来感受一下这些东西是如何工作的。如果我卡住了，那就是我要阅读文档和代码来看看我错过了什么的时候。

在这个版本中，我决定弄清楚 Fn 项目是如何工作的，如何运行它，使用它，甚至扩展它。Fn 项目是容器原生的、云不可知的无服务器平台。

作为本文的一部分，我为 Fn 服务器创建了三个不同的示例扩展——您可以在 [GitHub](https://github.com/peterj/fn-extensions) 上获得它们。

# 基础知识—安装和运行

本节将介绍如何安装 Fn 服务器，启动它，然后创建一个简单的函数并调用它。

你需要在你的机器上安装 Docker 来运行 Fn，一旦你有了 Docker，你就可以用 brew 来安装 Fn:

```
brew install fn 
```

Enter fullscreen mode Exit fullscreen mode

> 显然，我以前安装过 Fn，而我机器上的 Docker 映像太陈旧，导致启动时 Fn 失败——运行`fn update`将确保你的机器上有
> 最新的映像。

最后，安装了 Fn 并更新了映像后，您可以使用以下命令运行 Fn 服务器:

```
fn start 
```

Enter fullscreen mode Exit fullscreen mode

以上命令在单服务器模式下运行 Fn，并嵌入数据库和队列。在后台，fn start 命令以特权模式运行一个名为 fnproject/fnserver 的 Docker 映像。它还将 Docker 套接字装入容器以及当前工作目录中的/data 文件夹(这是存储数据库和队列信息的地方)。最后，它向主机公开端口 8080，因此您可以在该端口上调用它。

[![docker ps](../Images/abf075d28b747e318fed549d353be30b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NtJ2p8hE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Az3us6YbBtLWWHPQ-OVHckg.png)

现在您已经运行了 Fn 服务器，您可以创建一个新的函数。

# 第一功能

Fn CLI 附带一个 init 命令，用于创建新函数。

> 在撰写本文时，这些是支持的函数运行时:dotnet，go，
> java8，java9，java，lambda-nodejs4.3，lambda-node-4，node，php，python，
> python3.6，ruby，rust，kotlin

开始之前，先简单解释一下 Fn 使用的不同概念:

**应用程序**
应用程序是一种将您的功能以相同的名称进行逻辑分组的方式(例如，欢迎应用程序)

**路线**
每个功能都位于应用程序中的一条路线下(例如/迎宾员-应用程序/你好或/迎宾员-应用程序/再见)

**图片**
打包你的函数的 Docker 图片；所使用的图像取决于函数的语言(e.g.fnproject/go、fnproject/ruby、fnproject/node 等等)，这里的目标是图像尽可能小，以提高性能

**Calls**
Call 保存关于对该函数的调用的信息。它包括有关应用程序的信息、通话的创建、开始和完成时间以及通话的状态。

现在，让我们通过提供运行时(例如 Go、Node 或其他支持的语言)和函数名来创建一个新函数:

```
fn init --runtime go hello 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令在 hello 子文件夹中创建一个 Go 函数。函数结构是这样的:

```
hello
├── Gopkg.toml
├── func.go
├── func.yaml
└── test.json 
```

Enter fullscreen mode Exit fullscreen mode

您的函数的源代码位于 func.go 文件中，并且有一个函数处理程序以“Hello World”消息作为响应。`func.yaml`文件包含诸如版本运行时、名字和函数入口点的信息。

另一个有趣的文件是`test.json`——这个文件包含一组测试(输入值和预期输出值)，您可以通过运行`fn test`用它来测试您的函数。

要运行该功能，您可以使用`fn run`命令。在运行该命令之前，确保将环境变量`FN_REGISTRY`设置到 Docker 存储库中。

然后当你运行命令时，Fn 会用函数构建 Docker 镜像，并像这样运行函数:

```
$ fn run
Building image hello:0.0.1 ...........
{"message":"Hello World"} 
```

Enter fullscreen mode Exit fullscreen mode

这一切都很好，但我们有本地运行的 Fn 服务器，所以让我们将我们的功能部署到服务器上，而不只是运行它。

要部署该功能，您可以使用 fn deploy 命令，指定应用程序名称并添加`--local`，因为 fn 服务器正在本地运行:

```
fn deploy --app myapp --local 
```

Enter fullscreen mode Exit fullscreen mode

命令将应用程序(名为`myapp`)部署到本地 Fn 服务器，并创建一个名为`/hello`(我们的函数名)的路径。

[![running fn deploy](../Images/7ea01518e5cd45d393e12f9c4219d5cb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eMPVNBhX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AU9UHXdQlrCScQr6wLn4nsg.png)

这意味着在 Fn 服务器上，可以在/myapp/hello 路径下访问该函数。应用程序名称用于对功能进行逻辑分组。要查看 Fn 服务器上定义的所有路由的完整列表，请运行以下命令:

```
# List all routes for 'myapp'
$ fn routes list myapp
path   image        endpoint
/hello hello:0.0.3  localhost:8080/r/myapp/hello 
```

Enter fullscreen mode Exit fullscreen mode

最后，如果您访问端点，您将得到如下的“Hello World”消息:

```
$ curl localhost:8080/r/myapp/hello
{"message":"Hello World"} 
```

Enter fullscreen mode Exit fullscreen mode

## 分组功能

要将这些功能组合在一起，您可以使用应用程序名称结构——这允许您将不同的路线逻辑地组合在一起(例如，`greeter-app`可以有名为`/hello`和`/goodbye`的路线)。

在这种情况下，`greeter-app`也可以是你的函数所在的文件夹名，子文件夹`/hello`和`/goodbye`将包含实际的函数。您还可以在 app 根文件夹中定义`app.yaml`文件，以便能够用一个命令部署所有功能。

按照下面的步骤创建一个带有问候和再见功能的欢迎应用程序:

```
# Create the greeter-app folder
mkdir greeter-app && cd greeter-app
# Create app.yaml that defines the app name
echo "name: greeter-app" > app.yaml
# Create a hello function in /hello subfolder
fn init --runtime go hello
# Create a goodbye function in /goodbye subfolder
fn init --runtime go goodbye 
```

Enter fullscreen mode Exit fullscreen mode

完成所有这些设置并将`app.yaml`放在根文件夹中后，您可以使用这个命令将所有功能部署到本地 Fn 服务器:

```
fn deploy --all --local 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令创建了以下应用程序和端点:

```
$ fn routes l greeter-app
path     image         endpoint
/goodbye goodbye:0.0.2 localhost:8080/r/greeter-app/goodbye
/hello   hello:0.0.2   localhost:8080/r/greeter-app/hello 
```

Enter fullscreen mode Exit fullscreen mode

你也可以通过从应用程序的根文件夹
运行`fn init`命令来创建一个位于应用程序根目录下的函数

```
fn init --runtime node 
```

Enter fullscreen mode Exit fullscreen mode

现在我们在`/greeter-app`逻辑组下有三个函数:

```
$ fn routes l greeter-app
path     image             endpoint
/        greeter-app:0.0.2 localhost:8080/r/greeter-app
/goodbye goodbye:0.0.3     localhost:8080/r/greeter-app/goodbye
/hello   hello:0.0.3       localhost:8080/r/greeter-app/hello 
```

Enter fullscreen mode Exit fullscreen mode

# 启用用户界面

如果你更喜欢 UI 与 Fn 交互——这里也有适合你的。假设您有本地运行的 Fn 服务器，您可以像这样启动 UI:

```
docker run --rm -it --link fnserver:api -p 4000:4000 -e "FN_API_URL=http://api:8080" fnproject/ui 
```

Enter fullscreen mode Exit fullscreen mode

当图像被下载并且容器执行时，您将能够在`http://localhost:4000`上访问 UI。

[![fn project UI](../Images/85bf5b5f6893297a8175ecb180911732.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bejIdsJ5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1200/1%2AenzDJZLDo-7DB85i8sBSGQ.png)

# 延伸 Fn

有几个不同的选项可供您扩展 Fn 服务器。所有选项都要求您重新构建 Fn 服务器，因为您必须导入您的扩展——您可以使用 build-server CLI 命令和`ext.yaml`文件来构建带有您的扩展的 Fn 服务器的新映像，或者您可以派生&克隆 Fn repo 并在`cmd/fnserver/main.go`文件中引用您的扩展，然后重新构建代码并运行它。

对于开发，最快的方法是克隆 Fn repo 并在那里创建和注册您的扩展。如果您使用的是`build-server`命令，这可能需要更长的时间，因为该命令将在每次调用时重新构建 Fn 服务器映像。注意，在这两种情况下，每次都必须构建 Fn 服务器，但是直接构建要比重建 Docker 映像快得多。

Fn 服务器上有三个扩展点:监听器、中间件、自定义 API 端点。请继续阅读，以获得对每个扩展点的更详细的描述，并查看本文后面的一些示例。

## 听众

您可以监听各种 API 事件并对它们做出响应。目前有两种类型的监听器:应用程序和呼叫。我想路线听众应该很快也会来…

在应用程序监听器中，您可以响应以下事件:

```
BeforeAppCreate
AfterAppCreate
BeforeAppUpdate
AfterAppUpdate
BeforeAppDelete
AfterAppDelete 
```

Enter fullscreen mode Exit fullscreen mode

这些事件在呼叫监听器中可用:

```
BeforeCall
AfterCall 
```

Enter fullscreen mode Exit fullscreen mode

## 中间件

使用中间件，您可以为每个到达服务器的 API 请求添加所需的功能。在这个中间件中，您可以决定是取消请求还是调用链中的下一个中间件。中间件的一个简单例子是检查令牌头的认证中间件，或者为每个请求记录某些事情的中间件。

## 自定义 API 端点

自定义 API 端点允许您向 Fn 服务器添加新端点。例如，您可以添加一个自定义 API 端点来处理对自定义路由(如`/mycustomroute`)的请求，或者用路由`/v1/apps/:app_name/mycustomhandler`或`/v1/apps/:app_name/routes/:route_name/mycustomhandler`定义一个端点。

例如，可以在应用程序和路线上实现一个名为`stats` (so，`/v1/apps/:app_name/stats and /v1/app:app_name/routes/:route_name/stats`)的自定义端点，当这些端点被调用时，您可以返回应用程序或路线的一些基本统计数据。

# 示例:使用呼叫监听器的呼叫计数器分机

我写了一个简单的扩展，计算一个应用程序被调用的次数，并把这个数字输出到 stdout。您可以在这里获得该扩展的源代码。

扩展实现分为两个文件:`callcount.go`和`calllistener.go`。

在第一个文件(`callcount.go`)中，我注册了扩展并像这样设置了调用监听器: