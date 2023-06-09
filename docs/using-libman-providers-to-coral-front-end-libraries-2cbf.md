# 使用 Libman 提供程序来访问前端库

> 原文：<https://dev.to/catriname/using-libman-providers-to-coral-front-end-libraries-2cbf>

我学会使用的第一个包管理系统是 Bower。对于我们在一起的短暂爱情来说，这很棒，但它不再被支持，我厌倦了学习使用它的替代品。幸运的是，我还没有和利伯曼在一起。

libman(Library Manager)的简单 JSON 方法带来了前端依赖，这是我最容易快速实现的，也是对 Visual Studio 的一个很好的补充。然而，我可能实现得太快了，并且**忽略了一个强大的配置设置:提供者。**

## 库不在 Libman 上！

这是我遇到麻烦的第一个迹象，但实际发生的是:我没有指定正确的**提供者**。

我指定的 defaultProvider 是 cdnjs，就像那里的许多例子一样，尽管它有许多前端库， *cdnjs 当然没有所有的库。*

这里有一个简单的例子来说明 Libman 是如何成功地使用 cdnjs 引入 jQuery 的:

```
{  "version":  "1.0",  "defaultProvider":  "cdnjs",  "libraries":  [  {  "library":  "jquery@3.3.1",  "destination":  "wwwroot/lib/jquery",  "files":  [  "jquery.min.js"  ]  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

## Libman 提供者选项

除了 CDNJS，Libman 还支持另外两个提供商。以下是完整的列表:

*   cdnjs(默认提供商)
*   文件系统(本地文件)
*   un pkg(NPM 一切的 CDN)

这非常有效，如果一个依赖没有解决，我添加了一个提供者行，更改它的提供者:

```
{  "provider":  "unpkg",  "library":  "bulma-checkradio",  "destination":  "wwwroot/lib/bulma",  "files":  []  } 
```

Enter fullscreen mode Exit fullscreen mode

## 无法解析 UNPKG 库

但是，当使用 unpkg 作为提供者时，我无法在 Intellisense 中列出任何文件。我根本没有结果。我也没有图书馆的版本。

所以，我做了一点小小的改动，使用了第三个选项:文件系统。

## 使用本地文件

我有 2 个特定的包会显示在 unpkg 上，但是返回了以下错误:

“unpkg”提供程序无法解析“bulma-checkradio”库
“UNP kg”提供程序无法解析“datatables-bulma”库
还原操作已完成，但出现错误

所以，我从 NPM 进口:

```
{  "name":  "myproject",  "version":  "1.0.0",  "devDependencies":  {  "bulma-checkradio":  "2.1.0",  "datatables-bulma":  "^1.0.1"  },  "-vs-binding":  {  "BeforeBuild":  [  "update"  ]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

但是我不想将 my _node_modules 文件夹添加到项目中，这样 NPM 包就不能被放到视图中。所以，我使用 Libman 将 _node_modules 文件夹中的文件复制到我的 wwwroot/lib 文件夹:

```
{  "provider":  "filesystem",  "library":  "node_modules/bulma-checkradio/dist/css/",  "destination":  "wwwroot/lib/bulma/bulma-checkradio/css",  "files":  [  "bulma-checkradio.min.css"  ]  }, 
```

Enter fullscreen mode Exit fullscreen mode

它的工作，只是希望这是一个有点顺利。