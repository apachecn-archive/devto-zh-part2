# 新版本:Tate v1.0.0

> 原文：<https://dev.to/krmbzds/new-release-tate-v100-2ki3>

💎刚刚发布了泰特的重大更新！(1.0.0 版)💎

**发布亮点:**
👉自定义过滤器支持
👉支持 11 种新语言
👉改进的命令行工具

🔗来看看吧！

* * *

# 泰特·✍️

**Tate** 转换重音字符，并将非拉丁文字音译为最接近的 ASCII 字符。

Tate 是一个生产力工具，它的行为就像一个标准的 Unix 应用程序，并且可以与其他 Unix 命令链接。它从标准输入读取，并写入标准输出。你可以将它用作[命令行实用程序](#commandline)或[库](#library)。

## 例子

假设你有一个法语句子，里面有很多奇怪的字符，你想用最有代表性的方法把它转换成 ASCII 码。您可以使用:

```
echo 'Le cœur de la crémiére' | tate  #=> Le coeur de la cremiere 
```

Enter fullscreen mode Exit fullscreen mode

或者一些你看不懂的保加利亚文:

```
echo 'Здравей!' | tate --lang=bg  #=> Zdravey! 
```

Enter fullscreen mode Exit fullscreen mode

使用自定义过滤器的`lang`选项设置语言，例如德语:

```
echo 'Von großen Blöcken haut man große Stücke.' | tate --lang=de

```

字母\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\}和\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\

```
Von grossen Bloecken haut man grosse Stuecke.

```

特定于语言的标点符号将被转换为最接近的等效 ASCII 码。

例如，在加泰罗尼亚语中，注意引号(cometes franceses)和标点符号(punt volat)是如何音译的:

```
«Dóna amor que seràs feliç!». Això, il·lús company geniüt, ja és un lluït rètol blavís d’onze kWh.
"Dona amor que seras felic!". Aixo, il-lus company geniut, ja es un lluit retol blavis d'onze kWh. 
```

Enter fullscreen mode Exit fullscreen mode

## 安装

将这一行添加到应用程序的 Gemfile:

```
gem 'tate' 
```

Enter fullscreen mode Exit fullscreen mode

然后执行:

```
$ bundle 
```

Enter fullscreen mode Exit fullscreen mode

或者自己安装为:

```
$ gem install tate 
```

Enter fullscreen mode Exit fullscreen mode

## 用法

### Ruby 库

```
require 'tate'
Tate::transliterate('Zəfər', language='az')  #=> Zefer 
```

Enter fullscreen mode Exit fullscreen mode

### 命令行实用程序

```
Usage: tate [options]
-l, --lang=[LANGUAGE]            Set language for custom filters
-h, --help                       Show this message
-v, --version                    Show version 
```

Enter fullscreen mode Exit fullscreen mode

#### 互动模式

如果您调用`tate`而没有提供任何参数，它将期望您使用标准输入(键盘)来提供输入。当你完成输入后，你可以使用`cmd + D`触发`EOL (End of Line)`，结果将打印在下一行。

#### 标准流

您可以将另一个命令的输出通过管道传输到 tate。

```
curl gov.bg/bg | tate --lang=bg > index.html 
```

Enter fullscreen mode Exit fullscreen mode

## 语言支持

有自定义过滤器

```
Azeri, Bulgarian, Catalan, French, German, Hungarian, Polish, Romanian, Spanish, and Vietnamese. 
```

Enter fullscreen mode Exit fullscreen mode

已知以下语言可以工作(没有自定义过滤器):

```
Croatian, Czech, Danish, Esperanto, Estonian, Finnish, Icelandic, Latvian, Lithuania, Norwegian, Portuguese, Scottish, Slovak, Slovenian, Swedish, Turkish, and Welsh. 
```

Enter fullscreen mode Exit fullscreen mode

下一步是什么？

```
Russian, Irish, Arabic, and Yoruba. 
```

Enter fullscreen mode Exit fullscreen mode

## 有什么好？

是的。

### 捐款

你可以在 [Librepay](https://liberapay.com/krmbzds/donate) 把我捐了。谢谢！☕️

## 琐事

**tate** 是**t**rans 升**吃**的简称。

没人有时间在终端上输入音译。🤷‍♂️

* * *

既然你已经走了这么远..🤓

如果你喜欢，别忘了⭐️我的[库](https://github.com/krmbzds/tate)！

欢迎错误报告和请求！♥️

快乐黑客，
凯雷姆