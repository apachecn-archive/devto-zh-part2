# 读取 Chrome 扩展中的文件

> 原文：<https://dev.to/aussieguy/reading-files-in-a-chrome-extension--2c03>

通常，人们希望 Chrome 扩展与需要阅读的文件捆绑在一起。这些文件可能包含帮助扩展功能的数据或配置信息。这个简短的指南将告诉你如何设置你的 Chrome 扩展来读取文件。

## 将文件路径添加到 manifest.json

首先，您必须将文件路径添加到 manifest.json 文件中的 web_accessible_resources 属性。文件路径相对于扩展的根目录(manifest.json 所在的位置)。例如，如果我想包含一个名为`info.json`的文件，它位于一个数据文件夹中，它看起来像:

```
"web_accessible_resources":  [  "data/info.json"  ] 
```

Enter fullscreen mode Exit fullscreen mode

一个很酷的特性是这些路径支持通配符。例如:

```
"web_accessible_resources":  [  "data/*.json"  ] 
```

Enter fullscreen mode Exit fullscreen mode

将允许访问数据文件夹中的任何 json 文件。

## 从文件中读取

下一步是从文件中读取数据。为此，我们需要获取文件的
URL 并向它发出请求。

要获得文件的 URL，我们可以使用`chrome.runtime.getURL('path/to/file')`。

然后，我们向 URL 发出 GET 请求。在这个例子中，我们将使用 ES6 特性[获取](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)，但是像`XmlHttpRequest`这样的方法也可以。

```
const url = chrome.runtime.getURL('path/to/file');

fetch(url)
    .then((response) => response.json()) //assuming file contains json
    .then((json) => doSomething(json)); 
```

Enter fullscreen mode Exit fullscreen mode

我们做到了！

简单地重复一下步骤:

1.  将文件路径添加到`manifest.json`文件中的`web_accessible_resources`属性
2.  使用`chrome.runtime.getURL('path/to/file')`获取文件的 URL
3.  向 URL 发出 GET 请求