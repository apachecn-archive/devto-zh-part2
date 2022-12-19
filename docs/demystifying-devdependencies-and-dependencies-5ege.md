# 揭开“发展依赖”和“依赖”的神秘面纱

> 原文：<https://dev.to/moimikey/demystifying-devdependencies-and-dependencies-5ege>

如果您不熟悉的话，`devDependencies`和`dependencies`是当一个包分别作为开发依赖项或生产依赖项安装时添加到`package.json`中的两个属性。

在 npm 生态系统中，软件包是通过在文件中要求或导入它们来安装和使用的，或者作为二进制文件在命令行中运行。当一个应用程序被放入一个模块捆绑器(如 Webpack 或 Rollup)时，所有需要的依赖项都被放在一起并被捆绑(顾名思义)。您应该确保这些包存在于`dependencies`中，因为在运行时需要它们。

开发依赖项，或`devDependencies`是在开发阶段，通过在文件中需要它们或作为二进制文件运行而被消费的包。这些包仅在开发过程中是必需的，在生产构建中不是必需的。一些只在开发期间需要的包的例子是 babel 插件和预置，测试运行器和 linter 包。

或者，在生产和开发中都需要依赖。在这种情况下，可以将其添加到`dependencies`，因为`dependencies`在生产和开发中都可用。

我希望这个解释能帮助你决定下次是`--save-dev`还是`--save`那个包裹。