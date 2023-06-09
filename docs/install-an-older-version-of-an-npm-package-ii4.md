# 安装旧版本的 npm 软件包

> 原文：<https://dev.to/flaviocopes/install-an-older-version-of-an-npm-package-ii4>

您可以使用`@`语法:
安装旧版本的 npm 软件包

```
npm install <package>@<version> 
```

Enter fullscreen mode Exit fullscreen mode

示例:

```
npm install cowsay 
```

Enter fullscreen mode Exit fullscreen mode

安装版本 1.3.1(在撰写本文时)。

使用
安装 1.2.0 版

```
npm install cowsay@1.2.0 
```

Enter fullscreen mode Exit fullscreen mode

对全局包也可以这样做:

```
npm install -g webpack@4.16.4 
```

Enter fullscreen mode Exit fullscreen mode

您可能还对列出软件包的所有早期版本感兴趣。可以用`npm view <package> versions` :
来做

```
❯ npm view cowsay versions

[ '1.0.0',
  '1.0.1',
  '1.0.2',
  '1.0.3',
  '1.1.0',
  '1.1.1',
  '1.1.2',
  '1.1.3',
  '1.1.4',
  '1.1.5',
  '1.1.6',
  '1.1.7',
  '1.1.8',
  '1.1.9',
  '1.2.0',
  '1.2.1',
  '1.3.0',
  '1.3.1' ] 
```

Enter fullscreen mode Exit fullscreen mode