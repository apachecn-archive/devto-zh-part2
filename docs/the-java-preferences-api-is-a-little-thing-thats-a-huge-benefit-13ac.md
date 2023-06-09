# Java Preferences API 是一个有巨大好处的小东西

> 原文：<https://dev.to/argherna/the-java-preferences-api-is-a-little-thing-thats-a-huge-benefit-13ac>

## Java 首选项处理你想做的大部分事情

强有力的声明，但这是真的。我们都遇到过不同的管理配置数据的方法。Java Preferences API 是 JDK 中经常被忽略但非常有用的小东西之一。它处理了大量管理外部数据的“繁重工作”,并减少了代码的工作量。

## 配置数据放在哪里？

自 2002 年以来，我一直在积极开发 Java，但我仍然会问“我应该把配置数据放在哪里？”。通常答案是“无论你的用户想让它呆在哪里！”。尽管有很多选择。你想要哪一个？

### 一个纯文本文件

一个普通的旧的 Java 属性文件多年来一直是我配置的首选(也应该是你的)。它们很容易接近和理解。但是你把文件放在哪里，你的代码如何读取它呢？你把它放在类路径上了吗？你把它放在外部目录里吗？您是否更改了代码以便应用程序“知道”在哪里查找？你为你的文件添加了对花哨的 Url 样式名称的支持吗？

这些问题让许多开发人员和客户感到沮丧。如果 Java 至少给了我们一个默认值就更好了，这个默认值可以像 JDK 日志配置那样构建，配置文件可以被存储，然后用系统属性覆盖。

