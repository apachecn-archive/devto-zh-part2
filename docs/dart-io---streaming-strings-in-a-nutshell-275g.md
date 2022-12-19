# 简而言之，Dart IO 流字符串

> 原文：<https://dev.to/5422m4n/dart-io---streaming-strings-in-a-nutshell-275g>

# 起点

让我们从 [io-library-tour 中关于流文件内容](https://www.dartlang.org/dart-vm/io-library-tour) :
的例子开始

```
import 'dart:async';
import 'dart:io';
import 'dart:convert';

Future main() async {
  var config = File('config.txt');
  Stream<List<int>> inputStream = config.openRead();

  var lines = inputStream
      .transform(utf8.decoder)
      .transform(LineSplitter());
  try {
    await for (var line in lines) {
      print('Got ${line.length} characters from stream');
    }
    print('file is now closed');
  } catch (e) {
    print(e);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到，一个“config.txt”文件是以流式方式处理的。作为处理的一部分，有两个转换正在进行。

1.  [utf8.decoder](https://api.dartlang.org/stable/2.0.0/dart-convert/Utf8Decoder-class.html) 将无符号 8 位整数列表转换为字符串
2.  [LineSplitter](https://api.dartlang.org/stable/2.0.0/dart-convert/LineSplitter-class.html) 将一个字符串逐行拆分成单个的片段

然后，`await for`将基本上逐行处理该流，其中 EOL 字符串是产出列表的一部分。

# 让我们开始吧

那么这个`transform`是如何工作的呢？为此，我们将编写一个小的转换器，将每个字符串
转换成大写字符串。

## 爽，这怎么开始？

让我们检查流上转换的 [API。在那里我们发现了一个需要被忽略的`StreamTransformer<T, S>`。但是在检查之后，我们发现有一个更高层次的概念实现了这个接口，并且简化了很多。它叫做](https://api.dartlang.org/stable/2.0.0/dart-async/Stream/transform.html) [`Converter<S, T>`](https://api.dartlang.org/stable/2.0.0/dart-convert/Converter-class.html) 。所以我们的实现可以是这样的:

```
class UpperCase extends Converter<String, String> {
  @override
  String convert(String input) => input.toUpperCase();
} 
```

Enter fullscreen mode Exit fullscreen mode

嗯，那很简单！让我们运行整个程序，并检查它看起来如何:

```
import 'dart:async';
import 'dart:io';
import 'dart:convert';

class UpperCase extends Converter<String, String> {
  @override
  String convert(String input) => input.toUpperCase();
}

Future main() async {
  var config = File(Platform.script.toFilePath());
  Stream<List<int>> inputStream = config.openRead();

  var lines = inputStream
      .transform(utf8.decoder)
      .transform(LineSplitter())
      .transform(UpperCase());
  try {
    await for (var line in lines) {
      print('Got ${line.length} characters from stream');
      print(line);
    }
    print('file is now closed');
  } catch (e) {
    print(e);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$ dart io_expedition_iter0.dart

Unsupported operation: This converter does not support chunked conversions: Instance of 'UpperCase' 
```

Enter fullscreen mode Exit fullscreen mode

呜哇！

## 到底什么是组块转换？

让我们找出这个异常是从哪里产生的。那就是`Converter<S, T>` :

```
 /**
   * Starts a chunked conversion.
   *
   * The returned sink serves as input for the long-running conversion. The
   * given [sink] serves as output.
   */
  Sink<S> startChunkedConversion(Sink<T> sink) {
    throw new UnsupportedError(
        "This converter does not support chunked conversions: $this");
  } 
```

Enter fullscreen mode Exit fullscreen mode

这至少向我们表明，出于某种原因，一个`Converter`似乎以两种方式运作:

*   就像普通的只涉及`convert`方法一样
*   喜欢块状的

doc 块表明这是用于长期运行的转换。仍然不清楚这是运行时如何或为什么选择的路径。

## 让我们来关注如何解决这个问题

从签名中可以看出 [`Sink<S>`](https://api.dartlang.org/stable/2.0.0/dart-core/Sink-class.html) 有望返回。在我们的例子中，`Sink<String>`仅仅是发送字符串的目的地。所以让我们用一个像下面这样的小装饰类来拦截流:

```
class UpperCaseConversionSink extends StringConversionSinkBase {
  EventSink<String> wrapped;

  UpperCaseConversionSink(this.wrapped);

  @override
  void addSlice(String str, int start, int end, bool isLast) {
    wrapped.add(str.toUpperCase());
  }

  @override
  void close() {
    wrapped.close();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们像这样在`UpperCase`转换器中实现分块转换的开始:

```
 @override
  Sink<String> startChunkedConversion(Sink<String> sink) {
    return UpperCaseConversionSink(sink);
  } 
```

Enter fullscreen mode Exit fullscreen mode

```
$ dart io_expedition_iter1.dart

Got 19 characters from stream
LIBRARY IO_TESTING;
Got 0 characters from stream

Got 20 characters from stream
IMPORT 'DART:ASYNC';
Got 17 characters from stream
IMPORT 'DART:IO';
Got 22 characters from stream

# [...] 
```

Enter fullscreen mode Exit fullscreen mode

不错！那行得通。

# 我们来重构一下

正如你所看到的，小装饰器 sink `UpperCaseConversionSink`现在也了解了转换技术以及`UpperCase`转换器本身。这种重复可以通过引入一个更通用的接收器来清除，该接收器接受一个转换器并将具体的转换委托给转换器。让我们看看这可能是什么样子:

```
class StringEventConverterSink extends StringConversionSinkBase {
  EventSink<String> innerSink;
  Converter<String, String> converter;

  // [sink] is wrapped and [converter] knows about the concrete conversion algorithm
  StringEventConverterSink(Sink<String> sink, Converter<String, String> converter) {
    this.innerSink = sink;
    this.converter = converter;
  }

  @override
  void addSlice(String str, int start, int end, bool isLast) {
    innerSink.add(converter.convert(str));
  }

  @override
  void close() {
    innerSink.close();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

它的用法看起来像:

```
class UpperCaseConverter extends Converter<String, String> {
  @override
  String convert(String input) => input.toUpperCase();

  @override
  Sink<String> startChunkedConversion(Sink<String> sink) {
    return StringEventConverterSink(sink, this);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

完整的最终代码可以在我的 github 页面上找到。

## 闭包呢

当然，我们甚至可以进一步简化，使转换器本身更加通用，只接受闭包来完成工作。这样我们的用法看起来就像这样简单

```
 .transform(StringConverter((String x) => x.toUpperCase())); 
```

Enter fullscreen mode Exit fullscreen mode

所以我们将引入一个接受这个闭包的泛型`StringConverter`:

```
class StringConverter extends Converter<String, String> {
  String Function(String x) convertFunction;

  StringConverter(this.convertFunction);

  @override
  String convert(String input) => 
      convertFunction(input);

  @override
  Sink<String> startChunkedConversion(Sink<String> sink) => 
      StringEventConverterSink(sink, this);
} 
```

Enter fullscreen mode Exit fullscreen mode

完整的代码也在我的 github 页面上

# 四舍五入

*   Dart 流带有对变压器的内置支持
*   `Converter`用于那些转换
*   长时间运行的转换是分块处理的
*   字符串块处理可以通过从`StringConversionSinkBase`子类化来实现
*   装饰模式可以帮助拦截源和目的地接收器
*   转换器可以传递给拦截器，以将逻辑保存在一个地方
*   甚至闭包也可以用来进一步简化事情

对我来说，唯一悬而未决的问题是:道达尔决定转换是直接发生还是以分块方式发生。

如果你能澄清这一点，请随意发表评论或分享资源来进一步说明这一点。

感谢阅读

```
$ dart --version
Dart VM version: 2.0.0 (Fri Aug 3 10:53:23 2018 +0200) on "macos_x64" 
```

Enter fullscreen mode Exit fullscreen mode