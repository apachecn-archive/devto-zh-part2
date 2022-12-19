# npm 全局或本地包

> 原文：<https://dev.to/flaviocopes/npm-global-or-local-packages-10l2>

本地包和全局包的主要区别在于:

*   **本地包**安装在运行`npm install <package-name>`的目录下，放在该目录下的`node_modules`文件夹中
*   **全局包**都放在你系统的一个地方(具体位置取决于你的设置)，不管你在哪里运行`npm install -g <package-name>`

在您的代码中，它们都以相同的方式被要求:

```
require('package-name') 
```

Enter fullscreen mode Exit fullscreen mode

那么什么时候应该以这种或那种方式安装呢？

一般来说，**所有的包都应该本地安装**。

这确保了你可以在你的电脑里有几十个应用程序，如果需要的话，每个应用程序都运行不同版本的软件包。

更新一个全局包会使你的所有项目都使用新版本，正如你所想象的，这可能会导致维护方面的噩梦，因为一些包可能会破坏与其他依赖项的兼容性，等等。

所有的项目都有自己的本地版本的包，即使这看起来像是一种资源浪费，但与可能的负面后果相比，这是微不足道的。

当一个包**提供了一个可以从 shell (CLI)运行的可执行命令时，它应该被全局安装**,并且可以跨项目重用。

您也可以在本地安装可执行命令，并使用 [npx](https://dev.to/flaviocopes/npx-4g1p-temp-slug-4919515) 运行它们，但是有些包最好全局安装。

您可能知道的流行全球软件包的典型例子有

*   `npm`
*   `create-react-app`
*   `vue-cli`
*   `grunt-cli`
*   `mocha`
*   `react-native-cli`
*   `gatsby-cli`
*   `forever`
*   `nodemon`

您的系统上可能已经全局安装了一些软件包。您可以通过运行
来查看它们

```
npm list -g --depth 0 
```

Enter fullscreen mode Exit fullscreen mode

在你的命令行上。