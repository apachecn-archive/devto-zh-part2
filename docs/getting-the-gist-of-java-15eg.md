# 掌握 Java 的要点

> 原文：<https://dev.to/awwsmm/getting-the-gist-of-java-15eg>

### 或

# 我度过了一个非常忙碌的周末

为了赶在即将到来的最后期限之前完成积压的工作，我在过去的这个周末花了大部分时间编码(写新东西和修复旧项目)。看看我写的一些要点，给我那些甜蜜的星星:

1.  又一个 Java 的树形结构
2.  [典型化器](https://gist.github.com/awwsmm/56b8164410c89c719ebfca7b3d85870b)——将对象的`String`表示解析成 Java 的一个原始包装类(对于时间戳也是`LocalDateTime`)
3.  [expand path](https://gist.github.com/awwsmm/7d947dc26688227225a6942467e6e1e5)——解释`~`或`~user`主目录、`%ENVIRONMENT%` `$VARIABLES`和 gl `*` bs 以返回文件列表，在 Windows 和 Linux 上都是如此。
4.  [SplitXSV](https://gist.github.com/awwsmm/3527b995f25f74268b02e330fd4311ba) -拆分一行标记分隔的文本并返回结果
5.  这是一个小的 repo 示例，展示了如何在 Maven 项目的代码中读取`src/main/resources/`文件
6.  [parse filename](https://gist.github.com/awwsmm/002c099e68265b8a171980a4038adb6e)——解析一个文件名，并在`String[]`中返回路径、文件名本身和扩展名(如果存在的话)。
7.  解析命令行参数并归类为标准参数或标志

请参见以下这些项目的简短描述和示例:

* * *

## [PNode](https://gist.github.com/awwsmm/16d2357c8e929c327c5e2f57aa08b416)

没有用 Java 写的[定制](https://github.com/gt4dev/yet-another-tree-structure) [树形结构](http://vivin.net/2010/03/02/maven-project-for-generic-n-ary-tree-in-java/)的[短缺](https://www.baeldung.com/java-binary-tree)，可能是因为最好的实现只存在于 [javax.swing](https://docs.oracle.com/javase/6/docs/api/javax/swing/tree/package-summary.html) 包中，现在[事实上已经弃用](https://www.quora.com/Is-it-worth-learning-Swing-in-2017)，取而代之的是 JavaFX。所以我写了一个树(或者更确切地说是一个节点)类，其中每个节点都有一个标签和一个值(两者或其中之一都可以是`null`)。通过它们在`List` s 中的索引来跟踪节点，这意味着当一个节点被“删除”时，它将被一个`null`所取代，以维持`List`索引。

```
Original tree:

    0 |-- n01_label
    1 |    |-- n02_label
    2 |    |    |-- n04_label
    2 |    |    |-- n05_label
    3 |    |         |-- n08_label
    1 |    |-- n03_label
    2 |         |-- n06_label
    2 |         |-- n07_label

Tree after orphaning n05:

    0 |-- n01_label
    1 |    |-- n02_label
    2 |    |    |-- n04_label
    1 |    |-- n03_label
    2 |         |-- n06_label
    2 |         |-- n07_label

n05 tree after orphaning n05:

    0 |-- n05_label
    1 |    |-- n08_label

Tree after adopting n05:

    0 |-- n01_label
    1 |    |-- n02_label
    2 |    |    |-- n04_label
    1 |    |-- n03_label
    2 |    |    |-- n06_label
    2 |    |    |-- n07_label
    1 |    |-- n05_label
    2 |         |-- n08_label 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## [典型师](https://gist.github.com/awwsmm/56b8164410c89c719ebfca7b3d85870b)

通常，在解析文本文件时，您希望确定正在读取的`String`所代表的数据类型。`Typifier`允许将`String`数据解释为任何 Java 的原始包装器类型，尽可能将数据封装在最窄的类型中。代码还允许用户定义时间戳格式，这样`String`也可以被解析为`LocalDateTime`对象！为了加速大型数据集的处理，可以将解释限制为仅“普通”类型(`Double`、`Boolean`、`LocalDateTime`、`String`)。

```
String.trim() is used to remove leading and trailing whitespace, so the following work fine:

  typify (" 23 ") => "23" [Byte]
  typify ("     3.4 ") => "3.4" [Float]

But if the user enters only whitespace, that's fine, too:

  typify (" ") => " " [String]
  typify ("    ") => "    " [String]

The user can choose to interpret 1/0 as true/false:

  typify ("1") => "true" [Boolean]
  typify ("true") => "true" [Boolean]
  typify ("0") => "false" [Boolean]

Ranges of Byte, Short, and Float are used to box the value in the narrowest type available:

  typify (" 2 ") => "2" [Byte]
  typify (" 200 ") => "200" [Short]
  typify ("2e9 ") => "2.0E9" [Float]
  typify (" 2e99") => "2.0E99" [Double]

If String has length 1 and is not Boolean or Byte, it will be assigned the Character type:

  typify ("4") => "4" [Byte]
  typify ("-") => "-" [Character]
  typify ("a") => "a" [Character]

Dates can also be parsed, and formats can be defined by the user:

  typify ("2014-12-22 14:35:22") => "2014-12-22 14:35:22" [LocalDateTime]
  typify ("3/5/99 6:30") => "3/5/99 6:30" [LocalDateTime] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## [扩展路径](https://gist.github.com/awwsmm/7d947dc26688227225a6942467e6e1e5)

通过命令行向 Java 程序传递文件列表并不总是像预期的那样工作。shell 可以自动扩展`$ENVIRONMENT_VARIABLES`或者 gl `*` bs，很多在`bash`中可用的功能在 Windows `cmd.exe`提示符下是不可用的。该脚本允许在 Windows 和 Unix 上以相同的方式解释路径，并在 bash4 之前的 shells(以及`cmd`)中添加了递归的“双 globbing”(`**`)，以及其他一些很酷的特性。

```
C:\>java ExpandPath "~/test/**.txt"

arg: '~/test/**.txt'

  C:\Users\andrew.watson\test\a\d\a.txt
  C:\Users\andrew.watson\test\b\d\e.txt
  C:\Users\andrew.watson\test\a\d\d.txt
  C:\Users\andrew.watson\test\a.txt
  C:\Users\andrew.watson\test\c\c.txt
  C:\Users\andrew.watson\test\1.txt
  C:\Users\andrew.watson\test\b\d\a.txt
  C:\Users\andrew.watson\test\b\b.txt
  C:\Users\andrew.watson\test\b\d\f.txt
  C:\Users\andrew.watson\test\0.txt
  C:\Users\andrew.watson\test\a\a.txt
  C:\Users\andrew.watson\test\b\a.txt 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## [SplitXSV](https://gist.github.com/awwsmm/3527b995f25f74268b02e330fd4311ba)

该脚本拆分一行标记分隔的文本，并返回结果标记。任何非字母数字字符前面都有一个`'\'`来转义它，所以即使是制表符或换行符也可以用作分隔符。

```
given sentinel: '-'

given string:
{1-800-944-2345}

result:
{1}, {800}, {944}, {2345}

----------

given sentinel: ' '

given string:
{hi how are you}

result:
{hi}, {how}, {are}, {you} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## [拥有资源的美凤](https://github.com/awwsmm/Maven-With-Resources)

试图找出如何在 Maven 项目的代码中读取“资源”文件可能会令人沮丧。网上有不同和矛盾的答案。为了子孙后代，我建立了一个 MWE Maven 项目，展示如何读取位于`src/main/resources`的文件，并在 Java 代码中使用这些文件。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [ awwsmm ](https://github.com/awwsmm) / [带资源的 Maven](https://github.com/awwsmm/Maven-With-Resources)

### 从 Maven 项目的 src/main/resources 中轻松读取文件

<article class="markdown-body entry-content container-lg" itemprop="text">

# 在 Maven 项目中使用资源文件

运行以下命令生成一个空白 Maven 项目:

```
mvn -B archetype:generate \
  -DarchetypeGroupId=org.apache.maven.archetypes \
  -DgroupId=com.companyname.packagename \
  -DartifactId=my-new-package
```

Enter fullscreen mode Exit fullscreen mode

它将在当前目录中创建一个名为“`my-new-package`”的新目录。接下来，在 Maven 项目目录中(在`my-new-package`中)创建一个名为`src/main/resources`的目录，并将以下文本添加到该目录中名为`example.txt`的文件中:

```
this is an example resource
here is a second line
and a third one 
```

移除文件`src/test/java/com/companyname/packagename/AppTest.java`和`src/main/java/com/companyname/packagename/App.java`。用以下文本替换您的`pom.xml`文件的文本:

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
 http://maven.apache.org/maven-v4_0_0.xsd"&gt
  <modelVersion>4.0.0</modelVersion>

  <groupId>     com.companyname.packagename  </groupId>
  <artifactId>  my-new-package               </artifactId>
  <packaging>   jar                          </packaging>

  <version
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/awwsmm/Maven-With-Resources)

* * *

## [ParseFileName](https://gist.github.com/awwsmm/002c099e68265b8a171980a4038adb6e)

这个简短的脚本对于快速获取文件或其扩展名的包含目录非常有用。用户可以根据这些属性有选择地处理文件。

```
The lines:

    System.out.println(Arrays.toString(parseFileName("test").get()));
    System.out.println(Arrays.toString(parseFileName("test.a").get()));
    System.out.println(Arrays.toString(parseFileName("test.a.b").get()));

...yield the output:

[/home/andrew, test, ]
[/home/andrew, test, a]
[/home/andrew, test, a.b] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## [ParseCLArgs](https://gist.github.com/awwsmm/01e8d63c24690582fa4cf0d008a93f38)

这是另一个简短的脚本，它将命令行参数解析为基本的`ARGUMENT`、`FLAG`(如`-this`)、`LONGFLAG`(如`--this`)或`OTHER`(只有`-`或`--`)。参数在`List`中按顺序返回，因此用户可以确定所提供的标志和参数是否有效。

```
$ java ParseCLArgs arg -short anotherArg --long - -- ---toolong
ARGUMENT=arg
FLAG=short
ARGUMENT=anotherArg
LONGFLAG=long
OTHER=-
OTHER=--
LONGFLAG=-toolong 
```

Enter fullscreen mode Exit fullscreen mode

* * *

所以你有它！如果你除了吃饭和睡觉什么都不做，这就是你一个周末能完成的编码量。0/10 不推荐。