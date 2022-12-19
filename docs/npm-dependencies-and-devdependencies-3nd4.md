# npm 依赖项和开发依赖项

> 原文：<https://dev.to/flaviocopes/npm-dependencies-and-devdependencies-3nd4>

当你使用`npm install <package-name>`安装一个 npm 包时，你是把它作为一个**依赖**来安装的。

这个包会自动列在 [package.json 文件](https://flaviocopes.com/package-json/)中的`dependencies`列表下(从 npm 5 开始:在您必须手动指定`--save`之前)。

当您添加`-D`标志或`--save-dev`时，您正在将它作为开发依赖项安装，这将它添加到`devDependencies`列表中。

开发依赖项旨在作为开发专用包，在生产中不需要。例如测试包、 [webpack](https://flaviocopes.com/webpack/) 或者 [Babel](https://flaviocopes.com/babel/) 。

当您进入生产环境时，如果您键入`npm install`并且文件夹包含一个`package.json`文件，它们就会被安装，因为 npm 认为这是一个开发部署。

您需要设置`--production`标志(`npm install --production`)来避免安装那些开发依赖项。