# GIT 命令:用 ElectronJS 和 ReactJS 构建的跨平台桌面应用。

> 原文：<https://dev.to/mariorodeghiero/git-cheatsheet-2imn>

## 截图

[![Git Comannds](img/4a86737e891ec8af56a44d10cf33580d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RyBfsYh9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dmqyxw1aobscgqpp0fs4.png)

# Git 命令

GIT Commands 是一个桌面应用程序，使用 ElectronJS 和 ReactJS 构建，是跨平台的，提供了对主要 GIT 命令的快速访问，并提供了到文档的直接链接。

## 先决条件

*   **要求节点版本> = 7，npm 版本> = 4。**
*   **如果您对本项目有安装或编译问题，请参见[我们的调试指南](https://github.com/chentsulin/electron-react-boilerplate/issues/400)**

## 安装

首先，通过 git 克隆回购:

```
git clone --depth=1 https://github.com/mariorodeghiero/git-commands.git your-project-name 
```

Enter fullscreen mode Exit fullscreen mode

然后用 yarn 安装依赖项。

```
$ cd your-project-name
$ yarn 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:如果不能使用[纱](https://github.com/yarnpkg/yarn)，运行`npm install`。

## 运行

在`dev`环境中启动应用:

```
$ npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

## 包装

为本地平台打包应用:

```
$ npm run package 
```

Enter fullscreen mode Exit fullscreen mode

要为所有平台打包应用程序:

首先，参考[多平台构建](https://www.electron.build/multi-platform-build)了解依赖关系。

然后，

```
$ npm run package-all 
```

Enter fullscreen mode Exit fullscreen mode

使用选项打包应用程序:

```
$ npm run package -- --[option] 
```

Enter fullscreen mode Exit fullscreen mode

## 提示

若要提高此应用的可用性，您可以创建键盘快捷键来快速启动。

*   对于 Mac_OS，请使用“Automator”。
*   用于 Windows 访问应用程序的“属性”。

如果你喜欢这个项目，贡献改进或给我一个 GitHub 上的星⭐️。

谢谢！