# 在 VS 代码中调试 Ruby

> 原文：<https://dev.to/dnamsons/ruby-debugging-in-vscode-3bkj>

尽管对于简单的调试目的来说`binding.pry`已经足够了，但我发现当我必须快速查看多个变量和对象值时，这变得既耗时又麻烦。因此，我
通过 [ruby debug ide](https://github.com/ruby-debug/ruby-debug-ide) 接口使用 vscode 的内置调试特性。下面是设置它的方法:

## 安装需要的宝石

### 为 JRuby 或 Ruby v1.8.x

```
gem install ruby-debug-ide
gem install ruby-debug-base 
```

Enter fullscreen mode Exit fullscreen mode

### 为 Ruby v1.9.x

```
gem install ruby-debug-ide
gem install ruby-debug-base19x 
```

Enter fullscreen mode Exit fullscreen mode

### 为 Ruby v2.x

```
gem install ruby-debug-ide
gem install debase 
```

Enter fullscreen mode Exit fullscreen mode

## 创建一个`launch.json`文件

如果你还没有，安装 [Ruby](https://marketplace.visualstudio.com/items?itemName=rebornix.Ruby) 扩展并重新加载编辑器。

在 vscode 中，打开调试选项卡并单击下拉菜单，然后选择`Add configuration...`选项。

[![selecting 'Add Configuration in the debug view'](img/967c6a4acc3ba4af2996de817defbbe2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wPPUk-oK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fqz1kmiwvw3bavoy4sb8.png)

从新的下拉菜单中选择`Ruby`。这将在你的当前目录下创建一个带有`launch.json`文件的`.vscode`文件夹。

[![Selecting Ruby as the configuration](img/fa3c7674b28831bacdc1bbb8d29db4f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fJq_3Ibs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rxljn5dyck0rbeb1kpq6.png)

## 启动调试会话

现在，您可以在终端中运行`rdebug`界面:

`rdebug-ide --host 0.0.0.0 --port 1234 --dispatcher-port 26162 /path/to/the/file.rb`
(如果你需要改变端口，记得也在`launch.json`中指定)

如果你试图调试一个 rails 应用程序，你应该用`server`命令指定 Rails 脚本在你的项目中的路径。该脚本通常位于项目的`bin`目录中，在这种情况下，您可以将它写成`bin/rails server`。

接口在终端中运行后，您可以启动调试会话。在调试选项卡中，从下拉菜单中选择`Listen for rdebug-ide`。单击开始按钮。

[![selecting rdebug](img/b403e9e9fd0a5cbfa3486d2358d86069.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qeCbIL5O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q825wie7mvmldthbofy1.png)

您的调试会话应该正在运行。现在，您可以点击设置的断点，并使用调试控制台来帮助分析您的 Ruby 代码。

这里有一个例子:

[![debugging preview](img/154fee0355b9d3edce7497c162909869.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ebyPjrDg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9gj338y3o8wsy3xcy268.png)

调试愉快！