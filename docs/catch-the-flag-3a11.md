# 抓住旗子！

> 原文：<https://dev.to/belinde/catch-the-flag-3a11>

你见过把配置写成常量串联的吗？类似于`error_reporting = E_ERROR|E_CORE_ERROR`:这是什么样的巫术？事实证明，这是将许多布尔标志打包成单个值的一种非常常见(也非常聪明)的方式。

所以让我们做一些使用二进制标志的东西。我将使用 PHP，但是这些概念适用于任何支持布尔位操作符的语言。你得承认，它们很常见。因此，让我们假设我们有一个函数，当我们邀请我们的朋友回家吃饭时，这个函数设置桌子。有时我们会做一顿非常特别的晚餐，有开胃菜、第一道菜、第二道菜和甜点(我来自意大利，所以这并不罕见)，有时只是第一道菜和甜点，有时是任何其他排列。所以我们这样做:

```
function setTheTable(
    bool $appetizer = false,
    bool $firstCourse = false,
    bool $secondCourse = false,
    bool $dessert = false
): void
{
    if ($appetizer) {
        // do something
    }
    if ($firstCourse) {
        // do something
    }
    // here the others checks
} 
```

Enter fullscreen mode Exit fullscreen mode

嗯，那就是*真丑*。如果我们只需要甜点，我们必须叫一份令人尴尬的。如果我们需要添加对欢迎饮料、咖啡和利口酒的支持(意大利，还记得吗？)我们必须添加另外 3 个变量，因为我们希望向后兼容，所以它们都在前面的变量中排队...真的，很糟糕的解决方案。如果我们使用一个配置结构会更好，在 PHP 中数组是最好的解决方案:

```
function setTheTable(array $conf = []): void
{
    if ($conf['appetizer'] ?? false) {
        // do something
    }
    if ($conf['firstCourse'] ?? false) {
        // do something
    }
    // here the others checks
} 
```

Enter fullscreen mode Exit fullscreen mode

好多了，但还是有点糟糕。现在，我们依赖于一组自定义字符串:如果函数是唯一使用它们的函数，这是可以的，但是如果项目的其他部分依赖于它们，您就可以看到问题:字符串错误可能会带来很难发现的错误，IDE 无法帮助您。这是常量、枚举或其他类似语言结构的工作，但如果我们想使用对象而不是数组，我们可能会有问题。真是一团糟！我们只想要非常简单的东西

```
function setTheTable(int $conf = 0): void
{
    if (/* some magic here for appetizer */) {
        // do something
    }
    if (/* some magic here for first course */) {
        // do something
    }
    // here the others checks
} 
```

Enter fullscreen mode Exit fullscreen mode

但是如何才能实现这一点呢？

你必须知道的最重要的信息是，十进制数可以写成二进制形式。下面是我们将在本文中使用的数字的快速参考；我先把也分解一下，稍后你会发现原因:

| 小数 | 二进制的 | 分解 |
| --- | --- | --- |
| one | `0001` | one |
| Two | `0010` | Two |
| three | `0011` | 2 + 1 |
| four | `0100` | four |
| five | `0101` | 4 + 1 |
| six | `0110` | 4 + 2 |
| seven | `0111` | 4 + 2 + 1 |
| eight | `1000` | eight |

把你的注意力集中在那些没有分解的数字上:1，2，4 和 8。它们都是 2 的幂，在它们的二进制形式中，我们只能找到一个 1 和所有的 0。记住这个细节:2 的幂是重要的。

现在来看一些完全不同的东西，我们必须分析按位运算符对二进制数做了什么，因为它们将是我们标志系统的关键。我们通常使用布尔运算符，如`if ($a AND $b)`，将操作数视为一个完整的布尔值:因此我们知道`true AND false`是`false` , `true OR false`是`true` , `true XOR true`是`false` , `NOT true`是`false`。

按位运算符做同样的事情，但是在较低的层次上，一点一点地进行。符号也不同。c 语言教过我们写`&`代表按位`AND`，`|`代表`OR`，`^`代表`XOR`，`~`代表`NOT`。所以我们可以尝试一个简单的`0001 | 0010`:我们将写在专栏里，以明确这个想法:

