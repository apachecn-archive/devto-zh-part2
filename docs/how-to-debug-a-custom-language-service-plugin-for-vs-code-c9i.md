# 如何调试 VS 代码的定制语言服务插件

> 原文：<https://dev.to/justinribeiro/how-to-debug-a-custom-language-service-plugin-for-vs-code-c9i>

在下一个项目开始之前，我手上还有一点时间，我确信我可以在[typescript-lit-html-plugin](https://github.com/Microsoft/typescript-lit-html-plugin)中实现一些功能，比如 CSS quickfix 和 intellisense。这将需要调试语言服务，鉴于问题单标题[“在 VS 代码中调试语言服务”文档很奇怪](https://github.com/Microsoft/TypeScript/issues/19254)，我对流畅的体验没有最高的期望，但实际上我很少打嗝就启动并运行了。

让我们走几步，好吗？

1.  为您的平台下载稳定的 VS 代码。您将需要它来调试您的语言服务代码。
2.  克隆 VS 代码仓库。您将需要它来运行语言服务器，以便我们可以从稳定的 VS 代码中调试它。

```
$ git clone git@github.com:Microsoft/vscode.git 
```

1.  是时候建 VS Cod 了。构建链需要一个工具。有些你可能已经有了(git，python 2.7x，yarn，node 8)，有些你可能没有(C/C++编译器工具链，libsecret)。我运行的是 Ubuntu，所以我从 [wiki](https://github.com/Microsoft/vscode/wiki/How-to-Contribute) 开始进行了简化:

```
$ apt install build-essential libx11-dev libxkbfile-dev libsecret-1-dev $ cd vscode $ yarn $ yarn run watch # open new terminal $ ./scripts/code.sh 
```

此时，您应该有一个正在运行的 VS 代码版本了！如果没有，请检查构建错误。

1.  克隆 TypeScript repo。

```
$ git clone git@github.com:Microsoft/TypeScript.git 
```

1.  是时候构建 TypeScript 了。

```
$ yarn global add jake $ cd TypeScript $ yarn $ jake local 
```

1.  你做得很好！现在，在您构建的 VS 代码实例中，编辑用户设置以指向您刚刚构建的 TypeScript 版本:

```
{ "typescript.tsdk": "/path/to/TypeScript-build/built/local" } 
```

1.  一旦用 TypeScript 服务器的路径更新了用户设置，关闭构建的 VS 代码副本，返回到运行它的命令行。现在，我们将设置一个名为`TSS_DEBUG`的 env 变量，并重启我们构建的 VS 代码。

```
$ export TSS\_DEBUG = 5888 $ ./scripts/code.sh 
```

1.  你已经很接近了。在稳定 VS 代码中，打开您的语言服务器项目。您需要一个`launch.json`,它可以连接到您构建的 TypeScript 服务器，该服务器现在运行在您构建的 VS 代码版本中。所以我们加了一条:

```
{ "version": "0.1.0", "configurations": [{ "name": "Attach to TypeScript Server", "type": "node", "request": "launch", "protocol": "inspector", "port": 5888, "sourceMaps": true, "outFiles": ["/path/to/TypeScript-build/built/local"], "runtimeArgs": ["--inspect=5888"] } ] } 
```

这将让我们现在绑定和调试！

1.  在您构建的 VS 代码版本中，打开一个包含 typescript 文件或其他要调试的代码的文件夹。现在可以设置断点，通过 Debug [F5]连接到 TypeScript 服务器，并遍历代码。
2.  注意，在`typescript-lit-html-plugin`的例子中，我告诉 VS 代码应该通过在`tsconfig.json`中定义插件来使用它:

```
{ "compilerOptions": { "noImplicitAny": true, "plugins": [{ "name": "/work/src/typescript-lit-html-plugin/lib" },] } } 
```

维奥拉。我们已经开始调试了。

[![Sample of debugging a language service plugin in VS Code](../Images/67357079d3fe22b2088ca0267c19cc33.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kgbBa9sR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/jdr-public-imgs/blog/ss-2018-07-03-800-lang-server-debug.png)

相当直接。要了解更多信息，请查看[文档](https://github.com/Microsoft/TypeScript/wiki/Debugging-Language-Service-in-VS-Code)，它为不同的调试场景提供了一些额外的上下文和方法。