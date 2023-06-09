# iOS 和 Android 本地化工具

> 原文：<https://dev.to/rogermolas/ios-localization-tool-2pgf>

本地化就是将您的应用程序翻译成多种语言的过程。

在需要支持多种语言情况下，包括 API 响应消息和动态字符串，您需要一个可本地化的列表。字符串文件，你需要根据你想要的语言对它进行本地化(例如 ***英语*** ， ***中文*** ， ***日语*** )。

Xcode 有一个**内置的**可本地化文件生成器，可以生成您的可本地化文件。您支持的每种语言的字符串。

以这种格式

```
en.lproj  // directory
   |- localizable.strings

zh.lproj  // directory
   |- localizable.strings

ja.lproj  // directory
   |- localizable.strings 
```

Enter fullscreen mode Exit fullscreen mode

太酷了！！

***但是里面的东西呢？*T3】**

你需要根据相应的语言复制并粘贴它。对于中国刺，你把它放在**zh.lproj/可本地化.字符串**上，对于日本刺，把它放在**ja.lproj/可本地化.字符串**上，你就万事大吉了。

***神圣的什叶派，我有 400 根刺需要被翻译成 3 种以上的语言***400 x3 = 1200

只要复制并粘贴你的 1200 行字符串，祝你好运！

### 解决复制粘贴之苦，节省时间。

可以使用 Github 上提供的名为[**CSV-localizer**](https://github.com/rogermolas/csv-localizer)[![GitHub stars](img/ce14b1de67b6955a2476deae32fae656.png)](https://github.com/rogermolas/csv-localizer)的工具。

只需指示你的翻译或翻译工作的人把所有翻译成 CSV 文件的基础上提供的格式字符串。

**csv-localizer** 可以通过
从自制软件安装

```
$ brew tap rogermolas/csv-localizer
$ brew install csv-localizer 
```

Enter fullscreen mode Exit fullscreen mode

使用以下命令
进行转换

```
$ csv-localizer -p ios -i path/csv_files/ -o path/output 
```

Enter fullscreen mode Exit fullscreen mode

> *输入目录，CSV 文件的目录路径，不应该包括。csv 文件*
> *" path/CSV _ files/*"**而不是**"*~~path/CSV _ files/strings . CSV~~*
> 
> 原因是，csv-localizer 可以处理多个 csv 文件，这就是为什么它只接受一个目录路径。

生成的文件

```
en.lproj  // directory
   |- localizable.strings

zh.lproj  // directory
   |- localizable.strings

ja.lproj  // directory
   |- localizable.strings 
```

Enter fullscreen mode Exit fullscreen mode

就这样，你准备好了。简单对吗？

它将生成与 Xcode 一样的格式，但这次它在每种相应的语言中都有本地化的内容。:)

**也适用于 Android，它将生成`string.xml`** 和内容

```
$ csv-localizer -p android -i path/csv_files/ -o path/output 
```

Enter fullscreen mode Exit fullscreen mode

别忘了给个 [![GitHub stars](img/ce14b1de67b6955a2476deae32fae656.png)](https://github.com/rogermolas/csv-localizer)