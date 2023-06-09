# 发射装置扩展开发介绍

> 原文：<https://dev.to/brpaz/an-introduction-to-ulauncher-extension-development-1m69>

你好。在这篇文章中，我将向你介绍 [Ulauncher](https://ulauncher.io/) ，并演示为它构建一个扩展是多么简单。

我们将构建一个“货币转换”扩展，如下所示:

[![](img/edf515ee741061f2c38ef0598e50ac6c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SECtPUGZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://prod-ulauncher-ext-images.s3.amazonaws.com/github%257C184563/2018-08-18T10:36:08.309173.png)

敬请关注。

## 什么是 Ulauncher？

[![Imgur](img/4a63393715ab6f483aee673332343cc7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IyjInbca--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/lY9L2WI.png)

这是一个用于 Linux 的开源应用程序启动器。它是用 Python 2 编写的，有很多不错的特性，比如快捷方式、扩展和自定义主题。

在我看来，它是目前最好的 Linux 启动程序之一，也是我发现的最接近 MacOS 的东西。阿尔弗雷德:这简直太神奇了，我真的不明白为什么没有流行的 Linux 替代品。
作为一个 Mac 工具，Linux 用户是没有意识到还是觉得没有必要？我尝试了几个备选方案，如 Synapse、Albert、沙祖、Dext、Cerebro，每一个都有其优缺点，直到我最终找到了 Ulauncher，我把所有的马都放在了它身上。

除了核心功能之外，让 Alfred 如此出色的一个原因是它的社区和扩展数量。你可以在像 [Packal](http://www.packal.org/) 这样的网站上找到所有已经完成的东西。Ulauncher 和其他 Linux 启动程序缺乏这种受欢迎程度和社区兴趣。

这是我决定写这篇文章的原因之一。试图让更多的人知道 Ulauncher，并为核心或扩展做出贡献，希望我们最终能看到一个受欢迎的 Linux 启动器。

你可以在这里看到所有可用的扩展。

好，让我们开始写一个扩展。

## 先决条件

*   您的计算机上必须安装了 Ulauncher。你可以在[这里](https://ulauncher.io/#Download)找到安装说明。
*   需要 Python 的基础知识。

## 什么是分机？

从 [Ulauncher 扩展文档网站](http://docs.ulauncher.io/en/latest/extensions/intro.html):

> Ulauncher 扩展是 Python 2 程序，作为独立的进程与应用程序一起运行。

当您运行 Ulauncher 时，它会启动所有可用的扩展，以便它们准备好响应用户事件。当 Ulauncher 应用程序关闭或崩溃时，所有扩展都会终止。

基本上，一个扩展由一个触发扩展的“关键字”和一组事件和动作组成。

一些可用的事件包括:

*   当用户在 Ulauncher 上输入文本时触发
*   当用户在 Ulauncher 中选择一个项目时触发
*   当用户更新扩展首选项时触发。

您可以在扩展中监听这些事件，并且可以用动作来响应。
可用的操作包括:

*   CopyToClipboardAction
*   LaunchAppAction
*   OpenUrlAction
*   RunScriptAction
*   SetUserQueryAction

这个事件驱动架构做得非常好，它可以让你在扩展中做任何事情。

## 创建我们的第一个扩展

接下来，我将一步一步地指导您创建您的第一个扩展。我将向你展示开发[这个](https://ext.ulauncher.io/-/github-brpaz-ulauncher-currency)扩展的过程。这是一个简单的扩展，允许你使用 [Fixer](https://fixer.io) API 在两种货币之间转换数值。

我们将使用这个[演示扩展](https://github.com/Ulauncher/ulauncher-demo-ext)作为这个扩展的基础。

Ulauncher 在“~/”中查找扩展名。cache/ulauncher _ cache/extensions/"。你可以直接克隆到那里，但是我更喜欢把我所有的扩展放在其他地方，然后把它们符号链接到 extensions 文件夹。

出于本教程的目的，我们将直接在“扩展文件夹”中开发。

因此，打开一个终端窗口，输入:

```
cd ~/.cache/ulauncher_cache/extensions/
git clone https://github.com/Ulauncher/ulauncher-demo-ext demo-extension 
```

Enter fullscreen mode Exit fullscreen mode

如果您正在运行 Ulauncher，请关闭它并从终端

`ulauncher -v`

运行。这将在详细模式下启动 Ulauncher，这对于开发非常有用，因为这将允许查看所有已安装的扩展的任何日志或错误。

提示:如果你安装了很多扩展，那么很难找到你的扩展的日志。您可以使用“grep”来过滤输出，如下所示:

```
ulauncher -v |& grep -A 5 "ulauncher-currency" 
```

Enter fullscreen mode Exit fullscreen mode

如果你在 Ulauncher 窗口上输入“dm ”,你应该会看到演示扩展在工作。

[![demo](img/d8afd5b9dce6da1e7a85894b5f82c54a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jmJeGvXi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/caQZHxN.png)

**注意:在你的扩展中的代码改变后，只需在你的终端中“Ctrl-C”来停止 Ulauncher 并使用“ulauncher -v”再次启动它。**

现在在编辑器中打开项目，开始开发。

在你的扩展名中有个主文件。“manifest.json”和“main.py”。清单文件是您定义的地方，您的扩展信息，将触发它的关键字，以及您希望您的用户能够配置的任何其他首选项。

因此，请更新您的清单文件，并更改关键字 preferences:
中的“name”和“default_value”

```
 {  "id":  "kw",  "type":  "keyword",  "name":  "Currency Converter",  "default_value":  "currency"  }, 
```

Enter fullscreen mode Exit fullscreen mode

“default_value”是触发您的扩展的关键字本身。“名称”是您在 Ulauncher 中搜索时将出现的值。

我们稍后将回到这个文件。

如果重新加载 Ulauncher，现在应该可以用“currency”关键字而不是“dm”关键字启动演示扩展了。

你可以在这里阅读更多关于 manifest.json [中可用选项的信息。](http://docs.ulauncher.io/en/latest/extensions/tutorial.html#manifest-json)

现在让我们打开“main.py”。这是您的扩展代码所在的位置。由于这个扩展非常简单，我们将在这个文件中编写所有的代码。对于更复杂的扩展，将代码分成多个文件或模块可能是明智的。

main.py 在顶部包含一堆导入，然后是您的扩展类，使用 init 方法:

```
def __init__(self):
        super(DemoExtension, self).__init__()
        self.subscribe(KeywordQueryEvent, KeywordQueryEventListener())
        self.subscribe(ItemEnterEvent, ItemEnterEventListener()) 
```

Enter fullscreen mode Exit fullscreen mode

这是扩展启动时调用的构造函数方法。任何初始化逻辑和事件订阅都应该放在这里。

再往下是另一个类。注册为“KeywordQueryEvent”的事件侦听器的“KeywordQueryEventListener”。

```
class KeywordQueryEventListener(EventListener):

    def on_event(self, event, extension):
        items = []
        logger.info('preferences %s' % json.dumps(extension.preferences))
        for i in range(5):
            item_name = extension.preferences['item_name']
            data = {'new_name': '%s %s was clicked' % (item_name, i)}
            items.append(ExtensionResultItem(icon='images/icon.png',
                                             name='%s %s' % (item_name, i),
                                             description='Item description %s' % i,
                                             on_enter=ExtensionCustomAction(data, keep_app_open=True)))

        return RenderResultListAction(items) 
```

Enter fullscreen mode Exit fullscreen mode

这就像是你扩展的主要入口。每次用户在 Ulauncher 中输入一个文本，在你的 extension 关键字之后就会触发这个类的“on_event”方法。“event”参数包含关于事件的数据，例如用户输入的文本。" event.get_argument()"。“扩展”参数允许你访问你在扩展类中定义的方法和属性，以及你的扩展参数。

此方法应始终返回“ExtensionResultItem”列表。“ExtensionResultItem”表示结果列表中的一个项目，并包含诸如“name”、“description”、“icon”和“on_enter”等属性，这将是一个处理项目选择的函数。

这个演示扩展还为“ItemEnterEvent”注册了一个监听器，当用户从结果列表中选择一个项目时，就会触发这个监听器。

侦听此事件，允许您自定义当用户选择一个项目时您要做的事情。你只需要实现这一点，如果你的任何默认动作，如“CopyToClipboardAction”或“OpenUrlAction”不足以满足你的需要。

Ulauncher 扩展就是这样。事件和动作。

现在让我们开始编写我们的扩展。首先，让我们用一个新的输入字段“api_key”来更新我们的清单文件。使用 Fixer API 获取汇率时，此字段是必需的。除了“keyword”首选项之外，还要删除演示扩展中的所有内容。

我们最终的首选项数组将如下所示:

```
 "preferences":  [{  "id":  "kw",  "type":  "keyword",  "name":  "Currency Converter",  "description":  "Currency Conversion",  "default_value":  "currency"  },  {  "id":  "api_key",  "type":  "input",  "name":  "Fixer API key",  "description":  "API key for Fixer.io API. Get yours <a href=\"https://fixer.io/quickstart\">here</a>",  "default_value":  ""  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在您的“main.py”中，用“CurrencyConverter”替换所有对“Demo”的引用，并删除我们不需要的 ItemEnterEvent。

我们将向 CurrencyConverterExtension 类添加一个方法，该方法将接收“金额”、“from_currency”和“to_currency”，并返回以“to_currency”转换的金额。下面是修改后的扩展类的样子:

```
class CurrencyConverterExtension(Extension):

    def __init__(self):
        super(CurrencyConverterExtension, self).__init__()
        self.subscribe(KeywordQueryEvent, KeywordQueryEventListener())

    def convert_currency(self, amount, from_currency, to_currency):
        """ Converts an amount from one currency to another """

        params = {'access_key': self.preferences['api_key'], 'symbols': '%s,%s' % (
            from_currency, to_currency)}

        r = requests.get("http://data.fixer.io/api/latest", params=params)
        response = r.json()

        if r.status_code != 200:
            raise ConversionException(
                "Error connecting to conversion service.")

        if not response['success']:
            raise ConversionException(response['error']['info'])

        # Calculate the amount from the conversion rates.
        # Fixer.io base Currency is Eur.
        rates = response['rates']

        result = (float(amount) / rates[from_currency]) * rates[to_currency]

        return str(round(result, 2)) 
```

Enter fullscreen mode Exit fullscreen mode

该方法中唯一的 Ulauncher 特定逻辑是这一行:

```
self.preferences['api_key'] 
```

Enter fullscreen mode Exit fullscreen mode

这是访问扩展首选项的更简单的方法。然而，在某些情况下，您可能不希望每次都访问它，而是希望将它存储在扩展的一个属性中。为此，您可以收听“PreferencesUpdateEvent”和“PreferencesEvent ”,这两个事件将在首次加载首选项时以及用户在扩展设置中更新它们时被触发。有关真实示例，请参见[此处的](https://github.com/brpaz/ulauncher-text-expander/blob/master/main.py)。

剩下的只是普通的 Python 代码，它使用“requests”库对 Fixer API 进行 API 调用，以获取汇率并进行转换。

我们还创建了一个自定义的“ConversionException”类，如果出现错误，我们将抛出 n。

把这个加到文件的末尾，在“如果**名** == ' **主**'”行之前:

```
class ConversionException(Exception):
    """ Exception thrown when there was an error calling the conversion API """
    pass 
```

Enter fullscreen mode Exit fullscreen mode

好的。现在我们需要实现我们的“KeywordQueryEventListener”。

我们的扩展会像这样。用户需要输入类似“20 美元兑欧元”的表达式来触发转换。我们将使用正则表达式来匹配它。在与表达式匹配之后，我们将调用我们在扩展类中定义的“convert_currency”方法并呈现结果。

下面是“KeyboardQueryEventListener:
的最终代码

```
class KeywordQueryEventListener(EventListener):

    def on_event(self, event, extension):
        items = []

        regex = r"(\d+\.?\d*)\s([a-zA-Z]{3})\sto\s([a-zA-Z]{3})"
        query = event.get_argument() or ""

        matches = re.findall(regex, query, re.IGNORECASE)

        if not matches:
            items.append(ExtensionResultItem(icon='images/icon.png',
                                             name='Keep typing your query ...',
                                             description='It should be in the format: "20 EUR to USD"',
                                             highlightable=False,
                                             on_enter=HideWindowAction()))

            return RenderResultListAction(items)

        try:
            params = matches[0]

            amount = params[0]
            from_currency = params[1].upper()
            to_currency = params[2].upper()

            value = extension.convert_currency(
                amount, from_currency, to_currency)

            items.append(ExtensionResultItem(icon='images/icon.png',
                                             name="%s %s" % (
                                                 value, to_currency),
                                             highlightable=False,
                                             on_enter=CopyToClipboardAction(value)))

            return RenderResultListAction(items)

        except ConversionException as e:
            items.append(ExtensionResultItem(icon='images/icon.png',
                                             name='An error ocurred during the conversion process',
                                             description=e.message,
                                             highlightable=False,
                                             on_enter=HideWindowAction()))

            return RenderResultListAction(items) 
```

Enter fullscreen mode Exit fullscreen mode

如果你懂 Python，这很容易理解。我们使用“event.get_argument()”方法获取用户文本，然后查看它是否与我们的正则表达式匹配。

如果没有，我们只是向用户显示一条消息，让用户继续输入。如果匹配，我们从正则表达式中提取每个部分，并调用“convert_currency”方法。最后我们总是返回一个“ExtensionResultItem”列表。

下面是最终的“main.py”的样子:

```
import requests
import re
from ulauncher.api.client.Extension import Extension
from ulauncher.api.client.EventListener import EventListener
from ulauncher.api.shared.event import KeywordQueryEvent
from ulauncher.api.shared.item.ExtensionResultItem import ExtensionResultItem
from ulauncher.api.shared.action.RenderResultListAction import RenderResultListAction
from ulauncher.api.shared.action.HideWindowAction import HideWindowAction
from ulauncher.api.shared.action.CopyToClipboardAction import CopyToClipboardAction

class CurrencyConverterExtension(Extension):

    def __init__(self):
        super(CurrencyConverterExtension, self).__init__()
        self.subscribe(KeywordQueryEvent, KeywordQueryEventListener())

    def convert_currency(self, amount, from_currency, to_currency):
        params = {'access_key': self.preferences['api_key'], 'symbols': '%s,%s' % (
            from_currency, to_currency)}

        r = requests.get("http://data.fixer.io/api/latest", params=params)
        response = r.json()

        if r.status_code != 200:
            raise ConversionException(
                "Error connecting to conversion service.")

        if not response['success']:
            raise ConversionException(response['error']['info'])

        # Calculate the amount from the conversion rates.
        # Fixer.io base Currency is Eur.
        rates = response['rates']

        result = (float(amount) / rates[from_currency]) * rates[to_currency]

        return str(round(result, 2))

class KeywordQueryEventListener(EventListener):

    def on_event(self, event, extension):
        """ Handles the event """
        items = []

        regex = r"(\d+\.?\d*)\s([a-zA-Z]{3})\sto\s([a-zA-Z]{3})"
        query = event.get_argument() or ""

        matches = re.findall(regex, query, re.IGNORECASE)

        if not matches:
            items.append(ExtensionResultItem(icon='images/icon.png',
                                             name='Keep typing your query ...',
                                             description='It should be in the format: "20 EUR to USD"',
                                             highlightable=False,
                                             on_enter=HideWindowAction()))

            return RenderResultListAction(items)

        try:
            params = matches[0]

            amount = params[0]
            from_currency = params[1].upper()
            to_currency = params[2].upper()

            value = extension.convert_currency(
                amount, from_currency, to_currency)

            items.append(ExtensionResultItem(icon='images/icon.png',
                                             name="%s %s" % (
                                                 value, to_currency),
                                             highlightable=False,
                                             on_enter=CopyToClipboardAction(value)))

            return RenderResultListAction(items)

        except ConversionException as e:
            items.append(ExtensionResultItem(icon='images/icon.png',
                                             name='An error ocurred during the conversion process',
                                             description=e.message,
                                             highlightable=False,
                                             on_enter=HideWindowAction()))

            return RenderResultListAction(items)

class ConversionException(Exception):
    pass

if __name__ == '__main__':
    CurrencyConverterExtension().run() 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。如果您重新加载 Ulauncher，您应该可以让您的扩展工作。不要忘记去你的扩展设置和定义“API 键”的固定器服务。

你可以在这里安装这个扩展[的最终版本。](https://ext.ulauncher.io/-/github-brpaz-ulauncher-currency)

就是这样。如你所见，这是一个非常简单的过程。希望这足够清楚，我让你对 Ulauncher 本身及其扩展系统感兴趣。

希望看到扩展的数量上升；)

如果你有任何问题，欢迎在评论区提问。

谢谢大家！