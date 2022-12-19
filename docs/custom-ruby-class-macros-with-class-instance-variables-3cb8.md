# 带有类实例变量的自定义 Ruby 类宏

> 原文：<https://dev.to/sophiedebenedetto/custom-ruby-class-macros-with-class-instance-variables-3cb8>

类宏是仅在定义类时使用的类方法。它们允许我们在跨类的时候停止共享代码。在本文中，我们将构建一个自定义的类宏，它利用类实例变量来定义特定于类的属性。

## 类级共享代码

我们将从我的个人博客上的[早期帖子中重温一个领域模型，在那里我们构建了自己的 XML 模板引擎。我们在模块`XmlFormattable`的帮助下构建了我们的引擎，我们将该模块混合到任何需要响应`#to_xml`来生成 XML 字符串的类中。](http://www.thegreatcodeadventure.com/roll-your-own-xml-templater-in-ruby-2/)

之前，我们在`Album`、`Track`和`Artist`类中包含了我们的模块，以帮助我们编写描述音频发布元数据的 XML。现在，我们的应用程序已经发展壮大，我们为特定的在线商店提供了各种子类`Album`类，我们将这些元数据发送到这些在线商店。

我们有三个相册子类:

*   `Itunes::Album`
*   `Spotify::Album`
*   `DDEX::Album`

其中`Itunes::Album`和`Spotify::Album`类描述了专门为这些商店格式化的相册，`DDEX::Album`类定义了可以发送到各种不同商店的默认相册。 *(DDEX 是定义音频发布 XML 的行业标准)*

我们的共享`XmlFormattable`模块需要知道给定专辑的 XML 格式，即 iTunes、Spotify 或 DDEX 格式，以便编写正确的 XML 并根据正确的模式验证编写的 XML。

我们的`XmlFormattable`模块看起来像这样:

```
module XmlFormattable  
  def to_xml
    formatter.format
  end

  def render(file_name, object, options)
    formatter.render(file_name, object, options)
  end

  def formatter
    @formatter ||= XmlFormatter.new(self, "format type goes here!!")
  end
end 
```

### 用错误的方式来干我们的课

我们如何让`XmlFormattable`模块知道格式类型？

嗯，这是给定的`::Album`类的每个实例共享的信息。一种选择是在每个类中定义类常数:

```
class Itunes::Album
  include XMlFormattable
  XML_FORMAT = "itunes_version_9"
end 
```

```
class Spotify::Album
  include XmlFormattable
  XML_FORMAT = "spotify_version_7"
end 
```

```
class DDEX::Album
  include XmlFormattable
  XML_FORMAT = "ddex_version_38"
end 
```

然后，我们可以像这样在`XmlFormattable`模块中引用我们的类常量:

```
module XmlFormattable  
  def to_xml
    formatter.format
  end

  def render(file_name, object, options)
    formatter.render(file_name, object, options)
  end

  def formatter
    @formatter ||= XmlFormatter.new(self, XML_FORMAT)
  end
end 
```

**这种做法违反了单一责任原则。**最初创建`XmlFormattable`模块的全部目的是负责编写单个相册类的 XML *输出*。这种方法在 album 类中引入了一个与编写和验证 XML 相关的属性。这使得定义和管理该属性成为每个`::Album`类的唯一职责。

同时，我们让我们的`XmlFormattable`模块依赖于一条信息`XML_FORMAT`，而这条信息根本不是由这个模块定义或控制的。相反，它是由这个模块所在的类来管理的。这迫使将来使用这个模块的人知道并记住在包含这个模块的类中定义`XML_FORMAT`类常量。这种分裂的大脑状态使得我们很难扩展模块的使用。

我们如何让我们的模块负责定义一个类属性*和*来确保不同类的类属性*不同*？我以为你不会问了！

### 类宏

类宏是定义类时只计算一次的类方法。我们将在我们的`XmlFormattable`模块中定义一个类宏，我们将在每个`::Album`类中调用这个类宏来指定 XML 格式。

下面是我们如何使用宏:

```
class Itunes::Album
  include XmlFormattable
  xml_format "itunes_version_9"
end 
```

```
class Spotify::Album
  include XmlFormattable
  xml_format "spotify_version_7"
end 
```

```
class DDEX::Album
  include XmlFormattable
  xml_format "ddex_version_38"
end 
```

#### 定义类宏

定义一个类宏很容易——它实际上只是一个普通的旧类方法。我们将在子模块`XmlFormattable::ClassMethods`中定义我们的`xml_format`宏，这样我们就可以将它作为一个类方法使用。

```
module XmlFormattable
  def self.included(base)
    base.extend ClassMethods
  end

  module ClassMethods
    def xml_format(name)
      # coming soon!
    end
  end 
```

我们有一个类方法`.xml_format`，它接受一个 XML 格式名的参数。我们在每个`::Album`子类中将这个类方法称为宏。

现在我们有了一个接受 XML 格式参数的宏，我们将如何为每个`::Album`类存储这种格式，以便以后该类的实例可以检索它？

我们想到的一个存储特定于类的信息的选项是类变量。让我们看看当我们在这里尝试使用类变量时会发生什么。

### (剧透)类变量不行！

我们将在我们的`XmlFormattable`模块中设置一个类变量`@@xml_format`等于默认值`"ddex"`。*我们给它这个默认值，因为 DDEX 是音频发布 XML 的行业标准。*

```
module XmlFormattable
 module ClassMethods
    @@xml_format = "ddex"
  end
end 
```

然后，我们将使用我们的`.xml_format`类方法将`@@xml_format`变量赋给给定的 XML 格式类型。

```
module XmlFormattable
 module ClassMethods
    @@xml_format = "ddex"

    def xml_format(format)
      @@xml_format = format
    end
  end
end 
```

最后，我们将为我们的模块提供一种读取`@@xml_format`变量的方法，以将其公开。

```
module XmlFormattable
 module ClassMethods
    @@xml_format = "ddex"

    def xml_format(format)
      @@xml_format = format
    end

    def format
      @@xml_format
    end
  end
end 
```

好的，让我们看看当我们定义我们的`::Album`子类时会发生什么。对于每个子类，`xml_format`宏设置`@@xml_format`等于什么？

```
class Itunes::Album
  include XmlFormattable
  xml_format "itunes_version_9"
end

class Spotify::Album
  include XmlFormattable
  xml_format "spotify_version_7"
end

class DDEX::Album
  include XmlFormattable
  xml_format "ddex_version_38"
end

itunes_album = Itunes::Album.new
itunes_album.class.format
  => "ddex_version_38"

spotify_album = Spotify::Album.new
spotify_album.class.format
  => "ddex_version_38"

ddex_album = DDEX::Album.new
ddex_album.class.format
  => "ddex_version_38"f 
```

什么？？对于我们的`::Album`类的*所有*的实例，`.format`计算为`"ddex_version_38"`！

这是因为`xml_format`类宏正在更新`@@xml_format`类变量的值，该变量由混合了我们的模块的所有类共享*。请记住，将一个模块混合到一个类中会将该模块添加到该类的继承链中。*

我们所做的是将我们的`@@xml_format`类变量绑定到`XmlFormattable`模块，该模块由我们所有三个相册类共享*。这意味着每次我们调用`xml_format`宏时，我们都在*重新分配*同一个共享类变量。*

 *当定义一个类时，我们的类宏被调用，所以当我们定义`Itunes::Album`类时，`@@xml_format`被设置为`"itunes_version_9`，当我们随后定义`Spotify::Album`类时，我们将同一个变量重置为`"spotify_version_7"`。最后，当我们定义`DDEX::Album`时，它最后一次覆盖了那个类变量，将其设置为等于`"ddex_version_38"`。

如果有一种方法可以使用共享代码*和*来存储类属性，那么属性的值应该是特定于每个类的...

### 类实例变量拯救！

类实例变量将让我们做到这一点。(你太惊讶了，我知道。)在 Ruby 中，*任何对象*都可以赋一个实例变量。除了一个普通的旧对象，还有什么是类呢？

> Ruby 中的类是一级对象——每一个都是类`Class`––[Ruby Docs](https://ruby-doc.org/core-2.2.0/Class.html)的实例

这意味着一个*类也可以有一个实例变量*，和任何其他 Ruby 对象一样。

不同于由一个类(或模块)的所有后代共享的类变量，类实例变量是特定于给定的类的。

让我们用一个类实例变量替换我们的类变量`@@xml_format`来解决我们的问题。

```
module XmlFormattable
 module ClassMethods
    @xml_format = "ddex"

    def xml_format(format)
      @xml_format = format
    end

    def format
      @xml_format
    end
  end
end 
```

我们可以看到，我们在这里所做的只是在类级别上给了我们的`XmlFormattable`模块一个实例变量:

```
XmlFormattable::ClassMethods.instance_variables
  => [:@xml_format] 
```

现在，当我们定义三个相册类时，`@xml_format`实例变量为每个单独的类设置。它不会覆盖共享的类变量。

```
class Itunes::Album
  include XmlFormattable
  xml_format "itunes_version_9"
end

class Spotify::Album
  include XmlFormattable
  xml_format "spotify_version_7"
end

class DDEX::Album
  include XmlFormattable
  xml_format "ddex_version_38"
end

itunes_album = Itunes::Album.new
itunes_album.class.format
  => "itunes_version_9"

spotify_album = Spotify::Album.new
spotify_album.class.format
  => "spotify_version_7"

ddex_album = DDEX::Album.new
ddex_album.class.format
  => "ddex_version_38" 
```

就是这样！

## 结论

类实例变量并不神奇。在类级别上使用实例变量与在类的特定实例中使用实例变量没有什么不同。这是因为类也是对象。

通过利用类实例变量，我们能够编写一个宏，为我们的每个`::Album`子类定义一个类级别和特定于类的属性。这允许我们将定义和管理我们个人相册类的`xml_format`属性*和*的责任转移到*我们的`XmlFormattable`模块中，保持我们的代码干燥并遵守 SRP。*

编码快乐！*