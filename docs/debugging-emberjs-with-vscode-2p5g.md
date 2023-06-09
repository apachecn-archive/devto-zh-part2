# 用 VScode 调试 EmberJS

> 原文：<https://dev.to/michalbryxi/debugging-emberjs-with-vscode-2p5g>

我找到了很多描述这个主题的参考资料，但是我花了一段时间才弄明白如何将它应用到我的设置中。所以这将(希望)帮助我下次需要它的时候记得。所有路径都相对于您的工作空间。

1.  首先，您需要在 VScode 中为 Chrome 扩展使用[调试器。](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome)
2.  现在，您需要为 VScode 调试器创建一个配置:

```
//  .vscode/launch.json  {  //  Use  IntelliSense  to  learn  about  possible  attributes.  //  Hover  to  view  descriptions  of  existing  attributes.  //  For  more  information,  visit:  https://go.microsoft.com/fwlink/?linkid=830387  "version":  "0.2.0",  "configurations":  [  {  "type":  "chrome",  "request":  "launch",  "name":  "EmberJS",  "port":  9222,  "runtimeArgs":  [  "--remote-debugging-port=9222"  ],  "url":  "http://localhost:4200",  "sourceMapPathOverrides":  {  "admin/*":  "${workspaceFolder}/admin/app/*"  }  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

让我们破译个别线路:

```
"type": "chrome", 
```

Enter fullscreen mode Exit fullscreen mode

VScode 可以有多个调试配置。而且这个配置会使用 Chrome 扩展的调试器。

```
"request": "launch", 
```

Enter fullscreen mode Exit fullscreen mode

在 VScode 中启动调试器后，扩展将启动新的 Chrome 实例。

```
"name": "EmberJS", 
```

Enter fullscreen mode Exit fullscreen mode

这个配置将以这个名字出现在 Chrome 调试器中。

```
"port": 9222, 
```

Enter fullscreen mode Exit fullscreen mode

Chrome 调试器将连接到该端口。

```
"runtimeArgs": [ "--remote-debugging-port=9222" ], 
```

Enter fullscreen mode Exit fullscreen mode

Chrome 将在启用远程调试端口的情况下启动。您需要填写与在`port`配置中使用的相同的数字。

```
"url": "http://localhost:4200" 
```

Enter fullscreen mode Exit fullscreen mode

Chrome 将会在*发布*，并在标签中加载这个 URL。我的例子中的值是默认 URL `ember server`运行的[。](https://ember-cli.com/user-guide/) 

```
"sourceMapPathOverrides": { 
```

Enter fullscreen mode Exit fullscreen mode

浏览器使用源地图能够将缩小/编译的 JS 翻译回人类可读的形式。但是在复杂的设置中，像我这样的源文件有错误的路径。这个散列将提供一种方法来纠正这些:

```
"admin/*": "${workspaceFolder}/admin/app/*" 
```

Enter fullscreen mode Exit fullscreen mode

*   左侧:这是您的`config/environment.js`文件中`ENV.modulePrefix`变量的值(在我的例子中是`admin`)。最后加上斜杠和星号来覆盖所有子目录。
*   右侧:
    *   `${workspaceFolder}` -带有工作区文件夹路径的变量。
    *   `admin` -是您的工作空间中的子文件夹，它是您的项目的根。无论`package.json`文件在哪里。
    *   `app` -是您的应用程序的实际源代码子目录。文件夹`app`是模块统一之前的项目的典型文件夹。[模块统一](https://github.com/emberjs/rfcs/blob/master/text/0143-module-unification.md)登陆后，大概会是`src`。

最后一个要素是启用源地图:

```
// admin/ember-cli-build.js

module.exports = function(defaults) {
  let app = new EmberApp(defaults, {
    babel: {
      sourceMaps: 'inline'
    }
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 示例截图

<figure>

[![VScode debugging in action](img/1583c8c214729e60609d75cba4f04574.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gQ7403KK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7krvrs464f3qhj3yvg8h.png)

<figcaption>VScode debugging in action</figcaption>

</figure>

## 来源

*   [https://medium . com/@ minhdn/debug-ember-app-with-vs code-5 F4 FDE 511 f 9 f](https://medium.com/@minhdn/debug-ember-app-with-vscode-5f4fde511f9f)
*   [https://gist . github . com/jrock 2004/32 f 0353 e 176294 060 ed 1 D4 fa 9 f 56d 646](https://gist.github.com/jrock2004/32f0353e176294060ed1d4fa9f56d646)
*   [https://medium . com/front-end-hacking/solve-breakpoint-ignored-with-visual-studio-code-vs code-1-19-chrome-debugger-6d 484 c 88 b 829](https://medium.com/front-end-hacking/solve-breakpoint-ignored-with-visual-studio-code-vscode-1-19-chrome-debugger-6d484c88b829)