`0001`→`0010`→`0011`→`0010`→`0011`→`0010`→【2 = 3

你明白其中的逻辑吗？运算符已经检查了两个操作数的第一个字符，并应用了一个正常的布尔逻辑:0 是`false`，1 是`true`，因此`false OR false`得到`false`，也就是 0；第二个字符是相同的；第三个是`false OR true`，所以`true`，又名 1；第四个是`true OR false`，所以再来一个 1。很简单，不是吗？

也许现在你会说“好吧，按位 OR 是加法，因为 1|2=3”。嗯，肯定*没有*。尝试`1|1`:

`0001`→`0001`→`0001`→`0001`→`0001`→【1 | 1 = 1

因此，按位运算符一次只能处理一个字符:“加法”没有“进位”。让我们再举一个例子，按位 AND:

`0111`→【T7】
`0101`→【5】
`0101`→【7】&5 = 5

有意思的是:`0101`中的所有 1 都与`0111`中的 1 配对，所以结果是另一个`0101`。

我们知道(或者几秒钟后你就会知道)5 被解构为 4 + 1，也就是`0100 + 0001`，因为在二进制数中，每个字符根据他的位置“获得”一个值，以 2 的幂为单位，从右到左；我们可以把`0101`写成`0*8 + 1*4 + 0*2 + 1*1`，如果去掉 0 的乘法运算，简化*1，你得到的只是`4+1`。很简单，不是吗？所以我们可以说“析构”就像一个反转的按位 OR，因为`1|4`等于`0001 | 0100`，也就是`0101`，我们的 5。

现在，深呼吸。试着用其他名字称呼 1、2、4 和 8，比如开胃菜、第一道菜、第二道菜和甜点。所以 5、`0101`，是:

*   0 甜点(0*8)
*   1 门二级课程(1*4)
*   0 第一道菜(0*2)
*   1 份开胃菜(1*1)

它们看起来像旗帜，对吗？所以我们可以尝试这种方法(但是记住，这些值必须都是非零的 2 的不同次方):

```
// Constants declaration
define( 'APPETIZER',     1 );
define( 'FIRST_COURSE',  2 );
define( 'SECOND_COURSE', 4 );
define( 'DESSERT',       8 );

// Our beloved function
function setTheTable(int $conf = 0): void
{
    // here the checks
}

// Our business logic
setTheTable(FIRST_COURSE|DESSERT);
setTheTable(SECOND_COURSE);
setTheTable(FIRST_COURSE|DESSERT|APPETIZER|SECOND_COURSE); 
```

Enter fullscreen mode Exit fullscreen mode

那就好！现在我们有了 IDE 可以识别的常量，参数的顺序是不固定的，我们可以在不破坏兼容性的情况下添加任何其他值。我们只有一个限制:我们使用整数，所以我们有 32 或 64 位可用(限制取决于语言、建筑、季节天气、星体连词等等；RTFM 是了解真实极限的唯一途径)。但是如果你在考虑一个超过 32 个布尔开关的函数，你就有更多的问题了。

但是...我们如何检查标志？例如，循环 5，我们记为`APPETIZER|SECOND_COURSE`:我们需要检查`APPETIZER`是否启用。换句话说，检查在配置参数的分解中我们是否有`APPETIZER`的值。这是一个按位 AND 运算:

`0101`→`APPETIZER|SECOND_COURSE`
`0001`→【1】→`APPETIZER`
`0001`→【5】&【1 = 1】→`APPETIZER`

如果我们检查`DESSERT`？让我们看看:

`0101`→5→`APPETIZER|SECOND_COURSE`
`1000`→8→`DESSERT`
`0000`→5&8 = 0→我们对此没有常数，所以它只是一个`false`值

酷！现在我们可以填充那些“这里有些魔力”的评论:

```
function setTheTable(int $conf = 0): void
{
    if ($conf & APPETIZER) {
        // do something
    }
    if ($conf & FIRST_COURSE) {
        // do something
    }
    // here the others checks
} 
```

Enter fullscreen mode Exit fullscreen mode