我经常问的另一个问题是，用户实际上多久改变一次他们的配置？我的经验告诉我答案是很少，如果有的话。因此，虽然配置文件易于访问和更改，但如果不经常更改，这种访问的便利性就变得毫无意义(想想 [YAGNI](https://www.martinfowler.com/bliki/Yagni.html) )。

### 其他文件格式和库

有时你认为需要一种不同于 Properties 的格式，所以你尝试其他格式，比如 XML、JSON、YAML，或者其他任何格式(比如可怕的 HCL[。这一切都很好，选择不同的文件格式有很好的“理由”(合理化)。你所做的不过是增加你的用户的](https://github.com/hashicorp/hcl#why)[认知负荷](https://en.wikipedia.org/wiki/Cognitive_load)。一个特殊的配置文件格式不会给你的应用程序增加任何东西。如果您的客户在配置正常工作后不会改变太多，那还有什么意义呢(见上文)？

但是，假设您已经决定使用不同的文件格式。现在你需要代码来处理它。你可以写出来。那很好。或者会吗？编码，调试，测试，重复直到缺陷消失。这是浪费时间，对你的应用程序的功能没有任何帮助。

好吧，你说，我会遵循 Josh Bloch 在有效 Java 中的建议，只使用一个[稳定库](https://commons.apache.org/proper/commons-configuration/index.html)那是[广泛使用的](https://spring.io/)，因为每个人都已经清除了 bug。使用图书馆**是个很好的建议**。只是现在您已经向应用程序添加了一个依赖项。如果你的依赖关系图已经很复杂，这将使它变得更糟。有许多用于解析配置的库可供选择。哦，不要忘记为您的配置编写测试。当涉及到配置时，你的用户根本不关心应用程序是否正常工作。

### 在数据库或者 LDAP 里怎么样？

不要。现在，您已经向应用程序添加了另一个集成点/依赖项。

## 伙计，你真是个混蛋！

所有这些都在厚脸皮的讨论中根深蒂固。每个人做他们做的事都有理由。我们都知道，配置应该增强您的应用程序或服务，而不是处于前端和中心。对于基于 Java 的应用程序，有一种简单的方法可以做到这一点。那就是使用[首选项 API](https://docs.oracle.com/javase/10/docs/api/java/util/prefs/package-summary.html) ，它已经是 JDK 的一部分(从 1.4 版本开始就是)，是这些常见问题的解决方案。

## 那么什么是 Java 偏好呢？

Java 首选项是以特定于平台的方式存储并以平台无关的方式访问的数据，用于存储很少更改的配置。迷茫？我们来分解一下。

首选项数据以一种最适合 JVM 当前运行平台的方式存储。例如，你的 JVM 知道它运行在 Windows 上，所以 [PreferencesFactory](https://docs.oracle.com/javase/10/docs/api/java/util/prefs/PreferencesFactory.html) 的具体实现将使用 [Windows 注册表](https://stackoverflow.com/a/32670299/37776)来存储它的数据。其他平台会使用适合他们的东西。这一切都没问题，因为 JDK 中用于访问偏好数据的 API 是相同的，无论您在什么平台上，它都让 Java 自己决定如何最好地存储这些数据。这些细节已经(非常恰当地)从您这里抽象出来，让您专注于读/写使您的应用程序工作所需的数据。

所以现在你不用担心访问你的平台的后台存储，我该如何改变它呢？嗯，由于数据是以特定于平台的方式存储的，这可能意味着如果没有特定于操作系统的工具，您就不能轻松地访问它。大部分时间都没问题，对吧？我的意思是，如果你经常调整你的应用程序，那是一回事，但如果你只关心设置和运行，而不担心不断改变，那么这是最好的选择。例如，要在 Windows 中读取或更改首选项数据，您可能需要使用 Regedit 并导航到您想要更改的键。尽管如此，这个 API 还是很容易使用和理解，你可以[编写你自己的工具](https://github.com/argherna/Dotfiles/blob/master/Scripts/ImportPrefsTool.java) [来为你做这些工作](https://github.com/argherna/Dotfiles/blob/master/Scripts/ExportPrefsTool.java)使用 API，让你的 JRE 去担心编辑细节。

以这种方式使用和编辑偏好的最大好处是有一个标准格式[用于平台间共享偏好数据。这在后备存储器之外是完全可编辑的，并且可以相对容易地导入到另一个后备存储器。](https://docs.oracle.com/javase/10/docs/api/java/util/prefs/Preferences.html)

## 这些只是键值对还是什么？

偏好是树。这棵树的根有许多节点。根用于确定子节点的范围(要么是当前的*用户*，要么是整个*系统*)。节点是根下的名称。每个节点可以有另一个节点或地图作为子节点。最后，一个映射有子条目，它们是数据的键值对。

节点的约定是以类似于 unix 风格的路径命名它们，用完全限定的类名(包括包)命名它们。例如在*用户*根下，可能有一个名为`com/github/argherna/LocalDirectoryServer`的节点，其中每一段“路径名”都是一个节点下的一个节点。在最底层节点，可能有一个包含键和值的映射。Preferences API 允许您将它们读取为[字符串](https://docs.oracle.com/javase/10/docs/api/java/util/prefs/Preferences.html#get(java.lang.String,java.lang.String))、[布尔值](https://docs.oracle.com/javase/10/docs/api/java/util/prefs/Preferences.html#getBoolean(java.lang.String,boolean))、[整数](https://docs.oracle.com/javase/10/docs/api/java/util/prefs/Preferences.html#getInt(java.lang.String,int))、[字节数组](https://docs.oracle.com/javase/10/docs/api/java/util/prefs/Preferences.html#getByteArray(java.lang.String,byte%5B%5D))和其他数据类型，如果键-值对不在映射中，它要求您设置一个默认值(Preferences 不喜欢空值)。

偏好的组成部分是有限制的。它们意味着在多个平台上工作，因此它们必须符合这些平台的最低标准。因此，一个[节点的名称](https://docs.oracle.com/javase/10/docs/api/java/util/prefs/Preferences.html#MAX_NAME_LENGTH)，一个[键的名称](https://docs.oracle.com/javase/10/docs/api/java/util/prefs/Preferences.html#MAX_KEY_LENGTH)，以及一个作为字符串的[值的长度](https://docs.oracle.com/javase/10/docs/api/java/util/prefs/Preferences.html#MAX_VALUE_LENGTH)，都有一个最大长度。

## 给我看一个“平台无关”API 的例子

当您将读取和写入属性文件与读取和写入首选项进行比较时，您会注意到您过去担心的事情都消失了。

### 阅读偏好

访问首选项应遵循的步骤是:

1.  获取 root(或者是*用户*或者是*系统*)。
2.  获取根目录下的节点。
3.  读取节点下的键值对。

如果键值对不在节点下，它将在后备存储中创建。

```
Preferences myConnectionPrefs = Preferences.userRoot().node("com/github/argherna/MyApplication/connections");
String url = myConnectionPrefs.get("url", "http://example.com");

// Now start doing stuff with the url. 
```

Enter fullscreen mode Exit fullscreen mode

### 保存偏好设置

与上面类似，保存首选项的步骤如下:

1.  获取 root(或者是*用户*或者是*系统*)。
2.  获取根目录下的节点。
3.  将键值对保存在节点下。

对我来说，偏好 API 的这一部分代表了一个小的[漏抽象](https://en.wikipedia.org/wiki/Leaky_abstraction)。用于保存数据的方法以动词`put`开始，它与[地图](https://docs.oracle.com/javase/10/docs/api/java/util/Map.html)接口相关联。也许那是故意的。如果是的话，我应该能够获得节点下的地图数据结构并直接对其进行操作，然后将其设置回节点，并让 API 持久保存设置。

```
String newUrl = "https://secure.example.com/";
Preferences myConnectionPrefs = Preferences.userRoot().node("com/github/argherna/MyApplication/connections");

// Save the newUrl.
myConnectionPrefs.put("url", newUrl); 
```

Enter fullscreen mode Exit fullscreen mode

### 其他操作

可以删除、导出和导入首选项。导出和导入操作依赖于本文中链接的上述文件格式。您可以检查节点的子节点。您可以检查与节点相关联的值。这个 API 完全有能力做你能想到的任何有偏好的事情。

## 这还能干什么？

Prefrences API 的伟大之处在于它是可扩展的。参见[首选项 API](https://docs.oracle.com/javase/10/docs/api/java/util/prefs/Preferences.html) 。这意味着，如果您的 IT 部门通过将设置放在数据库或 LDAP 中来集中设置(正如我之前抱怨的那样)，您可以编写自己的 [PreferencesFactory](https://docs.oracle.com/javase/10/docs/api/java/util/prefs/PreferencesFactory.html) 实现，连接到它并管理读写操作以及数据结构。然而，对于几乎所有的目的，默认的实现就足够了。

## 有哪些弊端？

公平的问题。还有前面提到的长度限制。编辑首选项需要一些工具(由您的平台或您自己提供)。

最大的缺点之一是 Preferences API 不能很好地扩展。每个节点只能关联一个名称-值对(不允许基于集合的值)。但是取决于你如何使用它们，这不应该是一个问题。

知道何时使用它们也很重要。我在以下情况下使用偏好设置:

*   我可以保持少量的配置。
*   我需要依靠 JRE 来做出关于在哪里存储设置的正确决定。
*   这些设置很少改变，如果有的话。
*   应用程序可以内置一个参数编辑器(包括 web 应用程序),或者我有工具可以编辑它们。

## 这些你自己用吗？

是的，我知道。查看我的[点文件](https://github.com/argherna/Dotfiles/tree/master/Scripts)库中的一些实用程序。在那里你会看到我写的支持我的应用程序的首选项工具。还有，看我的项目[长枪](https://github.com/argherna/pike)。偏好符合我开发某些东西的方式。它们不仅仅适用于 GUI 应用程序。这是 JDK 能做好这项工作的小东西之一。