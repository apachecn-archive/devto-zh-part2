# 卸载 npm 软件包

> 原文：<https://dev.to/flaviocopes/uninstalling-npm-packages-46gl>

要卸载你之前在本地安装的**软件包**(使用`node_modules`文件夹中的`npm install <package-name>`，运行

```
npm uninstall <package-name> 
```

Enter fullscreen mode Exit fullscreen mode

从项目根文件夹(包含 node_modules 文件夹的文件夹)。

使用`-S`标志或`--save`，该操作也将删除 [`package.json`文件](https://flaviocopes.com/package-json/)中的引用。

如果这个包是一个开发依赖项，列在`package.json`文件的 devDependencies 中，那么您必须使用`-D` / `--save-dev`标志将它从文件:
中移除

```
npm uninstall -S <package-name>
npm uninstall -D <package-name> 
```

Enter fullscreen mode Exit fullscreen mode

如果包是全局安装**，需要添加`-g` / `--global`标志:** 

```
npm uninstall -g <package-name> 
```

Enter fullscreen mode Exit fullscreen mode

例如:

```
npm uninstall -g webpack 
```

Enter fullscreen mode Exit fullscreen mode

您可以在系统上的任何地方运行这个命令，因为您当前所在的文件夹并不重要。**