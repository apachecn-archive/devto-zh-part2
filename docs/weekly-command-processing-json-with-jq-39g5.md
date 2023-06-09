# 每周命令:用 jq 处理 JSON

> 原文：<https://dev.to/roperzh/weekly-command-processing-json-with-jq-39g5>

本周的命令是`jq`，一个处理和操纵 JSON 输入的灵活工具。

在我们开始之前，让我说一篇博文如果不变成一本完整的手册，就无法公正地对待`jq`。如果您渴望了解更多信息，请访问参考资料部分。

## 基础知识

```
**jq** [_options_...] _filter_ [_files_...] 
```

Enter fullscreen mode Exit fullscreen mode

虽然`jq`可以采用各种各样的*选项*，但它们主要涉及输入和输出格式，真正神奇的是在`filter`参数中。

理解`jq`如何工作的关键点是把它想象成一个处理器:接收输入，根据定义的`filter`处理输入并生成输出。

## 例子

下面的大多数例子将使用 [NASA NeoWs API](https://api.nasa.gov/api.html#NeoWS) 的`/feed`端点，它检索一个日期范围内靠近地球的[小行星](https://en.wikipedia.org/wiki/Asteroid)的列表。

为了简单起见，让我们假设在每一个例子中，下面的`curl`命令被用来检索 JSON 并将其通过管道传输到`jq` :

```
$ curl "https://api.nasa.gov/neo/rest/v1/feed?\
start_date=$(date +%Y-%m-%d)\
&api_key=DEMO_KEY" 
```

Enter fullscreen mode Exit fullscreen mode

端点检索到的响应大致如下:

```
{
  "links" : { /* pagination info here */ },
  "element_count" : 70,
  "near_earth_objects" : {
    "2018-03-30" : [/* Asteroids around this date */],
    "2018-04-01" : [/* Asteroids around this date */]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你想跟随，并且手边没有终端，有一个[网上游乐场](https://jqplay.org)可供你使用。

### 基本过滤器

```
$ jq '.' 
```

Enter fullscreen mode Exit fullscreen mode

基本滤波器产生不变的输入。您可以将点看作“当前上下文”,在本例中是整个 JSON 输入。

*提示:由于默认情况下`jq`格式化其输出，您可以使用它在控制台中漂亮地打印 JSON。*

### 访问字段

```
$ jq '.element_count' 
```

Enter fullscreen mode Exit fullscreen mode

JSON 数据中的字段可以通过点(`.`)后跟字段名来访问。您可以认为这是从当前上下文中访问一个属性。

在这种情况下，我们正在检索`element_count`字段，以了解地球周围有多少小行星。当我运行它的时候，有 70 个！

### 收藏

```
$ jq '.[]' 
```

Enter fullscreen mode Exit fullscreen mode

`.[]`滤波器系列用途广泛:

*   如果输入是一个数组，`.[]`将遍历元素。
*   如果输入是一个对象，`.[]`将遍历对象值。
*   如果你提供了一个索引，`.[0]`将通过位置来访问数组中的元素。
*   如果你提供一个字符串，`.["foo"]`将通过它们的键访问对象中的元素。

### 管道

```
$ jq '.near_earth_objects | .[] | .[].name' 
```

Enter fullscreen mode Exit fullscreen mode

过滤器可以用管道连接起来，就像您通常在终端中使用命令一样。在这个例子中，我们循环通过数组`.near_earth_objects`的字段，并检索所有小行星的名称。

结果(单击展开)

```
"(2015 FN120)"
  "(1999 FP19)"
  "(2017 FW90)"
  "(2018 FH5)"
  "(2018 FM4)"
  "(2018 FF1)"
  "348306 (2005 AY28)"
  "(2008 GB110)"
  "(2012 QG42)"
  "(2016 CC194)"
  "(2017 GO4)"
  "(2018 CZ13)"
  "(2018 FV3)"
  "(2005 GR33)"
  "(2007 GS3)"
  "(2007 RX8)"
  "(2011 HN5)"
  "(2014 OE338)"
  "(2016 GA3)"
  "489486 (2007 GS3)"
  "(2018 FV1)"
  "(2018 EM4)"
  "(2007 DB61)"
  "17511 (1992 QN)"
  "(2001 OT)"
  "(2004 FG29)"
  "(2018 ER1)"
  "(2004 FG1)"
  "(2008 GH110)"
  "(2015 XE261)"
  "474574 (2004 FG1)"
  "(2017 RO17)"
  "498548 (2008 GH110)"
  "(2018 FW4)"
  "(2011 EC7)"
  "85953 (1999 FK21)"
  "204232 (2004 DG2)"
  "225586 (2000 WS67)"
  "(2009 FF)"
  "(2015 TC25)"
  "(2017 GX6)"
  "(2017 UD1)"
  "(2018 FW2)"
  "(2007 WB)"
  "(2008 GE)"
  "(2011 HJ)"
  "(2013 HM11)"
  "(2016 AG9)"
  "(2015 XT168)"
  "(2008 VR4)"
  "(2013 OW2)"
  "509520 (2007 WB)"
  "(2013 GZ7)"
  "(2011 UH20)"
  "441304 (2008 AU26)"
  "(2004 FJ29)"
  "(2015 EL7)"
  "(2016 FF1)"
  "(2016 WB10)"
  "(2018 EB)" 
```

Enter fullscreen mode Exit fullscreen mode

### 对象和数组构造

```
$ jq '{count: .element_count}'
$ jq '.near_earth_objects | [.[] | .[]]' 
```

Enter fullscreen mode Exit fullscreen mode

`jq`还允许您明确定义输出的外观，从而提供对输出格式的精细控制。

在第一个例子中，我们将返回一个对象，将`element_cont`作为`count`。

第二个例子有点复杂，我们将所有日期的所有`near_earth_objects`都放在一个数组中。在操作符和函数的帮助下，这可以以一种更好的方式完成(见下文)。

结果 1(单击展开)

```
{count: 70} 
```

Enter fullscreen mode Exit fullscreen mode

结果 2(单击展开)

```
[
    {/* Asteroid data */},
    {/* Asteroid data */},
    {/* Asteroid data */},
    {/* Asteroid data */},
  ] 
```

Enter fullscreen mode Exit fullscreen mode

### 运算符和函数

```
$ jq '.near_earth_objects | map(.[])' 
```

Enter fullscreen mode Exit fullscreen mode

`jq`还自带了一组内置的运算符和函数:`+`、`-`、`length`、`map`、`add`、`range`，[列表一直到](https://stedolan.github.io/jq/manual/#Builtinoperatorsandfunctions)。在这种情况下，我们将重温之前的例子，使用`map`将所有日期的所有`near_earth_objects`排列在一个数组中。

结果(单击展开)

```
[
    {/* Asteroid data */},
    {/* Asteroid data */},
    {/* Asteroid data */},
    {/* Asteroid data */},
  ] 
```

Enter fullscreen mode Exit fullscreen mode

### 现在都在一起了

```
$ jq '.near_earth_objects | map(.[] | {name, is_potentially_hazardous_asteroid})' 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们建立一个过滤器来过滤掉我们的家人和朋友。每个小行星都有一个`name`和一个名为`is_potentially_hazardous_asteroid`的参数，顾名思义，这个参数目前是基于测量小行星威胁接近地球的可能性的参数来定义的([来源](https://cneos.jpl.nasa.gov/faq/))。

让我们检索所有小行星名称的列表，表明它们是否危险:

结果(单击展开)

```
[
    {
      "name": "(2018 FF1)",
      "is_potentially_hazardous_asteroid": false
    },
    {
      "name": "348306 (2005 AY28)",
      "is_potentially_hazardous_asteroid": true
    },
    {
      "name": "(2008 GB110)",
      "is_potentially_hazardous_asteroid": false
    },
    {
      "name": "(2012 QG42)",
      "is_potentially_hazardous_asteroid": true
    }
  ] 
```

Enter fullscreen mode Exit fullscreen mode

## 资源

*   [jq 手册](https://stedolan.github.io/jq/manual/)
*   [jq 教程](https://stedolan.github.io/jq/tutorial/)