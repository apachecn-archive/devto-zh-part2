# 带 YAML 的 Python 配置文件

> 原文：<https://dev.to/tomtucka/python-config-file-with-yaml-3e31>

所以大约 4 周前我自己在谷歌上搜索这个问题，找不到一个直接的答案，所以我打算和你分享一个简单的逐步解决方案。

首先，我假设你已经了解一些 python，并且已经准备好进入 venv 的项目设置。

首先让我们导入 yaml，

```
import yaml 
```

Enter fullscreen mode Exit fullscreen mode

所以现在让我们创建一个名为`load_config`
的方法

```
import yaml

def load_config(config_file): 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们也传入了`config_file`，接下来，我们将使用 python 内置的`open`以流的形式打开我们的配置文件。

```
import yaml

def load_config(config_file):
    with open(config_file, 'r') as stream: 
```

Enter fullscreen mode Exit fullscreen mode

使用 open，我们现在可以打开我们的配置文件并返回一个流。这里我们传入我们之前定义的`config_file`变量，我们也传入另一个参数，它是一串`r`基本上是告诉`open`我们想以哪种模式打开文件。现在我们可以继续加载我们的 YAML 文件。

```
def load_config(config_file):
    with open(config_file, 'r') as stream:
        try:
            return yaml.safe_load(stream)
        except yaml.YAMLError as exc:
            print(exc) 
```

Enter fullscreen mode Exit fullscreen mode

正如你在上面看到的，我们现在使用`safe_load`加载我们的 YAML 文件。这里使用`safe_load`非常重要，`safe_load`只解析基本的 YAML 标签，而`load`不解析。这也包含在一个`try except`块中，所以我们可以处理任何错误。

现在剩下要做的就是调用我们的方法来加载文件，确保在这里传递 config.yml 文件的路径。

```
config = load_config('config.yml') 
```

Enter fullscreen mode Exit fullscreen mode

这是我的第一篇博文，如果有任何反馈，我将不胜感激！:)