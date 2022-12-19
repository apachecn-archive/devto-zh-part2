# Python 的阿帕玛插件

> 原文：<https://dev.to/techcommunity/apama-plug-ins-for-python-302l>

# 集成数值第三方、硬件支持、机器学习等

阿帕玛的特定领域编程语言 EPL 有能力添加用 C++和 Java 编写的扩展点。10.3 版本还增加了一个令人兴奋的新选项，用 Python 编写它们。本文将探索一些现在可用的新功能。

| 2018 年第四期 | [![](img/a80ab71273994c8d589f4e03e98b42bb.png) 下载 PDFT4】](http://techcommunity.softwareag.com/ecosystem/download/techniques/2018-issue4/SAG_Apama_Python_TECHniques_Oct18_WEB.pdf) |
| --- | --- |

## Python 用例

通过提供直接从 EPL 访问 Python 的途径，由阿帕玛支持的流媒体应用可以利用现有的用 Python 编写的分析。您还可以让事件的源和接收器使用现有的 Python 访问库。

**硬件访问库**

很多系统都在用 Python 提供访问硬件接口的库。一个很好的例子是 Raspberry Pi 的 Sense HAT 组件。这已经过阿帕玛的测试，它可以通过一个非常简单的插件层从 Raspberry Pi 设备读取传感器数据，并向连接到 Pi 的设备发回命令。阿帕玛提供了一个运行在 Raspberry Pi 上的社区版相关器，可以用来测试这个功能。该模型可用于在基于 arm 的小型设备上提供边缘分析。

**数字和科学库**

Python 提供了丰富的库生态系统，如 numpi 和 scipi，它们在科学界广泛用于分析数据。使用 Python 的阿帕玛插件，可以将这些静态数据分析工具转换为实时流应用程序。

**机器学习和 Zementis for Nyoka**

最近最令人兴奋的发展是在机器学习和人工智能(AI)领域。这是 Python 与 Tensorflow 和 SKLearn 等平台集成的另一个重要领域。Software AG 提供了与 Python ML 框架集成的两个阶段。本版中的另一篇文章介绍了 Zementis for Nyoka，它提供了许多 Python 机器学习模型与标准的可移植建模语言预测模型标记语言(PMML)之间的集成。然后，这可以与阿帕玛 Zementis 插件

一起使用，以在流分析部署中推动分析。阿帕玛-Python 集成让你可以直接从 EPL 访问你的模型，包括那些不能在 PMML 直接表示的模型，从而让你进行快速的原型和开发。

有了这些机器学习框架，你可以让阿帕玛既提供数据来训练和更新你的模型，也可以根据你的模型的结果做出分析决策。这支持预测性流用例，如预测性维护和高级资本市场算法。

## 用 Python 编写 EPL 插件

完整的阿帕玛安装附带 Python

3.6.6 的安装，以及用于与阿帕玛集成的 Python 的阿帕玛专用库。

当你编写一个 EPL 插件 Python 时，你需要提供一个继承自`apama.eplplugin.EPLPluginBase`类的 Python 对象，并且有用`apama.eplplugin.EPLAction decorator`修饰的方法。这些方法将自动可供任何导入插件的 EPL 对象调用。一个简单的 Python 插件可能看起来像:

```
**from** apama.eplplugin **import** EPLPluginBase, EPLAction **class** Plugin(EPLPluginBase): **def** \_init\_(self, init): super(Plugin, self).\_init\_(init) @EPLAction(“action\<\> returns string”) **def** getString(): **return** “Hello World” 
```

这用一个动作定义了一个插件，该动作不带参数并返回一个字符串。

要使它对 EPL 可用，您必须将其添加到您的配置文件:

```
eplPlugins: pluginName: pythonFile: ${PARENT\_DIR}/plugin.py class: Plugin 
```

最后，你可以从 EPL 导入并调用它:

```
**monitor** Test { **import** “pluginName” as plugin; **action** onload() { **log** plugin.getString() at INFO; } } 
```

## 与 Python 接口

Python 插件接口支持在 Python 之间传递各种 EPL 类型，自动将它们转换为适当的 Python 类型。甚至复杂的嵌套类型和事件类型也可以在 EPL 和 Python 之间传递。

EPL 不知道的任意 Python 类型，可以存储在 EPL“块”类型中，允许创建 Python 对象，用 EPL 对象的生命周期存储，然后以较新的类型传递回 Python，以缓存对无法在 EPL 中准确表示的大型对象的引用。

任何合法的 EPL 动作签名都可以在 Python 函数的装饰器中声明。它必须是对象上的成员函数，可以接受并返回与声明的参数兼容的类型和数字。当插件第一次被导入到相关器时，对象的单个实例被创建，从 EPL 中的任何对象对这些方法的所有调用都将在同一个单例实例上被调用。

## 安装第三方库

阿帕玛附带了 Python 3.6.6 的完整安装，包括 pip3 包安装工具。在插件中使用第三方库有两种选择，比如上面描述的那些。在阿帕玛命令提示符下，您可以运行 pip3 install libraryname 将库安装到您的阿帕玛安装中。这将需要为您部署阿帕玛的每台机器完成。

或者，您可以利用内置的 Python 虚拟环境系统将库直接添加到您的项目中。在这种情况下，您需要将虚拟环境的路径添加到配置中 Python 库的搜索路径:

```
eplPlugins: pluginName: pythonFile: ${PARENT\_DIR}/plugin.py pythonPath: - ${PARENT\_DIR}/python-venv class: Plugin 
```

## 总结

阿帕玛与 Python 的集成，以及 Zementis for Nyoka，提供了与现有应用程序和用 Python 编写的库的紧密集成，从硬件控制库到数字和科学库，再到人工智能和机器学习。这为实时分析的未来提供了令人兴奋的新选择。

要了解更多信息，请查看 Software AG 阿帕玛公司的文档以及您的阿帕玛安装中提供的 Python 插件示例。