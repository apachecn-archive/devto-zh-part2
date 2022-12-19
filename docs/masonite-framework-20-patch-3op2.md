# Masonite Framework 2.0 补丁

> 原文：<https://dev.to/masonite/masonite-framework-20-patch-3op2>

# 补丁和挑战

Masonite 是一个非常年轻的框架，有一个非常年轻的社区。在为 Masonite 构建一个非常棒的仪表板包时，我意识到我正在开发的包与 Masonite 1.6 的不一样。更具体地说，我可以通过服务提供商向 Whitenoise 添加静态文件，但在 2.0 中却不行。

那些熟悉 Laravel 和服务提供商的人，我向你们提出一个挑战。这个挑战是要找出下面这段代码有什么问题。

答案就在它下面所以不要巅峰！

```
for provider in container.make('ProvidersConfig').PROVIDERS:
    located_provider = provider()
    located_provider.load_app(container).register()
    if located_provider.wsgi:
        container.make('WSGIProviders').append(located_provider)
    else:
        container.resolve(located_provider.boot)
        container.make('Providers').append(located_provider) 
```

不确定是什么？我可以给你一个提示。

```
for provider in container.make('ProvidersConfig').PROVIDERS:
    located_provider = provider()
    located_provider.load_app(container).register() # <-- this line
    if located_provider.wsgi:
        container.make('WSGIProviders').append(located_provider)
    else:
        container.resolve(located_provider.boot) # <-- and this line
        container.make('Providers').append(located_provider) 
```

找到了吗？好吧，我就告诉你。这个问题是，在所有服务提供商启动之前，并非所有服务提供商都已注册。

# 补丁

解决方法是将代码重组如下:

```
for provider in container.make('ProvidersConfig').PROVIDERS:
    located_provider = provider()
    located_provider.load_app(container).register()
    if located_provider.wsgi:
        container.make('WSGIProviders').append(located_provider)
    else:
        container.make('Providers').append(located_provider)

for provider in container.make('Providers'):
    container.resolve(provider.boot) 
```

您可以在 Masonite 2.0 应用程序中对此进行修补。

# 我的想法和可能的解决方案

所以这个框架还很年轻，我们会不时地犯这样的错误，直到这个框架变得非常稳定。这个问题有几种解决方法。

## 解决方案 1 -新的主要版本

这需要每个人将框架从 2.0 升级到 2.1。这需要将文档更新到 2.1 版(没有任何文档更改)，对于没有得到消息的人来说，这意味着很多困惑

所以这个解决方案并不可行。

## 解决方案 2 -为所有新应用打补丁

我们可以简单地在 Masonite 的所有未来应用程序中进行修复，但问题现在会出现，现在技术上有 2 个不同版本的 Masonite 2.0。1 个版本有补丁，1 个版本没有。这将使包装变得困难。

## 胜利者

我们将为这个问题选择的解决方案是，我们将发布 Masonite 的下一个次要版本。在这个新的 2.0.x 版本中将有一个新的函数，如下所示:

```
def _check_patch(self):
    patched = False

    with open('wsgi.py', 'r') as file:
        # read a list of lines into data
        data = file.readlines()

    # change the line that starts with KEY=
    for line_number, line in enumerate(data):
        if line.startswith("for provider in container.make('Providers'):"):
            patched = True
            break

    if not patched:
        print('\033[93mWARNING: {}\033[0m'.format('Your Application Is Not Patched!')) 
```

我们可以将它放在 serve 命令中:

```
def handle(self):
    self._check_patch() # <-- New Line Here 
    if self.option('reload'):
        self._run_with_reloader(extra_files=[".env"])

    else:
        self._run_application() 
```

这将做的是扫描你的`wsgi.py`文件中的补丁，如果它没有检测到它，那么当你运行`craft serve`时它将显示错误。

一旦您进行了更改，Masonite 将不再显示警告消息。

* * *

同样，这个框架还很年轻(但是很成熟),代码中可能会有这样的错误。该框架的很少部分在应用程序本身中，因此大多数修复都是通过升级 Masonite 本身来完成的。创建的应用程序中包含框架的一小部分，可能会出现少量错误。

我们需要作为一个社区一起工作来发现这些问题，并使 Masonite 成为一个我们都能享受的更好的框架:)