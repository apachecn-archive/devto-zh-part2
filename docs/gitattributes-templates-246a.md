# 。gitattributes 模板

> 原文：<https://dev.to/jozefizso/gitattributes-templates-246a>

[gitattributes.io](https://gitattributes.io) 是一个从模板生成`.gitattribute`文件的服务。这项服务的灵感来自于 [gitignore.io](https://www.gitignore.io) ，在那里你可以快速引导你的`.gitignore`文件。

在 web 界面上选择模板，或者使用非常简单的 API 列出模板名称并生成新的`.gitattribute`文件内容。

要为您的 web 项目生成`.gitattributes`文件，只需调用:

```
https://gitattributes.io/api/web 
```

Enter fullscreen mode Exit fullscreen mode

在 Visual Studio 中从事 C++项目？用逗号分隔多个模板名称:

```
https://gitattributes.io/api/c++,visualstudio 
```

Enter fullscreen mode Exit fullscreen mode

所有模板名称的列表可在`api/list`端点处获得:

```
https://gitattributes.io/api/list 
```

Enter fullscreen mode Exit fullscreen mode

这项服务是由来自 **alexkaratarakis** 的项目实现的，他用`.gitattributes`模板创建了最初的[alexkaratarakis/git attributes](https://github.com/alexkaratarakis/gitattributes)库。您可以在[github.com/gitattributes](https://github.com/gitattributes)参与 **gitattributes.io** 项目