# 带有氖和云的 AWS Lambda 上的铁锈 9

> 原文：<https://dev.to/kayis/rust-on-aws-lambda-with-neon--cloud9--4el7>

*[封面图片由 Ekin Arabacioglu 在 Flickr 上](https://www.flickr.com/photos/ekinarabaci/)*

目前我正努力成为一名全栈开发者。经过多年的前端开发，这意味着获得一些后端技能。所以我的想法是直接跳上这辆宣传列车，它就是*无服务器*！

当我在读一本关于无服务器架构的书时，我想尝试一下 AWS Lambda。什么样的小东西会适合写博客呢？对，用某种新奇的语言编写软件。[上次我尝试理智](https://dev.to/kayis/10-easy-steps-to-create-aws-lambda-functions-with-the-serverless-framework--reason-in-aws-cloud9-8d1)。今天我试了铁锈！

这太容易了！

## 为什么？

那么你为什么要在 Lambda 上运行 Rust 呢？安全性能！

当我还是个小程序员的时候，我工作的第一家公司是做 PHP 软件的。当他们遇到性能瓶颈时，他们会用 C 语言重写一个模块，然后就完事了。

我的想法是:为什么不用 JavaScript 和 Rust 来做呢？

用 JS 写所有东西，然后，如果你发现一些计算密集型代码，用 Rust 重写，从长远来看可以节省一些钱。有了 Rust，你可以获得接近 C 的性能，还有一些很好的内存安全特性，在运行时不会影响你。

## 什么？

Rust 是一种系统编程语言，运行速度快得惊人，能防止 segfaults，并保证线程安全

AWS Cloud9 是一个运行在浏览器内部的 IDE，使用 EC2 实例作为后端。当您启动一个 shell 时，它将在 EC2 上的云中运行。好的一面是，它预装了 Node.js 和 AWS CLI，因此您只需创建一个 Cloud9 环境就可以了。没有配置，什么都没有。

[Neon](https://www.neon-bindings.com/) 是一个允许你用 Rust 编写 native/binary Node.js 模块的项目。您只需编写 Rust 代码，Neon 将在 Rust 工具链的帮助下构建它，并将其提供给 Node.js JavaScript 模块。

## 如何？

我假设您已经注册了 AWS，并且知道如何创建 Lambda 函数和配置 Cloud9。如果没有，你可以在我的[其他文章](https://dev.to/kayis/10-easy-steps-to-create-aws-lambda-functions-with-the-serverless-framework--reason-in-aws-cloud9-8d1)中读到。第 1-5 步应该可以完成任务。对于这一点来说，`t2.micro`实例是可以的，对于本教程来说，不需要将 Cloud9 切换到`t2.small`。

### 1。创建 Lambda 函数

第一步是创建一个 Lambda 函数来执行我们的代码

*   打开 AWS 控制台
*   转到 AWS Lambda
*   点击*创建功能*
*   命名为`hello-rust`
*   选择了一个可以执行 Lambdas 的角色
*   点击*创建功能*

右上角是显示的那个函数的 *ARN* ，这是后面部署需要的*。*

另外，除了*测试*按钮之外，在右上角、左侧创建一个*测试事件*。

*   命名为`Test`
*   将 JSON 替换为`{}`
*   点击*创建*
*   点击*测试*按钮
*   打开出现的绿色框中的*细节*

返回的结果应该类似于`"Hello from Lambda"`

接下来，将*功能代码*，并将*处理程序* `index.handler`替换为`lib/index.handler`。(霓虹灯创建`index.js`的默认位置)

### 2。给云装上铁锈和霓虹 9

现在我们需要构建工具来构建 Rust 代码。所以打开你的 Cloud9 环境，通过 shell 安装它们。

对于锈蚀安装，使用

```
curl https://sh.rustup.rs -sSf | sh 
```

Enter fullscreen mode Exit fullscreen mode

Neon 通过 npm 安装:

```
npm i --global neon-cli 
```

Enter fullscreen mode Exit fullscreen mode

### 3。创建一个霓虹灯项目

接下来，在 Neon CLI 的帮助下获得一个项目设置。

```
neon new hello-rust
cd hello-rust
npm install
node . 
```

Enter fullscreen mode Exit fullscreen mode

这将把`hello node`记录到控制台中。

*   打开`hello-rust/native/src/lib.rs`
*   将`"hello node"`字符串更改为`"Hello from Rust"`并保存
*   运行`neon build && node .`

现在它应该输出新的字符串。

### 4。转换为 Lambda 函数

创建的 CLI 项目需要在 Lambda 上运行。我们需要一个`handler`来开始整个事情。

打开`lib/index.js`，将其内容替换为:

```
const native = require('../native');
exports.handler = (event, context, callback) => callback(null, native.hello()); 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们需要原生 Rust 模块。接下来，我们在每次调用处理程序时调用它，并将其结果推入回调，而不是记录它。

### 5。部署到 AWS Lambda

为此，我们需要向`package.json`添加脚本，它们将使用 AWS CLI。

把你的`package.json`里的`"scripts"`换成这个:

```
 "scripts":  {  "install":  "neon build",  "predeploy":  "zip -r deployment.zip * -x *.zip *.json *.log",  "deploy":  "aws lambda update-function-code --function-name <FUNCTION_ARN> --zip-file fileb://deployment.zip"  } 
```

Enter fullscreen mode Exit fullscreen mode

`<FUNCTION_ARN>`是来自 1 的 ARN。一步。你可以在 AWS 控制台右上角的*Lambda>Functions>hello-rust*下找到它。

如果你现在运行

```
npm run deploy 
```

Enter fullscreen mode Exit fullscreen mode

它会将你的代码和二进制文件打包成一个 ZIP 文件，并在 AWS-CLI 的帮助下上传到 AWS。压缩可以被优化，因为并不是所有的文件都是真正需要的。

### 6。运行您的函数

最后，一切就绪后，您可以通过 AWS 控制台运行您的测试事件。

只需进入*λ>功能> hello-rust* 并点击*测试*按钮，在出现的绿色方框的*细节*中应写下`"Hello from Rust"`

## 结论

在 AWS Lambda 上运行 Rust 二进制文件真的很容易。Cloud9 为这项工作进行了完美的预配置，甚至`t2.micro`实例也足以运行 Rust 工具链。

由于一切仍然是通过 JavaScript 连接的，这使得 Neon 成为只需要更多功能的完美替代。