现在真漂亮！简洁明了。现在我们可以做一些很酷的技巧，比如检查选择中的任何标志:我们可以检查我们是否有开胃菜或甜点或者两者都有:

```
if ($conf & (APPETIZER|DESSERT)) {
    // something
} 
```

Enter fullscreen mode Exit fullscreen mode

它是如何工作的？首先我们计算`APPETIZER|DESSERT` : `0001|1000 = 1001`，然后我们检查`$conf`是否有一些 1 的共同点；让我们看一些不同配置的示例:

| 文字$conf | $conf | 结果 | 文字结果 |
| --- | --- | --- | --- |
| `APPETIZER` | `0001` | `0001` | `APPETIZER` |
| `APPETIZER⎮FIRST_COURSE` | `0011` | `0001` | `APPETIZER` |
| `APPETIZER⎮DESSERT` | `1001` | `1001` | `APPETIZER⎮DESSERT` |
| `FIRST_COURSE` | `0010` | `0000` | – |
| `FIRST_COURSE⎮SECOND_COURSE` | `0110` | `0000` | – |

因此结果携带了关于哪个标志有效打开的信息。因此，我们还可以检查所有必需的标志是否都是活动的:

```
if ((APPETIZER|DESSERT) === ($conf & (APPETIZER|DESSERT))) {
    // something
} 
```

Enter fullscreen mode Exit fullscreen mode

这有点冗长，但是如果你只做一次检查，就没问题了。但是如果你做了很多只有第一道菜和甜点的晚餐，以及很多包含所有成分的完整晚餐呢？嗯，你可以定义导出常数！

```
// Basic constants declaration
define( 'APPETIZER',     1 );
define( 'FIRST_COURSE',  2 );
define( 'SECOND_COURSE', 4 );
define( 'DESSERT',       8 );

// Derived constant declaration
define( 'SMALL_DINNER',  FIRST_COURSE|DESSERT );
define( 'FULL_DINNER',  APPETIZER|FIRST_COURSE|SECOND_COURSE|DESSERT );

// We can use derived constant like any other:

if ($conf & SMALL_DINNER) {
    // we have a first course, or a dessert, or both
}

if (SMALL_DINNER === $conf & SMALL_DINNER) {
    // we surely have a first course AND a dessert
} 
```

Enter fullscreen mode Exit fullscreen mode

这也很方便，因为现在你可以玩否定！我们来看看:

```
setTheTable( FULL_DINNER & ~DESSERT ); 
```

Enter fullscreen mode Exit fullscreen mode

它是如何工作的？首先让我们看看`FULL_DINNER`的值是什么:它被定义为`APPETIZER|FIRST_COURSE|SECOND_COURSE|DESSERT`，所以它是`0001|0010|0100|1000 = 1111`。在我们完成`~DESSERT`之后，是`~1000 = 0111`。最后一步是这两个值的 AND:`1111&0111 = 0111`。我们已经有效地从配置中删除了甜点，真是遗憾。现在，对于仅仅 4 个标志来说，它不是很有用，但是试着想想有很多标志，比如在 PHP 中，错误报告的配置:`E_ALL & ~E_NOTICE`比列出*所有其他可用级别*要好。如果在遥远的将来会添加另一个级别，开发人员会负责更改`E_ALL`的真实值，因此您的配置将保持有效。这带来了一个重要的建议:never，never， *never* 直接使用数值，只使用文字常量；数字可以改变它们的含义，另一方面常数是，嗯，*常数*！

最后一个技巧:如果您愿意，您可以直接用二进制整数表示法编写您的常数(如果您选择的语言支持的话)。当你习惯二进制标志时，它将是无用的，但在开始时可以使概念更清楚:

```
define( 'APPETIZER',     0b0001 ); // 1
define( 'FIRST_COURSE',  0b0010 ); // 2
define( 'SECOND_COURSE', 0b0100 ); // 4
define( 'DESSERT',       0b1000 ); // 8 
```

Enter fullscreen mode Exit fullscreen mode

-

这只是对二进制标志的介绍；编辑这些值时也有一些有趣的技巧，但我认为对于本文来说*a suffit*。如果这些概念有用，请告诉我，这是我关于 dev.to 的第一篇文章，我渴望得到反馈！