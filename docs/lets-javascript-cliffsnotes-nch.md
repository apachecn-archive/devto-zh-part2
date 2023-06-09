# 新手 JavaScript！CliffsNotes

> 原文：<https://dev.to/kauresss/lets-javascript-cliffsnotes-nch>

[![image](img/e019a7aa3035250eeaec44feb7b0f212.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k77mSZ4n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dgzitqw69vjprkahl7f8.png)

### 简介

大量的 JavaScript 注释。CliffsNotes 版本。

### JavaScript 语法

#### JavaScript 注释

以//开头的一行是注释。

多行注释的形式如下:

```
 /*   

            */ 
```

#### 严格模式

当严格模式生效时，JavaScript 解释器将发出不安全语法的错误。通过将以下语句放在 JavaScript 文件的顶部或 JavaScript 方法的顶部来调用严格模式:

“使用严格”；

#### JavaScript 语句

JavaScript 语句区分大小写。语句总是以分号结尾:；

#### 代码块

多个 JavaScript 语句可以包含在一个代码块中，如下所示:

```
{
   ….   
} 
```

#### 空白

JavaScript 解释器忽略语句中的空白。

#### JavaScript 变量

在 JavaScript 中，变量保存值。变量名区分大小写。变量可以以＄或下划线 _ 开头。变量名中允许有数字，但变量名不能以数字开头。

```
 var home; 
```

在上面的语句中，变量 home 已定义但未初始化(没有赋值)。JavaScript 是松散类型的，所以 home 可以在任何时候被赋予任何类型的值。

JavaScript 变量可以在一条语句中声明和初始化:

```
var choice = 5;
var  home = “echo lake”; 
```

#### 样式

默认的 JavaScript 风格是使用 camel case: example，blueStallion。

#### 可变范围

如果变量是用 var 关键字定义的，那么它的作用域就被限制在封闭的代码块中。如果变量定义时没有使用 var 关键字，那么它就是一个全局变量。

具有全局作用域的变量:在所有函数之外声明的变量，不管有没有 var 关键字，总是具有全局作用域。

在下面的例子中，treehouse 是一个全局变量:

```
 function myfunc {                       
treehouse = “birch”;      
// treehouse is a global variable

 } 
```

#### 范围规则

1.  在所有函数之外声明的变量是全局的(即使使用了 var 关键字)2。在函数中用关键字 var 声明的变量的作用域被限制在封闭块或函数体中。
2.  在函数中没有使用 var 关键字定义的变量具有全局范围。
3.  全局变量可以在一个步骤中定义和初始化，如下所示:

```
 treehouse =  “bird”; 
```

#### 环境

JavaScript 程序的环境是它的变量值。标准浏览器环境标准浏览器环境包含一些有用的功能。这些是:
警告框
函数 Alert 打印它的参数值(在一个对话框中)。例子:

```
alert(“Hello”); 
```

确认对话框
确认对话框显示一个带有“是”和“否”选项的对话框。当用户按下按钮时，该函数返回 true 或 false。

```
confirm(“Save File?) 
```

提示对话框

提示功能会显示一个对话框，用户可以在其中给出答案。第一个参数是将呈现给用户的文本字符串。第二个参数是用户答案开始的字符串。

```
prompt(“What is your favorite color?”,  “my favorite color is:  ”) 
```

向用户呈现文本输入框。该函数返回第二个参数前的用户答案。

### JavaScript 数据类型

有 7 种基本的 JavaScript 数据类型:

1 .未定义的
2 .空值
3 .布尔型
4 .数字
5 .字符串
6 .BigInt
7。标志

并且:

目标

#### 类型自省

JavaScript 中的变量是松散类型的。也就是说，它们可以保存任何数据类型的值。

我们需要一种方法来确定变量保存的数据类型。该信息由 typeof 运算符提供。typeof 运算符以字符串形式返回变量包含的数据类型:

```
typeof 4.1; 
// “number”
typeof “hello world”;                        
//  “string”
 typeof myObject; 
// returns  “object” 
```

我们也可以使用语法:

```
typeof(my_var); 
```

typeof 运算符返回以下六个值之一:未定义、布尔、字符串、数字、对象或函数。

#### 未定义类型

在 Javascript 中，已定义但未初始化的变量被赋值为 undefined。如果对此变量调用 typeof 运算符，它将返回 undefined。

像 var home 这样的语句声明了 home 变量，但是没有给这个变量赋值。因此，在这种情况下，typeof(home)是未定义的，分配给 home 的值是“未定义的”。

测试 home == undefined 是一个有效的测试。

#### 空类型

null 类型只有一个值 null。当一个变量被定义并期望在以后保存一个
值时，该变量应该被赋予空值。这让我们可以执行诸如
的测试，如:

```
if ( car != null ) {
} 
```

#### 布尔数据类型

布尔数据类型有两个可能的值:true 或 false。这些与 0
或 1 不一样。
函数 Boolean()自动将变量转换为布尔值。
转换如下:

##### 布尔转换

| 数据类型值 | 转换为真值 | 转换为 false |
| --- | --- | --- |
| 线 | 非空字符串 | " "-空字符串 |
| 数字 | 任何非零数字 | 0 和 NaN |
| 目标 | 任何物体 | 空 |
| 不明确的 | 不适用的 | 不明确的 |
| 布尔型 | 真实的 | 错误的 |

Boolean()很重要，因为许多控制流语句隐式地使用它来测试真或假:

```
var message = 'hello world'
if Boolean(message) {
} 
```

### 数字

JavaScript 有一个用来表示数字的单一数字类型。
普通整数可以表示为:var iNum = 5；
十六进制数字有一个前导 0x : var iNum = 0x5FA3(不区分大小写)。

#### 浮点数

JavaScript 将任何带小数点的数字和小数点后的数字字符串视为浮点数。浮点数的长度是 64 位。
一位用于符号，11 位用于数字的小数部分。Thus52 位可用于表示整数。
数字可以用科学记数法表示如:2.998e8
即:2.998 * 10**8

##### 在 Javascript 中，一切都是对象，因此数字是有方法的对象。

#### 正负无穷大

Javascript 中可以表示的最大数字表示为:

号码。最大值

最小的数字表示为:

号码。最小值；

我们可以用:isFinite()来测试一个数是否在最大最小范围内。当一个数低于最低可能值时，它被赋予值:-无穷大，当它大于最大值时，它被表示为:无穷大。不能对这些值执行任何算术运算。

#### 楠

当执行算术运算并且期望得到一个数字时，当结果不是数字时(错误除外)，结果被设置为
NaN。NaN(不是一个
数)是执行非法算术运算(如除以 0)时得到的值。

NaN 不等于包括自身在内的任何数。

#### IsNaN()

函数 isNaN(x)
测试操作数是数字还是可以转换成
数字。因此 isNan("10 ")返回 false。isNaN('green ')返回 true。

#### 数字转换功能

有三个函数可以将各种数据类型的变量转换为数字:Number()、parseInt()和 parseFloat()。

#### 数字()

数字被设计成接受任意操作数。Number 函数将其参数转换为数字，例如:Number("56 ")，Number("blue ")。它的规则如下:

| 操作数 | 回收价值 |
| --- | --- |
| 布尔值:真或假 | 1 或 0 |
| 数字 | 返回操作数 |
| 空 | Zero |
| 不明确的 | 圆盘烤饼 |
| 只有数字的字符串 | 返回操作数 |
| 空字符串 | Zero |
| 字符串中的浮点数 | 浮点数 |
| 字符串中的有效十六进制数 | 小数 |
| 其他字符串 | 圆盘烤饼 |

#### parseInt()

parseInt 将字符串转换为整数或浮点表示形式。parseInt 的工作原理如下。字符串中的前导空格将被忽略。如果第一个字符不是+、-或数字，parseInt 将返回 NaN。parseInt()继续检查数字，直到到达字符串或非数字字符的末尾。然后它停止并返回结果。因此 parseInt("1234blue ")返回 1234。
parseInt 识别十六进制数字。因此 parse int(“0x fa”)被识别。
parseInt(" ")返回 NaN。
parseInt()可以接受一个可选的第二个参数，即在
转换中使用的基数:
parseInt("310 "，4)。这里的基数是 4。

##### parseFloat()

parseFloat 从字符串生成一个浮点数，直到到达一个非数字字符。它不识别除 10 以外的任何基数。

#### 字符串数据类型

字符串是用单引号或双引号括起来的字符序列。下面是
的一个例子:

```
var myvar = 'this is a string' 
```

Javascript 解释器不做任何字符串插值。
字符串中可以包含特殊字符，如果它们前面有反斜杠转义字符，例如:\ n–换行符:\ -反斜杠、\" -引号和\ t–tab。

#### 字符串长度

字符串的长度可以用 length 属性获得:“hello world”。长度

#### 字符串串联

+运算符可用于连接字符串。

```
“my ” + “new ” + “shoes”
// “my new shoes”
var my_str = “Shoes”;
var new_var = “my new ” + mystr; // “my new Shoes” 
```

在字符串加法中，当变量之一是数字时，在执行字符串加法之前，该数字被转换为字符串:

```
my_var = 3.5 + “2.0”; // “3.52.0” 
```

#### 查尔特

给定一个字符串 str，str.charAt(x)返回第 x 个位置的字符。字符索引从 0 开始。
当索引超出字符串末尾时，charAt 返回" "。

#### 切片

给定一个字符串 str，str.slice(x，y)返回从位置 x 开始的 y 个字符长的子字符串。
当长度参数 y 超出字符串的末尾时，slice 简单地截断 y 以扩展到字符串的末尾。
如果 slice 只有一个参数，它将返回从起始索引位置到字符串末尾的子字符串。

#### substr

str.substr(x，y)
提取从字符串的索引位置 x 开始向上的子字符串(但
不包括从索引位置 y 开始的字符串部分。

```
“hello world”.substr(2, 5); // llo 
```

#### 案例转换

函数 toUpperCase()将一个字符串转换成大写。比如:
toUpperCase(“你好”)。toLowerCase()函数将函数转换为小写。

#### 转换为字符串

函数 toString 将一个对象转换成一个字符串:

```
var val = 11;
val.toString // “11”
var val = true
val.toString // “true” 
```

此方法不适用于 null 或 undefined。如果不确定对象是否为空或未定义，请使用 String()。String(operand)如果操作数为 null，则返回“null”，如果操作数未定义，则返回“undefined”。toString 可以接受用于数字转换的基数参数。例如:167.toString(16)。

#### 字符串比较

字符串按字典顺序进行比较。大写字母总是小于小写字母。该实现使用 Unicode，它为每个字符分配一个数字。

食蚁兽>斑马//对

#### HTML 转义函数

escape 函数将 ASCII 字符串转换为 URL 编码格式。这编码了在 HTTP 中有特殊含义的
字符，如空格、冒号、斜线等。
var encodedStr = escape(" my string ")
对于非 ASCII 字符串，有必要使用:encodeURI 和 decodeURI。

#### 对象类型

使用新运算符创建自定义对象:

```
var obj = new Object(); 
```

之后，可以将属性和方法添加到 obj 中。所有对象都有 toString 方法，它返回对象的字符串表示。
对象也有 valueOf 方法，该方法返回对象的数字、布尔或字符串
表示。

#### 算术运算符

算术运算符是:*、/、%、+和-。
乘法和除法的优先级高于加法和减法。当
算术运算符具有相同的优先级时，它们从左到右应用。使用括号可以改变运算的
优先级。

#### 自动递增和自动递减

JavaScript 使用 C 的递增和递减运算符++和-。这些可以应用在变量之前或之后:++a，a++，- a，a -。前缀版本将在计算表达式之前递增或递减变量。后缀版本在计算表达式后递增或递减变量。这些运算符可用于数字、布尔值、字符串或对象。对于布尔值，true 转换为 1，false 转换为 0。
如果字符串可以转换为数字，那么首先将字符串转换为数字，然后应用递增或递减运算符。如果字符串不能转换为数字，则自动递增或递减操作的值将设置为 NaN。
自动递增和自动递减运算符可应用于对象。首先，使用 valueOf 运算符转换对象。如果操作数不能转换为数字，则结果为 NaN。

#### 一元运算符:-，+

一元运算符可以应用于数字、布尔值、字符串和对象。当
应用于字符串、布尔或对象时，首先使用 valueOf 函数将对象转换为数字，然后将一元运算符应用于结果。如果操作数不能转换为数字，则结果为 NaN。

#### 模数运算符

模运算符%是两个数相除后的余数。%与*和/具有相同的
优先级。

#### 常数

用常量修饰符
可以使变量只读

```
const var readonly = 3.5; 
```

#### 赋值操作

nvalue * = 3；//nvalue = nvalue * 3；
nvalue-= 5；
nvalue/= myvar；
nvalue %=模块 ii；

#### 逻辑运算符

逻辑运算符有:&&、||和！
逻辑运算符的优先级如下图所示，优先级从高到低(括号中的运算符优先级相等，从左到右求值):
！, !=，===，(<，>，&，&，||)

#### 一元！操作员

逻辑非运算符:！可以对任何操作数进行操作。首先将操作数转换为布尔值，然后对其求反。它翻转逻辑值。该运算符表现如下:

|  |  |
| --- | --- |
| 操作数是一个对象 | 返回 false |
| 操作数是空字符串 | 返回 true |
| 操作数是非空字符串 | 返回 false |
| 操作数是非零数字 | Zero |
| 操作数是无穷大 | 返回 false |
| 操作数为空 | 返回 true |
| 操作数是 NaN | 返回 true |
| 操作数是 NaN | 返回 true |
| 操作数未定义 | 返回 true |

#### 逻辑与

逻辑 AND 运算符用&表示。运算符可以接受任何类型的
操作数。结果可能不是布尔值。该操作符可能返回结果
而不计算整个表达式(短路计算)。
& &如果左侧的计算结果为假，将不计算右侧的表达式。& &是短路运行。
如果其中一个操作数不是布尔值，则适用以下评估规则:

1.  如果第一个操作数为 false，则返回 false。第二个操作数被忽略。
2.  如果两个操作数都为真，则返回真。
3.  如果第一个操作数为假，第二个操作数为真，则返回假
4.  如果第一个操作数是对象，则总是返回第二个操作数
5.  如果两个操作数都是对象，那么总是返回第二个对象
6.  如果任一对象为 null，则返回 null
7.  如果任一对象为 NaN，则返回 NaN
8.  如果任一对象未定义，则返回 undefined

#### 逻辑或运算符

逻辑 OR 运算符由||。如果两个操作数都是布尔值，那么只有当两个操作数都为假时,
运算符才返回假。

以下附加规则适用:

1.  如果第一个操作数是一个对象，则返回第一个对象
2.  如果第一个操作数的计算结果为假，则返回第二个操作数
3.  如果两个操作数都为空，则返回空
4.  如果两个操作数都是 NaN，则返回 NaN
5.  如果两个操作数都没有定义，则返回 undefined

#### 乘法运算符

乘法运算符有:*、/和%。如果其中一个操作数不是数字，那么
Number()函数用于执行类型转换。
以下规则适用:

1.对于数字操作数，普通规则适用，除了对于越界
结果，可能返回+无穷大或-无穷大。
2。如果无穷大乘以 0，则返回 NaN。
3。如果无穷大乘以一个非零的数，则返回+无穷大或-无穷大。
4。如果无穷大乘以无穷大，结果是无穷大

#### 除法运算符

除法运算符(/)将两个操作数相除。对于数字，执行普通除法，但如果结果超出界限，则返回+无穷大或-无穷大。如果
操作数之一不是数字，则 number()函数用于转换操作数。

以下特殊规则适用:

1.  如果一个或两个操作数都是 NaN，则返回 NaN
2.  如果无穷大除以无穷大，则返回 NaN
3.  如果 0 除以 0，则返回 NaN
4.  如果非零有限数被 0 除，则返回+无穷大或-无穷大

#### 模数运算符

模数运算符用%表示。如果其中一个操作数不是数字，则应用 number()函数将其转换为数字。

以下特殊规则适用:

1.  如果被除数是无限的，除数是有限的，则返回 NaN
2.  如果被除数是一个有限的数，除数为零，则返回 NaN
3.  如果无穷大除以无穷大，则返回 NaN
4.  如果被除数是一个有限的数，除数是无限的，那么被除数返回 5。如果被除数为零，除数为有限数，则返回 0

##### 加法运算符

如果两个操作数都是数字，那么加法的一般规则适用。以下特殊规则适用:

1.  如果任一操作数为 NaN，则结果为 NaN
2.  如果将-Infinity 加到+Infinity，则结果为 NaN
3.  如果-infinity 加上-infinity，则结果是+infinity
4.  如果+无穷大加到+无穷大，那么结果是+无穷大
5.  如果+0 加上-0，结果是+0
6.  如果-0 加上-0，则结果为-0

#### 字符串加法

如果两个操作数都是字符串，那么+将连接字符串。如果其中一个操作数是字符串，那么另一个操作数将被转换为字符串，并将两个字符串连接起来。这包括为不是字符串的操作数调用 toString()函数。

#### 减法运算符

对于普通的数字，减法遵循普通的规则。以下特殊规则适用:

1.如果任一操作数是 NaN，则结果为 NaN

1.  如果从+无穷大中减去+无穷大，则结果为 NaN
2.  如果从-infinity 减去-infinity，结果是 Nan
3.  如果从+无穷大中减去-无穷大，那么结果就是无穷大
4.  如果从-无穷大中减去+无穷大，那么结果就是无穷大
5.  如果从+0 减去-0，结果是-0
6.  如果从-0 减去-0，结果是+0
7.  如果任一操作数是字符串、布尔值、null 或未定义的，则使用 Number() 9 将其转换为数字。如果任一操作数是对象，则调用它的 valueOf()函数。如果对象没有 valueOf()函数，则调用 toString()函数，并将结果字符串转换为数字。

#### 关系运算符

关系运算符有: >、< , > =和<=. Relational expressions return true or false.
以下特殊规则适用:

1.  如果两个操作数都是数字，则进行普通比较
2.  如果两个操作数都是字符串，则按字典顺序比较字符串
3.  如果一个操作数是数字，另一个操作数将被转换为数字
4.  如果一个操作数是布尔值，它将被转换为数字
5.  如果一个操作数是对象，则使用 valueOf()将其转换为数字。如果 valueOf()不可用，则使用 toString()。

如果其中一个操作数是 NaN，那么结果将为假。对于字符串比较，将比较 ASCII 字符代码。因此，例如“Z”小于“a”。

#### 相等运算符

相等运算符为==。不等式运算符是！=.类型强制在进行相等或不相等测试之前执行。

应用以下规则:

其中一个操作数是布尔值将布尔值转换为数字(0 或 1)
一个操作数是字符串，另一个是数字
试图将字符串转换为数字
其中一个操作数是对象，另一个
不是使用 valueOf()转换对象 函数
null == undefined 返回 true
其中一个操作数是 NaN 返回 false
两个操作数都是对象只有当它们指向同一个
对象
“NaN”= = NaN false
NaN = = NaN false
Undefined = = 0 false

#### 同等和不同等运算符

同等运算符为===而不同等运算符为！==.这些运算符的工作原理类似于== and！=但在进行相等测试之前，不要执行任何类型转换。
由于不执行类型强制，null === undefined 为 false。

#### 条件运算符

条件运算符的形式为:

```
var result = (boolean expression) ? val1 : val2; 
```

如果布尔表达式为真，则值为 val1，否则为 val2。

#### 赋值运算符

赋值运算符的形式为:x = val。右边的值被分配给左边的变量。
可以做复合赋值如:x += 10，x *= 5，x /= 8，x %=6。

#### 逗号运算符

逗号运算符允许在一个语句中计算多个表达式:

```
x = 1, y = 7, z = 9; 
```

### 表情和语句

表达式是产生一些值的一段代码。语句是以分号结尾的一个或多个表达式:

```
1 + 5; 
```

块:块是一组用括号括起来的语句。

### 控制流程

#### 如果处理

这是基本的 if 语句:

```
if (some expression) {
} 
```

if 条件必须用大括号括起来。除非是一条语句，否则按条件计算的代码块必须用大括号括起来。

这是一个多路 if 测试:

```
if (some expression) {
}
else if (some expression) {
}
else if (some expression) {
}
else {
} 
```

#### 切换语句

这是一个多路开关测试，类似于 c 中的开关。其形式为:

```
switch (expression)
{
case first_test_condition:
statements;
[break;]
case second_test_condition:
statements;
[break;]
default:
statements;
} 
```

计算 switch 语句中的表达式。然后将其与第一个测试条件进行比较。如果匹配，则执行标签后面的语句。如果有中断，则处理终止。否则，计算下一个表达式。如果处理没有以 break 语句终止，则评估第二个测试条件，依此类推。如果没有找到匹配的大小写，那么如果缺省块存在，则执行缺省语句。要通过开关中断落差，匹配表达式必须有 break 语句。当两个或多个 case 语句执行同一个语句块时，我们可以使用简写:

```
case expression1:
case expression2:
case expression3:
statements; 
```

*重要提示*:switch 语句使用同等于运算符计算表达式。不执行类型强制。

开关示例

```
Switch (prompt(“what is the weather like”, “...”) {
case “rainy”:
alert(“bad weather');
break;
case “sunny”:
alert(“Nice weather")
break;
default:
alert(“not known”);
} 
```

#### 三元运算符

三元运算符的形式为:

```
var res = condition ? val1 : val2 
```

示例:

```
var my_var = 3
var q_var = my_var > 0 ? true : false 
```

#### While 循环

这个循环的形式是:

```
while (test) {
} 
```

测试条件必须用括号括起来，代码块必须用大括号括起来，除非它是一条语句。在调用测试之前，将使用 Boolean()方法转换测试条件。

示例:

```
var ctr = 1;
while (ctr < 10) {
ctr = ctr + 1;
} 
```

#### 做 While 循环

```
do {
} while ( expression ); 
```

循环测试必须用括号括起来。循环执行的代码块必须用大括号括起来。
do while 循环将至少执行一次循环体。

示例:

```
var ctr = 1;
do {
...
ctr = ctr + 1;
} while (ctr < 10); 
```

#### 为循环

for 循环的基本形式是:
for(初始化；条件；后循环表达式){
}

示例:

```
var val = 5;
for ( var i = 0; i < val; i++) {
} 
```

这个循环和 c 语言中的 for 循环是一样的。
循环测试在循环体执行之前进行。在执行完循环体的
之后，对循环后的表达式进行求值。

示例:

```
for (var ctr = 0; ctr < 10; ctr = ctr + 1) {
alert(ctr);
} 
```

#### 空循环语句

初始化、控制表达式和后循环表达式都是可选的。无限循环的形式如下:

```
for (;;)
{
...
} 
```

#### 标注报表

带标签的语句具有以下形式:

```
 label:
statements 
```

#### 中断并继续语句

break 语句中断循环:

```
var sum = 20
while (var index = 0; index < 100; index++) {
if (index == sum)
break;
} 
```

如果执行 break 语句，则不会执行后循环表达式。

#### 继续下一次迭代

```
for (var ctr = 0; ctr < 10; ctr = ctr + 1) {
continue;
} 
```

break 和 continue 都可以有标签参数。这对打破嵌套循环很有用:

```
for ( i = 0; i < 10; i++) {
for ( j = 0; j < 100; j++) {
...
break outermost;
}
}
outermost:
statements; 
```

#### 为在迭代器中

下面的形式对于遍历集合很有用:

```
for( property in some_collection)
{
} 
```

这个迭代器遍历集合的所有属性，对每个属性执行一次迭代器体。如果对象为空或未定义，将不会执行迭代器体。

示例:

```
for (token in ['red', blue]) {
....
} 
```

#### 同语句

一个例子如下:

```
with(some_object ) {
...
var finder = location;
} 
```

变量 location 被视为局部变量，如果在块中找不到它，则它被视为对象的特性，并在对象中搜索特性值。

在严格模式下不允许使用 with 语句，这被认为是不良做法。

### 功能

##### 功能和方法

一个方法属于一个对象，一个方法在一个对象上被调用。这个对象被称为接收者。
相比之下，在 JavaScript 中，函数是一个对象:my_func.toStr.
因为函数是对象，所以它们可以传递给其他函数并从
函数返回。

#### JavaScript 函数

JavaScript 函数定义如下:

```
function myFunction( a, b) {
var x;
return expression;
} 
```

函数由 function 关键字、函数名、圆括号中的参数列表
和函数体组成，函数体是括号中的语句集合。关键字函数是必需的，大括号也是必需的。
一个函数如果没有任何副作用，称为纯函数。
用 var 关键字在函数中定义的变量的作用域被限制在该函数中。在前面的函数示例中，变量 x 的范围被限制在函数体中。

#### 返回函数的值

一个函数可能有 return 关键字，后跟一个可选的表达式。当函数执行过程中遇到
return 关键字时，函数返回表达式的值。如果 return 关键字后面没有表达式，或者函数体中没有 return 关键字，则返回未定义的值。

#### 函数参数

例如，即使一个 JavaScript 函数有一个带三个参数的签名，它也可以不带参数或带任意数量的参数被调用。

当 JavaScript 函数被调用时，JavaScript 会创建一个名为 arguments 的数组。传递给函数的第一个参数可以作为参数[0]访问，第二个参数作为参数[1]访问，依此类推。

传递给函数的参数数量可以用 arguments.length 来确定。

命名参数只是 JavaScript 中的一个便利特性，因为可以使用参数数组来代替。

我们可以改变一个数组元素(如 arguments[3])，这将自动改变相应的命名参数。反之则不然。如果我们在函数体内改变了命名的
参数，那么相应的参数数组元素不会改变。

在严格模式下，如果试图更改 argument 元素，则会发生错误。

#### 重载功能

在 Javascript 中，函数名不能重载，因为函数本身没有签名。如果同一个函数名被定义了两次，那么 Javascript 将总是使用最后定义的函数。

#### 变量作用域和闭包

传递给函数的参数可以作为函数体内部的变量:

```
function add(x, y)
{
return (x + y);
} 
```

在函数体内定义的变量(带有 var 关键字)只在函数体内可见。

```
function mult(x)
{
var y = 3;
return (x*y);
} 
```

如果一个函数在它的主体中没有找到一个需要的变量，它会在封闭的作用域中寻找这个变量:

```
var
y = 7;
function add(x)
{
return (x + y);
} 
```

在这个例子中，因为 y 不存在于函数体中，所以它是从封闭作用域中选取的。实际上，在函数定义之上定义的所有变量都是可见的。
一个函数可以在其主体内定义另一个函数:

```
 function
parent(x) {
var y = 3;
function child()
{
return x*y;
}
child();
# call the child function
} 
```

子函数是在 parent()中定义的。child()的封闭范围是父函数的主体。变量 x 和 y 在 child 内部是可见的。

#### 词法范围

JavaScript 中的范围规则称为词法范围。

JavaScript 不是块范围的语言。大括号内的代码{...}不创建新范围。唯一能创建新作用域的是函数。

#### 函数返回函数

函数可以返回函数。考虑一下这个:

```
 function parent(x) {
var y = 5;
function child(z)
{
return x*y*z;
}
return child;
//return the child function
}
var childfunc = parent(11);
childfunc(10);
//call the child function 
```

需要理解的重要一点是，每当调用 childfunc()时，child()的封闭范围中的变量 x 和 y 仍然存在。这些上下文变量在一个范围对象中维护。这是一个封闭的例子。

#### 匿名函数

常规函数声明的形式为:

```
function bank_account() {
} 
```

当定义一个函数时，没有必要在函数关键字后面跟函数名:

```
var add = function(a, b) {
return a + b;
}; 
```

右边称为函数表达式。函数表达式也称为匿名函数。该函数被分配给变量 add。add 是对函数的引用。

我们可以如下执行匿名函数:

```
add(5, 5); 
```

#### 变量、范围和记忆

在 JavaScript 中，变量保存原始值或引用。原始值是:空、未定义、布尔、数字和字符串。引用是指一个对象。

注意:对象可以在运行时附加属性和方法。原始值不能在运行时附加属性和方法。

#### 复制变量

在 Javascript 中，当一个原始变量被赋给另一个变量(例如 x = y)时，变量 x 在堆上被分配新的内存，然后新的内存位置用 y 的值初始化，这被称为按值传递。然而，如果 y 是一个对象，那么赋值操作初始化 x，使其指向内存中与对象 y 相同的位置，这叫做引用传递。

#### 函数参数

函数参数通过值传递。然而对于对象，该函数接收到一个对该对象的
引用(即指向堆上对象的指针)。

#### 确定类型

typeof 运算符用于确定变量的类型。用法是:

```
typeof some_variable 
```

这将返回一个字符串形式的变量类型:
"null "，" undefined "，" boolean" "number "或" string "。如果变量是一个对象，那么 typeof 将返回 object。

对于对象，instanceOf 运算符可用于确定对象的类型:

```
some_variable instanceOf(ObjectType) 
```

instanceOf 将返回 true 或 false。

#### 块级范围

代码块是一些用大括号括起来的 JavaScript 语句。JavaScript 没有块级范围。这意味着可以在代码块外部访问代码块内部定义的变量。

在 JavaScript 中，只有两种类型的作用域。全局范围和功能范围。

#### 函数中的变量

如果在函数中声明的变量是用 var 关键字声明的，那么它就有局部作用域。如果变量声明时没有使用 var 关键字，则它具有全局范围。

#### 嵌套作用域

范围可以嵌套:

```
var color = blue
function func1() {
var color2 = “red”;
func2() {
myColor = color;
}
} 
```

这里有三个作用域:定义变量 color 的全局作用域、func1
函数作用域和 func2 函数作用域。当 func2 寻找变量 color 时，它首先搜索它的作用域。如果找不到该变量，它将搜索封闭范围 func1，然后继续向外搜索到全局范围。一旦在封闭范围内找到变量，搜索就会终止。

### 物体

在 JavaScript 中，对象是属性值对的无序集合。值可以是基本类型、对象或函数。属性名可以是字符串或数字。

#### 创建 JavaScript 对象

创建 JavaScript 对象有两种方法。

#### 对象构造方法

通过用 Object.new 创建一个空白对象，然后向该空白对象添加属性，可以创建一个 JavaScript 对象:

```
person = Object.new;
person.name = “jannat singh”;
person.age = 16;
person.speak = function() {
alert(this.name);
}; 
```

*new* 是一个对象构造器。除了 Object.new，我们还可以写:

```
person = new Object(); 
```

#### 对象字面法

构造对象的第二种技术是使用对象文字符号:

```
person =
{
name: “jannat singh”,
age: 16,
speak: function() {
alert(this.name),
};
} 
```

可以用:var obj = Object.new 或者用这个符号
符号:
创建一个空对象

```
person = {}; 
```

注意，在 person 的对象字面定义中，我们有一个 speak 属性，这些值是一个函数。person.speak 将执行该函数。

在函数内部，对象的其他属性可以作为 this.property 引用。

#### 拥有自己的属性

每个对象都有一个 hasOwnProperty 方法。该方法确定一个对象是否具有
某种属性:

```
person.hasOwnProperty(“age”);
person.hasOwnProperty(“address”);
// true
// false 
```

#### 对象创建模式:工厂模式

当必须创建大量对象时，上述对象创建技术很麻烦。工厂模式解决了这个问题。解决方案是指定一个函数，该函数根据传递给函数的属性创建一个对象:

```
function createPerson(name, age)
var obj = new Object;
obj.name = name;
obj.age = age;
var sayName = function() {
return this.name
};
return obj;
}; 
```

createPerson 函数可以创建多个相同类型的对象。

#### 对象创建:构造器模式

工厂模式的问题是创建的每个对象都是同一类型的对象。JavaScript 本身支持一些对象类型，比如:对象和数组。但是我们可能想要创建自定义类型的对象。构造器模式让我们可以创建这样的定制对象。

#### 考虑以下功能:

```
function Person(name, age) {
this.name = name;
this.age = age;
this.say = function sayName() {
return this.name;
};
}; 
```

函数 Person 实际上定义了一个新的数据类型(如字符串、数字、对象等)。)被叫人。

我们现在可以创建一个 Person 类型的新对象，如下所示:

```
jannat = new Person(“jannat singh”, 16); 
```

这将创建一个名为 jannat 的 Person 类型的对象。如果我们键入 jannat，返回值将是 Person。

该模式的操作如下:

当遇到 new 关键字时，JavaScript 创建一个新的空对象。JavaScript 接下来将其设置为指向新对象的内存位置。接下来，JavaScript 执行函数，然后返回这个。该值被分配给左侧–jannat。上面的模式创建了一个新的 Person 类型的对象。

*按照惯例，函数名是大小写合适的。*在函数体内，这被对新对象的引用所取代。

*函数不能有 return 语句。*

请注意，构造函数定义了一个属性，比如将函数作为值。

每个对象都有一个名为 constructor 的方法，它返回对象的类型。由此:

```
jannat.constructor === Person; // true 
```

构造函数模式的主要问题是，当调用构造函数时，对象中的每个函数都需要新的内存分配。

#### 公有和私有变量

默认情况下，对象的所有属性都是公共的(可以用 object.property 或 object["property"]来访问它们)。不能从对象外部访问私有变量。我们用:
声明一个私有变量

```
var myObject = {
var color: blue;
} 
```

这将颜色声明为对象的私有变量。

我们可以为返回私有变量的对象定义一个访问函数:

```
myObject.getColor = function() {
return this.color;
} 
```

类似地，对象中的方法可以用 var 关键字
声明为私有的

```
var myObject = {
var balance: function() {
7500;
};
} 
```

#### 按值存储和参照存储

在 Javascript 中，原始值是按值存储的。然而，对象是通过
引用存储的。比如:

```
var new_person = person; 
```

如果更改了 person.name，则 new_person.name 将反映新值。这是
，因为 new_person 是对 person 对象的引用。

#### 属性属性

对象中具有原始值(null、boolean、number、string、undefined)的每个属性都有三个附加属性:可配置、可枚举和可写。可配置属性确定是否可以删除或更改该属性。可枚举属性确定是否在 for-in 循环中返回该属性。可写属性确定是否可以写入该属性。默认情况下，所有这些属性都设置为 true。

#### 访问对象的属性

有两种方法可以访问对象的属性值:点操作符符号和数组符号:

```
var jannat = {
name: 'jannat',
age: 22
school: “UofT”
} 
```

name 属性可以通过以下方式访问: *jannat.name 或 jannat["name"]*

当属性不遵循 Javascript 命名
约定时，必须使用数组符号。[]符号的一个优点是括号可以包含一个变量:

```
var tag = “name”;
jannat[tag] // jannat 
```

试图读取未定义的属性将返回 undefined。

如果属性名不符合 Javascript 约定，那么它必须用引号括起来，并且必须使用数组符号:

```
myObject[“5”] = “a number'
myObject[“my color”] = 'blue' 
```

#### 改变对象的属性值

点运算符和数组符号可用于改变属性的值:

```
myObject.street= 'Katepwa'
myObject['street'] = 'Katepwa' 
```

*如果对象中不存在该属性，赋值将创建它。*

#### 删除一个属性

删除一个属性如下:

```
delete myObject.name
delete myObject['name'] 
```

#### 表情中的物体

数组运算符内的对象可以是表达式。JavaScript 会在做属性操作之前将其转换成字符串:

```
myObject[3+5] will be converted to myObject[“5”] 
```

#### 物体相等

考虑对象:*object 1 = { key:15 }*
object 1 实际上是指向内存中某个块的指针。

当我们把 object1 赋值给一个新对象:
*object2 = object1* 那么 object2 指向的同一个位置就是内存。此外:

*object 2 = = = object 1
//true*

如果我们将 object1 更改如下:*object 1 . key = 25*T2】那么: *object2.key = 25*

考虑:*object 3 = { key:25 }；然后:object3 == object1 // false*

只有当两个对象都指向内存中的同一个位置时，JavaScript 的==操作符才会返回 true。

#### 迭代对象属性

for in 运算符可用于迭代对象的所有可枚举属性:

```
for (prop in my_object) {
console.log(my_object[prop])
} 
```

这将打印出对象的所有属性值。对象的属性名可以打印出来:

*获取一个对象的属性*

```
for (prop in my_object) {
console.log(prop)
} 
```

### 原型

以下是 Person 类型对象的构造函数:

```
function Person() {
this.name: 'jannat
this.address: 'echo lake'
}; 
```

Person 上的大写只是约定俗成。我们也可以用小写的人称。对象 Person 称为构造函数。创建新的 person 对象的构造函数语法是:new person()。

所有构造函数都有一个名为 prototype 的属性。prototype 属性的值是对象。

我们可以如下设置 Person 这个原型对象的属性:

```
Person.prototype.name
= “jannat”
Person.prototype.address = “echo lake”
Person.prototype.sayName = function() {
alert(this.name);
}; 
```

请注意，原型属性 Person.prototype.sayName 是一个值为函数的属性。
我们也可以立即创建并设置 Person 构造函数
的原型的属性，如:

```
function Person(name, address) {
this.age = 25
this.country = 'Mars”
this.prototype.name = “jannat”
this.prototype.address = “echo lake”
this.prototype.sayName = function() {
alert(this.name);
};
}; 
```

我们可以如下创建一个新的人对象:

```
var person = new Person(); 
```

原型对象的重要性在于，在原型对象上定义的所有属性对于现有的人对象和任何其他新创建的人对象都是可用的。

此外，原型对象的属性由创建的 Person 实例共享(这样可以节省内存)。

#### 默认原型方法

默认情况下，每个原型对象都有一个名为 constructor 的默认属性。构造函数指向包含原型对象的函数对象:

```
Person.prototype.constructor == Person. 
```

当用 Person 构造函数创建一个新对象时，创建的实例维护一个指向原型构造函数值的指针。这个指针可以被引用**原型**

#### 备选原型语法

我们可以用文字符号为 Person 指定原型对象:

```
function Person() {
Person.prototype = {
name: 'jannat',
age: 22,
sayName: function() {
alert(this.name);
}
} 
```

完成后，Person . prototype . constructor 指向原型对象，而不是 Person。我们可以如下显式设置构造函数:

```
function Person()
Person.prototype = {
constructor: Person
name: 'jannat',
age: 22,
sayName: function() {
alert(this.name);
}
} 
```

#### 继承与原型

JavaScript 有原型继承。我们继承的对象称为原型。

考虑以下动物对象:

```
function Animal(name, numLegs) {
this.name = name;
this.numLegs = numLegs
sayName = function() {
console.log(this.name);

} 
```

接下来，我们构建一个企鹅类。

```
function Penguin(name) {
this.name = name;
this.numLegs = 2;
} 
```

企鹅是一种动物，因此应该继承动物类的所有属性。这是通过以下方式完成的:

```
Penguin.prototype = new Animal(); 
```

现在企鹅类继承了动物类的所有属性。下面使用了继承的 sayName 属性:

```
penguin = new Penguin(“linux”)
penguin.sayName(); 
```

假设我们接下来定义一个帝企鹅类:

```
function Emperor(habitat) {
this.habitat = habitat;
} 
```

创建帝企鹅的实例:

```
var emperor = new Emperor(“alaska”); 
```

接下来，我们确保 emporer 企鹅从企鹅那里继承:

```
Emperor.prototype = new Penguin(); 
```

既然皇帝是企鹅的后代，而企鹅是动物的后代；Javascript 将搜索继承树，寻找 sayName 函数。类层次结构的根是 Object。

#### 是协议类型

IsPrototypeOf 可以用来测试给定的原型是否是某个实例对象的原型:

```
Person.prototype.isPrototypeOf(Animal) 
```

#### getPrototypeOf()

getPrototypeOf()函数可用于检索特定对象实例的原型:

```
Object.getPrototypeOf(person)
// Person.prototype 
```

#### 原型、访问器和变异器

特定对象实例的属性可以按如下方式检索:

```
person1.name // “jannat” 
```

这里，person1 对象从对象的原型中检索了 name 属性。person1 对象包含一个指向原型的指针。

person1 对象可以按如下方式更改 name 的值:

```
person1.name = “kaur” 
```

其效果如下:name 属性被添加到 person1 实例中。原型属性名称的值不变。

现在，访问器 person1.name 获取“kaur”。对特定属性或功能的搜索从对象实例开始，并向上进行到原型对象。

我们可以删除人员 1 姓名属性:

```
delete person1.name 
```

现在，访问器 person1.name 从原型中获取值，因为 person1 对象实例没有属性 name。

#### 原型和 In 运算符

对象有自己的属性和继承的属性。自有属性是由用户在创建的对象上定义的属性。继承属性是从对象的原型继承的属性。

如果一个属性或它的原型有已命名的属性，那么操作符中的*返回 true。*

考虑:

```
function Person(name, address) {
this.prototype.name = “jannat”
this.prototype.address = “echo lake”
this.sayName = function() {
alert(this.name);
};
}; 
```

和

```
jannatkaur = new Person 
```

现在，jannatkaur 中的 *name 返回 true，因为 name 属性在原型中。
然而:
*jannat . hasownproperty(name)*返回 false，因为 name 出现在原型中，但不在 jannat 对象中。*

如果我们指定 jannatkaur.name = 'jyot '，则 name 成为 jannatkaur 对象的一个属性，并且 jannatkaur . hasownproperty(' jannat ')返回 true。名称仍然是 jannat 的财产。注意:jannatkaur 中的 name 也是真的。

#### 原型和 for-in 循环

for-in 循环的形式为:

```
for (var prop in jannat) {
} 
```

这将遍历 jannat 对象的所有属性，这些属性可以在 jannat 对象或其原型中枚举。默认情况下，用户在 jannat 对象上创建的所有属性都是可枚举的。

#### 获取可枚举属性列表

考虑:

```
function Person(name, address) {
this.prototype.name = “bugga”
this.prototype.address = “echo lake”
this.sayName = function() {
alert(this.name);
};
}; 
```

还有:

```
jannat = new Person
jannat.name = “jannat”
jannat.age = 22 
```

Object.keys 方法返回一个对象的可枚举属性，作为一个由
字符串组成的数组:

```
var
proplist = Object.keys(Person.prototype) 
```

这将返回数组:['姓名'，'年龄'，' sayName']

```
var proplist = Object.keys(jannat) 
```

这只返回 jannat 对象上不在原型上的属性:['name '，' age']

### 数组

数组是按数字索引的属性的有序列表。索引从零开始。JavaScript 数组可以动态增长。索引元素不必是连续的；索引不连续的数组称为稀疏数组。

数组元素不必属于同一类型，数组元素可以是数组也可以是对象。

### 用数组文字创建数组

创建数组最简单的方法是使用数组文字符号:

空数组创建如下:

```
var myArray = [] 
```

使用数组文字符号，可以在一个步骤中创建和初始化数组，如下所示:

```
var myArray = [“mail1”, 5, “mail3” ] 
```

所有数组都是整数索引，索引从 0 开始。
看这个数组: *var myArray = [ 5，，100]*
这里 myArray[0]的值为 5，myArray[1]未定义。

#### 用构造函数创建数组

构造器符号为:

```
var myArray = new Array(); 
```

这将创建一个空数组。

```
var myArray = new Array('jannat', “singh', 20) 
```

创建一个包含 3 个元素的数组。

符号 var myArray = new Array(10)只是为一个十元素数组预先分配空间。

#### 数组和 JavaScript 对象

所有数组都是 JavaScript 对象。

#### 读写数组值

括号运算符[]用于读写数组:

```
var myArray = [5, 0, 1, 2, 3]; //// initialize a five element array
myArray[1] = -1;
myArray[0] = 0; 
```

#### 稀疏阵列

这些是索引值不连续的数组。因此，数组的长度将大于元素的数量。数组的长度是最大索引值加 1。

```
var myArray = []
myArray[1000] = -1
// array length is 1001 
```

我们可以测试一个稀疏数组在索引值 I 处是否有值，如下:

```
i in mArray 
```

#### 数组长度

所有数组都有长度属性。这是它们区别于普通物体的唯一属性。对于非稀疏数组，长度是数组中元素的数量。对于稀疏数组，长度是最大索引值加 1。

考虑五元素数组:myArray = [0，1，2，3，4]

myArray.length = 5

现在，如果我们将数组长度设置为 2: myArray.length = 2

这将删除所有索引大于或等于长度值的元素:

```
myArray.length = 2;
// myArray = [0,1] 
```

设置 myArray.length = 1000 只是分配额外的空间(但不设置新的索引值)。

##### 推动和弹出

数组的原型对象包含 push 和 pop 方法。push 方法
将其参数推到数组的末尾。pop 方法从数组中弹出最后一个元素并返回:

```
myArray = [1,2,3];
myArray.push(“mail5”);
var last = myArray.pop;
// [1, 2, 3, “mail5']
// last = “mail5” 
```

#### 删除数组元素

使用删除运算符删除数组元素:

```
myArray = [1,2,3]
delete myArray[1] 
```

这样做的效果如下。数组的长度不会改变，数组索引也不会下移。myArray[1]现在有一个未定义的值。

#### 轮班

删除索引处的数组值，并通过减一来移动更高的索引。

#### 遍历 JavaScript 数组

for 循环用于循环访问数组。设马里为某数组:

```
for (var i = 0, i < mArray.length, i++) {
var value = mArray[i];
....
} 
```

### 迭代器方法

#### forEach 迭代器

foreach 迭代器将函数作为参数。对于每个数组元素，该函数通过三个参数调用，即数组元素的值、索引和数组本身:

```
var mArray = [ 'a', 'b', 'c' ];
var sum = 0;
mArray.forEach( function(value, index, mArray) {
sum = sum + val;
}
); 
```

如果只需要数组的值，请指定只有一个参数的函数。

#### 地图迭代器

map 迭代器将函数作为参数。该函数接收数组元素的值并返回值。迭代器使用这些返回值来构造一个新数组:

```
MArray = [1, 5, 10];
mArray.map(function(val) {
return val*val;
});
// [1, 25, 100] 
```

#### 过滤迭代器

过滤器迭代器返回数组的子集。迭代器将函数作为参数。
函数以数组值为自变量，返回 true 或 false(是一个
谓词函数)。如果返回值为真或者可以提升为真，则数组元素被添加到新的结果数组:

```
MArray = [1,2, 5, 6];
mArray.filter(function(val) {
return val < 3
});
// [1,2] 
```

#### every()和 some()迭代器

这些迭代器将谓词函数作为参数。谓词函数将数组的值作为参数。只有当谓词函数对所有迭代都返回 true 时，every()迭代器才返回 true。仅当谓词函数为至少一个迭代器值返回 true 时，any()迭代器才返回 true:

```
mArray = [1,2,5, 10];
mArray.every(function(val) {
val < 6;
});
// returns false 
```

#### 减少迭代器

reduce 迭代器对数组进行操作，并将数组简化为一个值:

```
mArray = [1,2,3,4]
mArray.reduce(function(init, array_value, index, array) {
}, init_val); 
```

reduce 函数有两个值:一个函数和一个可选的初始值。
函数接受一个初始值、一个数组值、一个可选索引值和一个可选数组。

在第一次调用 reduce 函数时，初始值(init_val)作为第一个参数传递给 reduce 函数。该函数返回一个累加值。
对于后续迭代，该函数接收该累加值。在最后一次迭代中，返回最终的累积值。
如果 reduce 函数的第二个参数未指定，则 reduce 函数的参数在初始迭代中使用数组的前两个值。

#### 多维数组

JavaScript 不支持带有如下符号的多维数组:

```
mArray[][] 
```

#### 对一个数组进行测试

谓词函数 isArray 测试变量是否是数组:

```
Array.isArray(mArray);
// true or false 
```

#### 加入

join 方法通过连接数组的所有
元素从数组元素创建一个字符串。元素由传递给 join 的参数分隔:

```
MyArray.join(“,”) 
```

如果没有传递参数，空格将用作字符串分隔符。

#### 分割

split 方法与 join 方法相反。它通过拆分一个字符串来创建一个数组。字符串用传递给方法的字符分割:

```
sentence.split(“ “) 
```

#### 搜索数组和索引

indexOf 在数组中搜索某个值的第一个匹配项，如果找到，则返回该值所在的索引。如果找不到该值，则返回-1。

#### 字符串和数组

在 Javascript 中，字符串是不可变的数组，因此可以用[]操作符访问字符串元素。

```
var str = “hello world”;
str[4] // o 
```

### 日期对象

日期对象可以被实例化如下:

```
var dateObject = new Date(year, month, day) 
```

日期对象也可以实例化如下:

```
var dateObject = new Date(year, month, day, hours, minutes, seconds) 
```

最后三个参数是可选的，如果没有指定则为零。

如果省略所有参数，date 对象将用今天的日期和时间初始化。

在内部，date 对象以毫秒*(自 1970 年午夜起)*表示纪元时间中的日期和时间。

访问器方法有:getFullYear()、getMonth()、getDay()、getDate()、getHours()、getMinutes()和 getSeconds()。除了 getDay()之外，所有这些方法都有 setter 方法。

日期可以用，> =来比较。==不起作用。

可以使用:getTimeZoneOffset()检索 date 对象的时区偏移量。

### Javascript 错误处理

当发生严重错误时，底层系统会抛出异常，例如，要写入的磁盘已满或网络连接不可用。软件应用程序也可能引发异常。在 Javascript 中，可以用 throw 语句抛出一个异常:

```
throw “network error”; 
```

也可以通过创建一个新的要抛出的对象来抛出错误:

```
throw new myErrorObject(“message string”); 
```

当异常发生时，Javascript 会引发一个错误对象。错误对象的内容通常可以用消息方法来检查。异常向上传播到应用程序的根，如果没有捕获和处理它，应用程序通常会崩溃。

异常在 try 和 catch 块中被捕获和处理:

```
 try {
}
catch (error) {
print error.message
}
finally {
} 
```

执行 try 块中的语句。如果抛出一个异常，那么它将被捕获并在 catch 块中处理。

try catch 块可以选择以 finally 块结束。无论是否发生异常，finally 块都会被执行。

### 高级 JavaScript 函数

在 JavaScript 中，函数的规范形式是:

```
function myFunction(a, b, c)
{
} 
```

函数是用关键字:function 定义的。函数体在花括号内。括号中的值是函数的参数。

Javascript 函数可以赋给变量，然后作为函数参数传递，由函数返回。

一个函数可以被指定为一个对象的属性:

```
var obj = new Object;
obj.speak = function sayHello() {
alert(“hello”);
} 
```

据说这个函数是对象 obj 的一个方法。

#### 匿名函数和函数文字

函数名不是必需的。函数名可以在函数
定义中缺失。比如:

```
var func = function(a,b) {
return a+b;
} 
```

这里定义了一个函数文字或匿名函数，并将其赋给了一个变量 func。

#### 嵌套函数

在 Javascript 中，一个函数可以在另一个函数中定义，然后由封闭函数调用:

```
function adder(x, y) {
function subtractor(a,b) {
return a-b;
}
z = subtractor(x, y);
return ( z + x + y);
} 
```

函数减法器在加法器函数内部定义，然后在加法器主体中调用。

Javascript 的作用域规则允许减法器函数读写封闭函数体中的变量以及加法器的参数。

#### 调用 JavaScript 函数

JavaScript 函数可以通过以下方式调用:

(一)函数
(二)方法
(三)作为构造函数
(四)带有调用和应用

#### 函数调用

一个 Javascript 函数被称为:myFunction(a，b)。参数 a 和 b 可以是表达式。

#### 从函数返回

函数在遇到 return 语句时返回。将返回附加到 return 关键字的表达式。如果函数不包含 return 关键字，或者如果值没有附加到 return 语句，则函数返回 undefined。

#### 方法调用

如果函数 myFunc(a，b)是对象 obj 的一个属性，那么它被调用为:
obj.myFunc(a，b)。可以用括号符号代替点符号:
obj[“my func”](https://dev.toa,b)

对于方法，关键字 this 是指函数是其属性的对象。对象(this)是方法的调用上下文，对象的所有属性对方法都是可用的。

**重要提示:**嵌套函数不会继承其包含方法的上下文。如果嵌套函数作为一个方法被调用，那么它就是被调用的对象。如果嵌套函数作为函数被调用，那么它没有上下文。

#### 构造函数调用

当一个函数以 new 关键字开头时，就会创建一个新对象，包括它的
原型对象:

```
var obj = new function adder(x, y) {
x+y; 
```

如果函数不包括参数，我们可以说:

```
var sum = new adder 
```

构造函数不能包含 return 关键字。

#### 功能参数

JavaScript 不计算传递的参数数量或参数类型。

当调用的 JavaScript 函数的参数少于函数
参数列表中的参数时，额外的参数被设置为未定义。

#### Varargs 函数

varargs 函数是一个可以接受可变数量参数的函数。传递给此类函数的参数可以从 arguments 数组中检索。传递给该函数的第一个参数是 arguments[0]，该函数接收的第 n 个参数是 arguments[n-1]。

传递给函数的参数数量是 arguments.length。

#### 分配功能

在 Javascript 中，可以将匿名函数赋给变量。考虑加法器功能:

```
function adder(x, y) {
return x+y;
} 
```

我们将函数分配给变量 sigma:

```
var sigma = adder 
```

其将函数加法器分配给变量 sigma。我们现在可以调用函数:

```
sigma(5,6) 
```

函数可以定义为对象属性:

```
obj.sigma = function(x, y) { return x+y; } 
```

注意，我们还没有定义函数名。这是一个**函数文字。**

#### 可变范围

Javascript 没有块范围的概念。在 Javascript 中，变量有函数作用域。如果变量是用 var 关键字定义的，那么函数内部的变量在函数外部是不可见的。但是这些变量对任何嵌套函数都是可见的。任何函数之外的变量总是全局变量。

**注意:**一个函数体内部的变量，如果没有用 var 关键字声明，那么它的作用域是全局的。

#### 作用域链

当定义一个函数时，Javascript 为该函数创建一个 scope 对象，该对象包含函数体中的所有变量和函数参数。然后，JavaScript 查看封装第一个函数的函数，并为第一个函数创建另一个范围对象，该对象由封装函数体中的变量及其参数组成。

这个过程一直持续到所有包络函数都被考虑。JavaScript 然后为函数创建一个范围对象，由所有全局变量组成。

请注意，函数的作用域是在定义时确定的。不是当它被调用的时候。这种构造函数作用域的方式被称为**词法作用域。**

当 JavaScript 必须解析变量的值时，它首先在函数体和函数的参数中寻找变量。如果在此范围内没有找到该变量，它将检查封闭范围对象中的变量。这个过程一直持续到变量被解析或者达到全局范围(任何函数之外的变量)。如果变量未被解析，则会引发异常。

### 关闭

从上面可以清楚地看到，一个 JavaScript 函数不仅包括函数体中的代码，还包括函数的**作用域链**。由于词法作用域，这个作用域链是在定义函数时确定的。

功能代码和作用域链的组合被称为**闭包。**简而言之，闭包包括函数体中的所有变量、它的参数以及函数范围链中函数外部的变量。

请看这个闭包的例子:

```
var scope = “global”,
function checkScope() {
var scope = “local”;
function f () {
return scope;
}
return f();
}
checkscope(); 
```

这里，return 语句执行嵌套函数 f 并返回它的值。根据词法范围规则，该值是“本地”的。

现在考虑下面的例子:

```
var scope = “global”,
function checkScope() {
var scope = “local”;
function f () {
return scope;
}
return f;
}
checkscope()(); 
```

在本例中，checkscope 返回函数 f。然后执行 f(check scope()())。根据词法范围规则，checkscope()()返回“local”。checkscope()()搜索其作用域链，作用域链在声明函数时是固定的。

**注意**f 的堆栈操作。在执行 checkscope 后，函数 checkScope 已经退出，因此函数体中的所有变量都已从堆栈中取出。然而，f 的作用域链由堆上的对象组成，任何这样的对象都不会被删除，直到对它的所有引用都消失。

考虑下面的函数:

```
function adder() {
var x = 1;
return {
sum: sum() { return ++x; }
reset: reset() { return (x = 1) ;
}
} 
```

加法器返回一个对象。现在调用加法器:

```
var a = adder();
var b = adder(); 
```

考虑:

```
a.sum
//2
b.sum
//2 not 3
a.reset
//0
b.sum
//4 
```

这里，每次调用 adder()都创建了一个新的作用域链，每个作用域链中的变量 x 都是该作用域链的私有变量。

#### 闭包和本

每个方法都有一个 this 值。它是接收者对象。一个函数(没有明确地绑定到一个对象)有一个 this 值设置为全局对象。嵌套函数不能访问其外部函数的 this 值，除非 this 值被赋给变量。

#### 调用和应用

每个 JavaScript 函数都是一个对象，并具有属性:调用和应用。调用和应用将 JavaScript 对象作为参数。设 f 是函数，m 是对象:

```
f.call(m) 
```

这样做的效果是隐式地让 f 成为对象 m 的一个属性，然后调用 f:也就是:

```
f.call(m) is equivalent to m.f 
```

注意，调用改变了函数的作用域链。作用域链从对象 m 开始。此外，m 的所有属性都可以被 f 访问。

调用可以在 object 参数后接受任意数量的参数。这些自变量将是 f:
的自变量

```
f.call(m, x, y) is equivalent to m.f(x, y) 
```

apply 与 call 相同，只是参数放在一个数组中。因此 apply 有一个对象参数和一个可选的参数数组。

#### bind()函数

设 o 是对象，f 是函数。我们将 f 绑定到 o 如下:

```
var g = f.bind(o) 
```

这使得 g 成为 o 的一个方法，并将 g 的 This 值设置为对象 o。用参数调用 g，用这些参数调用 f，其中 f 具有对象 o 的上下文。

#### 局部应用(或涂抹)

考虑以下情况:

```
var f = function(x, y, z) { return (x + y + z) }
var g = f.bind(o, 1) 
```

现在 g 的作用域链从对象 o 开始，参数 1 绑定到函数 f 的
参数 x，我们可以如下调用 g:

```
g( 2, 3) this is equivalent to the call : f(1, 2, 3) 
```

### 功能编程:高阶功能

高阶函数是以一个或多个函数作为参数并返回一个函数的函数。普通函数是一阶函数。函数式编程关注的是操作高阶函数。

传统上，在高阶函数中，函数参数首先被写入。

这是一个将匿名函数动作作为参数的函数示例:

```
function forEach(array, action) {
for (var i = 0; i < array.length; i++)
action(array[i]);
}
// action is some arbitrary function
function sum(numbers) {
var total = 0;
forEach(numbers, function (number) {
total += number;
});
return total;
}
show(sum([1, 10, 100])); 
```

在上面的示例中，为每个传递了一个匿名函数，该函数更新 total，total 是函数 sum 范围内的一个变量。

### JavaScript 中的函数上下文绑定

语言中的 this 或 self 结构为变量或方法提供了上下文。这个上下文定义了变量或方法的范围。也就是说，上下文或范围决定了什么对方法或变量是可见的。

在大多数语言中，变量或方法的上下文是在声明变量或方法时确定的。例如，在 Ruby 中，self 总是指包含变量或方法的对象。JavaScript 却不是这种情况。特别是，JavaScript 提供了一个绑定函数，让我们在运行时定义对象的变量或方法的上下文。因此 JavaScript 提供了后期绑定。

在 JavaScript 中，有四种调用函数的模式:

函数调用模式
方法调用模式
构造函数调用模式
应用调用模式

每种模式都有不同的上下文。

考虑普通的函数调用:

```
var p = 90
var func = function(x) {
// ...
};
func(p) 
```

该函数调用的上下文 *(this)* 是浏览器中的全局环境或窗口。

现在考虑一个具有函数的对象:

```
var unicorns = {
func: function(x) { // ... }
};
var fun = unicorns.func;
fun(x) 
```

这里 unicorns.func(z)将对象作为它的上下文，但是变量 fun 将全局环境作为它的上下文。这个例子表明函数的上下文依赖于函数的调用方式。

#### 方法调用模式

查看前面的模式，方法调用 unicorns.func 的上下文是对象 unicorns。

#### 构造函数调用模式

考虑下面的构造函数:

```
function Wizard() {
this.castSpell = function() { return "KABOOM"; };
}
var merlin = new Wizard(); // this is set to an empty object {}. Returns `this`
implicitly.
merlin.castSpell() // returns "KABOOM"; 
```

“调用新向导”会隐式返回此消息。梅林指向新创建的空对象。上下文是新的对象。

#### 应用调用模式

我们还可以用 apply 和 call 函数为函数调用提供上下文。apply 和 call 为被调用的函数提供上下文。这些调用的模式如下:

**调用**——它将一个上下文作为第一个参数。其余的参数是传递给被这样调用的函数的
参数。
**apply**——它将一个上下文作为第一个参数，将被调用函数的参数数组
作为第二个参数。

请看这个例子:

```
function addAndSetX(a, b) {
this.x += a + b;
}
var obj1 = { x: 1, y: 2 };
// this = obj1, obj1 after call = { x: 3, y : 2
addAndSetX.call(obj1, 1, 1);
// It is the same as:
// addAndSetX.apply(obj1, [1, 1]); 
```

#### 绑定功能

JavaScript 中的有界函数是绑定到给定上下文的函数。这意味着无论你如何打电话，电话的上下文将保持不变。唯一的例外是 new 操作符，它总是返回新创建对象的上下文。

为了从常规函数中创建有界函数，使用了 bind 方法。bind 方法将您希望将函数绑定到的上下文作为第一个参数。其余的参数是将总是传递给这样一个函数的任何参数。bind 方法返回一个有界函数。

示例:

```
function add(x, y) {
this.result += x + y;
}
var computation1 = { result: 0 };
var boundedAdd = add.bind(computation1);
// the context `this` is set to the object `computation1`
// computation1 after the call to the bounded function: { result: 3 }
// result refers to the result in the context
boundedAdd(1, 2);
// always pass a 2 to the bounded function
var boundedAddPlusTwo = add.bind(computation1, 2);
boundedAddPlusTwo(4); 
```

不能通过在函数上使用*调用*或*应用*来改变绑定函数的上下文。

### 浏览器中的 JavaScript

#### 窗口对象

window 对象是指浏览器窗口或框架。简称为
窗口。窗口对象是客户端 Javascript 程序的全局对象。它位于范围链的顶端。它有各种属性，可以在没有限定符窗口的情况下引用。

窗口对象也有一些重要的方法，如:alert()、setTimeout()等。

#### 窗口属性

每个窗口对象都有 window 属性，该属性引用窗口对象本身。

#### 窗口定时器属性

*window.setTimeout()* 注册一个函数，该函数在给定的时间过后被调用:

```
setTimeout(“showList”, 5000) 
```

5000 毫秒后调用*显示列表*功能。

下面是另一个例子:

```
setTimeout(function() {
location.href = '/upgrade'
}, 400); 
```

*setInterval()* 函数的工作方式完全相同，只是在每个时间间隔后会重复调用该函数。

对于 *setTimeout()* 和 *setInterval()* ，HTML5 规范允许在前两个参数之后向函数传递额外的参数。

#### 浏览器定位和导航

窗口对象有一个*位置*属性。窗口位置属性 *(window.location)* 的值是一个附加了属性的位置对象。 *window.location* 是指浏览器窗口中显示的 URL。

*window.location.href* 是一个字符串，表示窗口中显示的页面的完整 URL。这将填充地址栏并执行 url *(HTTP)。*

*window.location* 还有其他属性，比如:*协议、主机、主机名、端口、路径名、搜索和哈希。*

assign 方法在窗口中加载一个新文档:

*window . assign("[http://IBM . com "](http://ibm.com%E2%80%9D))*

URL 可以是绝对或相对 URL。

*replace()* 方法类似，但它从历史记录中删除当前页面，这样后退按钮就不会返回到遍历过的页面。

*assign()* 和 *replace()* 可以带一个片段 url ("#mark ")。这将导致浏览器将页面滚动到指定位置。

#### 属性

*window.location.search* 属性设置或返回 URL 的查询部分。假设网址是 www.jannat.singh.com/?name=jannat%surname=singh 的

然后 location.search 会返回？name=jannat&surname=singh。

新的 URL 查询字符串可以通过以下方式设置:

location.search = "？name=jyoti&surname=singh "

#### 浏览历史

窗口对象包含一个历史属性 *(window.history)* 。 *window.history* 是一个物体。
*window . history . forward()*和 *window.history.back()* 遍历显示匹配页面的浏览历史。
*window . history . go(+-n)*从当前位置跳转到浏览器历史+或-页面中的一个页面。

#### 打开和关闭窗户

使用 window.open()在浏览器中打开一个新窗口。open()接受四个可选的
参数:

导航到的 URL
窗口名称
新窗口属性的逗号分隔列表(以字符串形式):
"width=400，height=350，status=yes，resizable=yes"
浏览器可以忽略此参数
一个布尔值，它指示该 URL 是否应该替换 windows 历史中当前的
URL。仅当窗口被命名为
时才适用

*window.open()* 返回已经创建的窗口对象。

用 *window.close(w)* 关闭一个窗口，其中 w 是一个窗口对象。

### 脚本 JavaScript

窗口对象最重要的属性之一是文档。这是指窗口中以树状结构表示的文档。

#### 脚本标签

Javascript 可以嵌入到 HTML 页面中，或者外部 Javascript 文件可以包含在 HTML 页面中。这是通过脚本标记完成的。脚本标签的形式为:

```
<script>
</script> 
```

我们可以在开始和结束标记之间放置 JavaScript 语句。

属性: *type="text/javascript"* 不需要包含在脚本标记中，因为该类型是自动假定的。为了与旧的浏览器兼容，可以将它设置为脚本标签的一个属性。

#### 包括外部 JavaScript 文件

外部 JavaScript 文件包含在带有 src 属性的 HTML 页面中:

```
<script src=”externalFile.js” ></script> 
```

src 属性可以是完整的 URL。当包含外部文件时，不能在开始和结束标记之间放置 JavaScript。
新的浏览器理解自结束标签:

```
<script src=”externalFile.js” /> 
```

#### 如何处理带有 Javascript 的 HTML 页面

解释器从顶部开始解释页面。当遇到*标签时，它会停止，直到 JavaScript 被解释并加载到内存中。一旦遇到*标签，页面渲染就开始了。**

 ***标签可以放在 HTML 页面的*部分或*部分的任何地方。出于上述原因，最好将 JavaScript 放在*部分的底部。****

 ***#### 延期属性

defer 属性是可选的，可以添加到脚本标记中。它仅适用于要包含在页面中的外部脚本。该属性指示浏览器引擎在页面显示后下载外部 JavaScript 文件。其效果类似于将脚本标签放在*部分的末尾。* 

```
<script type=”text/javascript” defer>
</script> 
```

*具有 defer 属性的标签按照遇到的顺序加载(在页面加载完成后)。*

 *#### Asych 属性

该属性在一个线程中下载外部 JavaScript 文件，并在另一个线程中继续处理文件的其余部分。

#### noscript 标签

可以在 body 部分中指定 noscripT 标记。标签可以包含任何 HTML。如果浏览器不支持 JavaScript 或者关闭了 JavaScript，标签的内容将被呈现。

### 文档对象模型

#### getElementById

如果你想获得或改变一个 HTML 元素的值，给这个 HTML 元素一个 id。然后可以用 getElementById 方法访问该元素。这是文档对象的一个方法。

#### 访问和更改 HTML 元素

假设我们有 DOM 元素

```
<p id=”som”>hello</p> 
```

我们可以用
来访问 *hello* 元素

```
var el = document.getElementById(“som”).innerHTML 
```

我们可以用
来改变这个元素

```
document.getElementById(“som”).innerHTML = 'hello world” 
```

#### 在页面上滚动到给定的 ID

```
document.getElementById('trade-notice').scrollIntoView(); 
```

##### 访问和更改表单元素

假设我们有以下形式:

```
<p type='text' id='myText' />
<input type='button' onclick='notEmpty()' value='Form Checker' /> 
```

我们用:
获得对 HTML 元素的引用

```
 ref = document.getElementById('myText'); 
```

该方法将 HTML 元素 id 作为参数，并返回对
HTML 元素的引用。为了获得 HTML 表单元素的值，我们需要:

```
val = document.getElementById('myText').value;
// or ref.value 
```

#### 测试复选框是否被勾选

```
<script type="text/javascript">
function validate() {
if (document.getElementById('remember').checked) {
alert("checked");
} else {
alert("You didn't check it! Let me check it for you.");
}
}
</script> 
```

#### 访问非表单 HTML 元素的值

每个 HTML 元素都有一个 innerHTML 属性，它是 HTML 标记和文本
，文本位于元素的开始和结束标记之间。

考虑下面的 HTML:

```
<p id='modern'>Hello World </p> 
```

我们如下访问文本 Hello World:

```
 val = document.getElementById('modern').innerHTML; 
```

#### 改变 HTML 元素的文本

你也可以用 innerHTML 方法改变 HTML 元素的文本:

```
<p id=”header”>Hello </p> 
```

下面的 JavaScript 将 H1 标记的值更改为“BYE”。

```
<script type=”text/javascript”>
document.getElementById(“header”).innerHTML=”BYE”;
</script> 
```

#### 获取、设置或删除一个 HTML 元素的属性

假设我们想要获取 HTML 元素的 src 属性的值，该元素的 ID 为' image':

```
< img id=”image” src=”pic1.jpeg”> </img> 
```

下面的 Javascript 代码可以做到这一点:

```
val = document.getElementById(“image”).getAttribute('src'); 
```

如果属性不存在，getAttribute 将返回一个空字符串。

假设我们想要设置属性 src。下面的 Javascript 代码实现了这一点:

```
document.getElementById(“image”).setAttribute('src', 'newpic.jpeg'); 
```

属性 src 可通过
作为属性移除

```
document.getElementById(“image”).removeAttribute('src' ); 
```

### 创建一个 JavaScript 弹出窗口

弹出窗口将出现在一个 div 或 span 元素中，所以为它创建一个 CSS 样式:

```
<style type="text/css">
.popup {
position:absolute; right:0; bottom:-80;
width: 400px;
border: 1px solid black;
padding: 10px;
background: white;
font-family: Times New Roman;
font-weight: 200;
text-align: left;
z-index: -2;
visibility: hidden;
}
</style> 
```

**注意，visibility 属性是隐藏的。**

接下来创建两个 JavaScript 函数来处理显示:

```
<script type=”text/javascript”>
function ShowPop(id) {
document.getElementBy(id).style.visibility = "visible";
}
function HidePop(id)
{
document.getElementBy(Id).style.visibility = "hidden";
}
</script> 
```

现在我们将弹出菜单附加到一个链接:

```
<td>
<a href="#;" onMouseover="ShowPop('message_div');" on
Mouseout="HidePop('message_div');" > Click Here </a>
</td> 
```

接下来用 id 消息创建一个 span 或 div 元素:

```
<div id="message_div" class='popup'>
lorem ipsum factum ....
</div> 
```

#### 获取一个标签的值

标签不同于表单元素，表单元素的值是用。运算符符号。

```
<td id=”pay”>Pay No</td> 
```

获取标签 id 的值:

```
document.pay 
```

设置标签的值:

```
document.pay = “hello world” 
```

#### 访问表单

可通过以下方式访问表格:

```
document.order.payno 
```

文档是文档对象。order 是表单的 id 或名称标识符。payno 是表单元素的名称。该元素的值为:

```
document.order.payno.value 
```

#### 获取表单复选框的值

```
if (document.getElementById('atr60DefaultColor').checked) {
pref.atr60DefaultColor = 1;
}
else {
pref.atr60DefaultColor = 0;
} 
```

#### 设置复选框的值

```
document.getElementById(“id”).checked = [ true | false] 
```

#### 获取表单文本框的值

创建 JavaScript 函数:

```
<script type =”text/javascript”>
function get_text_value() {
var txt = document.getElementById('txt_element');
// view the value of the test element
alert(txt.value);
}
</script> 
```

HTML 片段是:

```
<tr onMouseout="get_text_value();">
<td>
<%= f.text_field 'bill_name', :id => 'bill_name' %>
</td>
</tr> 
```

#### 设置一个文本的值

创建 javaScript 函数:

```
function set_value() {
var val1 = document.getElementById('valA');
var val2 = document.getElementById('valB');
val1.value = val2.value;
} 
```

你也可以这样做:

```
id_box = Document.getElementById(“ida”)
id_box.value = “hello” 
```

#### 获取下拉框中的选定值，在另一个中设置选定值

下拉框

创建 JavaScript 函数

```
function dropdown-box_selector()
{
var dropdown_box_1 = document.getElementById('dropdownbox');
// get the selected index
var selIndex = dropdown_box_1.selectedIndex;
// Get The Selected Value
var selValue = dropdown_box_1.options[selIndex].value;
// Identify another dropdown box
var dropdown_box_2 = document.getElementById('another_dropdown');
// Set it's selected index value to the same value as the first dropdown box
dropdown_box_2.options[selIndex].selected = true;
} 
```

这是对应的 HTML 片段:

```
<td onblur=”dropdownbox_selector;”>
<%= f.select 'ship_state_prov', state_options,:id => 'dropdownbox' , %>
</td> 
```

#### 隐藏或显示一个元素

显示或隐藏特定的 HTML 元素:

```
document.getElementById('for_delivery').style.visibility = 'hidden';
document.getElementById('for_delivery').style.visibility = 'visible';
document.getElementById('for_delivery').style.display = 'inline'; 
```

#### Javascript 后退按钮

下面的代码创建了一个支持 Javascript 的后退按钮，可以返回到上一页:

```
<a href=”#” onClick=”history.go(-1)”>Back</a>
<input type=button value=”Back” onClick=”history.go(-1)”> 
```

可以用-n 替换-1，以向后遍历 n 页。

### 时间和日期算术

*纪元*现在时间:

```
var now = (new Date).getTime()/1000; 
```

### ES6 语法

在 ES6 之前，声明变量的唯一方式是使用 var 关键字。现在有几种方法来声明变量。

#### const 关键字

常数是不能改变的变量。在变量被声明并且
被赋值后，它不能被变异。试图改变它将抛出一个异常。

```
const pizza = true
pizza = false
//exception thrown 
```

#### 让关键字

在 JavaScript 中，花括号分隔代码块。在 ES6 之前，块内定义的变量在块外是可见的，前提是花括号不是函数定义的一部分。

在 ES6 中，通过使用 let 关键字:
定义变量，可以将变量定义为只在代码块中可见

```
var topic = "JavaScript"
if (topic) {
let topic = "React"
// React
console.log('block', topic)
}
console.log('global', topic) // Javascript 
```

#### 模板字符串

ES6 使用${variable}语法启用字符串插值:

```
console.log(`${lastName}, ${firstName} ${middleName}`) 
```

Javascript 解释器会将变量*的名字、* *的姓氏*和*的中间名*的值代入字符串。

请注意，使用反斜线代替引号来分隔字符串。

支持字符串内表达式插值:

```
var str = `JavaScript first appeared ${a+b} years ago. Crazy!` 
```

也可以调用函数:

```
function fn() { return "I am a result. Rarr"; }
var str = `foo ${fn()} bar` 
```

ES6 风格的字符串支持空格和换行符。这允许自然地书写多行字符串:

```
var x =
`Hello ${firstName},
Thanks for ordering ${qty} tickets to ${event}.
Order Details
${firstName} ${middleName} ${lastName}
${qty} x $${price} = $${qty*price} to ${event}
You can pick your tickets up at will call 30 minutes before
the show.
Thanks,
${ticketAgent}` 
```

### 功能默认参数

默认参数包含在 ES6 规范中。这意味着，如果调用函数时没有为参数提供值，那么将使用默认值。

```
function logActivity(name="p.singh", activity="cs") {
console.log( `${name} loves ${activity}` )
} 
```

如果用: *logActivity("jannat")* 调用 *logActivity* ，那么函数将使用“cs”作为 Activity 的值。

#### 箭头功能

箭头函数缩短了匿名函数的语法。使用箭头函数，您可以在不使用 function 关键字的情况下创建函数。您也不必使用 return 关键字。
ES6 之前的匿名函数具有以下形式:

```
var locator = function(address) {
return `my location is ${address}`
}
console.log( locator("bc") 
```

对于箭头样式的功能，这可以简化为:

```
var locator = (address) => `my location is ${address}` 
```

如果只有一个参数，则不需要括号。

通过用大括号
将
体括起来，箭头函数可以在函数体中有多行

```
var locator = (address) => {
address = ‘’;
`my location is ${address}`;
} 
```

#### 运输 ES6

并不是所有的网络浏览器都支持 ES6，即使是支持 ES6 的浏览器也不是什么都支持。确保您的 ES6 代码能够工作的唯一方法是在浏览器中运行之前将其转换为 ES5 代码。这个过程被称为传输。巴别尔是一个穿越者。

#### 析构数组和对象的赋值

析构是一个 JavaScript 表达式，它可以将数组中的值或对象中的属性提取到不同的变量中。也就是说，我们可以从数组和对象中提取数据，并将它们赋给变量。
在 ES6 之前，如果我们想从一个数组中提取值，我们将如下进行:

```
var introduction = ["Hello", "I" , "am", "jannat"];
var greeting = introduction[0];
var name = introduction[3];
console.log(greeting);
console.log(name);
//"Hello"
//"jannat” 
```

借助 ES6，我们可以做到以下几点:

```
[greet, me] = introduction
console.log(greet)
console.log(me)
// “hello”
// “I” 
```

变量 greet 和 me 不需要事先声明。

假设我们只需要数组的第一个和第四个元素。我们做:

```
[x,,,y] = ["Hello", "I" , "am", "jannat"]
console.log(x)
console.log(y)
// “I”
// “jannat” 
```

我们用逗号跳过不需要的数组元素。

我们也可以将数组中的一些元素赋给变量，将数组的其余部分赋给变量(它将是 off 类型数组)。

```
var [greeting, ...intro] = ["Hello", "I" , "am", "jannat"];
console.log(greeting);//"Hello"
console.log(intro);//["I", "am", "jannat"] 
```

注意这三个点。

```
var sandwich = {
bread: "dutch crunch",
meat: "tuna",
cheese: "swiss",
toppings: ["lettuce", "tomato", "mustard"]
} 
```

在 ES6 之前，我们将使用
*sandwich.bread* 和 *sandwich.meat.* 提取对象 *bread* 和 *meat* 的值。使用 ES6，我们可以这样做:

##### 默认值

如果从数组中提取的值是未定义的，可以将默认值赋给变量。

```
var[greeting = "hi",name = "jannat"] = ["hello"];
console.log(greeting);
console.log(name);
//"Hello"
//"jannat" 
```

可以用 ES6 写成:

```
({bread}) → console.log(sandwich.bread); 
```

#### 破坏物体

在对象析构中，我们希望从对象中提取数据并赋给新的
变量。

在 ES6 之前，我们会做:

```
var person = {name: "jannat", country: "canada", job: "Developer"};
var name = person.name;
var country = person.country;
var job = person.job;
console.log(name);
//"jannat"
console.log(country);
console.log(job);
//"canada"
//Developer" 
```

有了 ES6，我们可以:

```
var person = {name: "jannat", country: "canada", job: "Developer"}
var {name, country, job} = person;
console.log(name);
console.log(country);
console.log(job);
//"jannat"
//"canada"
//Developer" 
```

左侧对象中的变量应该与对象 person 中的属性键同名。如果名称不同，我们将得到 *undefined。*

请注意，var 是必需的。如果它丢失了，我们将得到一个异常。解释器会认为大括号是一个代码块。所以如果 var 不存在，do:

```
({name, country, job} = person); 
```

如果我们想把一个对象的值赋给一个新的变量，而不是使用属性的名字，我们会这样做:

```
var person = {name: "jannat", country: "canada", job: "Developer"}
var {name: foo, job: bar} = person;
console.log(foo);//"jannat"
console.log(bar);//"Developer" 
```

默认值也可以在对象析构中使用，以防变量在它想要从中提取数据的对象
中
未定义

```
var person = {name: "jannat", country: "canada", job: "Developer"}
var {name=’jannat’, friend=”annie”} = person;
console.log(name);
console.log(friend);
//"jannat"
//"annie"
var {name:foo =’jannat’, friend:bar =”annie”} = person;
console.log(foo);
console.log(bar);
//"jannat"
//"annie" 
```

#### 增强的对象文字量

通过对象文字增强，我们可以将全局变量或作用域中的变量转换成对象:

```
var name = "Tallac"
var elevation = 9738
// constructing an object literal the old way:
{ name: “Talloc”, elevation: 9738 }
// the ES6 way
{ name, elevation } 
```

JavaScript 解释器检查属性键是否有相应的变量名，并将该变量值赋给属性。注意如果没有变量与定义的属性键同名，我们将得到一个异常。

对象中的函数可以写得更简洁:

在 ES6 之前，我们会写:

```
 {
hello: ‘hi’,
greet: function() {
return alert(hello);
}
} 
```

在 ES6 中，这可以写成:

```
{
hello: ‘hi’,
greet() {
return alert(hello);
}
} 
```

#### 计算对象文字量

访问对象属性时，有两种方法可以指定键:点符号和括号符号。括号符号允许我们使用表达式来访问属性。计算属性名允许我们编写一个用方括号括起来的表达式，而不是常规的属性名。表达式计算出的值将成为属性名。这意味着我们可以这样做:

```
var name = "make";
const laptop = {

}
console.log(laptop.make);//"Apple" 
```

name 的值被计算为 make，并被用作
属性的名称。

我们还可以做:

```
var name = "make";
var i = 0;
const laptop = {

}
console.log(laptop.make1);//"Apple"
console.log(laptop.make2);//"Dell"
console.log(laptop.make3);//"HP" 
```

#### 传播算子

扩展操作符(...some_variable)允许一个表达式在另一个表达式(尤其是数组)内内联展开。如果没有 spread 运算符，我们有:

```
var middle = [3, 4];
var arr = [1, 2, middle, 5, 6];
console.log(arr);
// [1, 2, [3, 4], 5, 6] 
```

让我们使用**展开**操作符:

```
var middle = [3, 4];
var arr = [1, 2, ...middle, 5, 6];
console.log(arr);
// [1, 2, 3, 4, 5, 6] 
```

再比如:

```
 var arr = ['a', 'b', 'c'];
var arr2 = ['d', 'e', 'f'];
arr = [...arr, ...arr2];
console.log(arr);
// ['a', 'b', 'c', 'd', 'e', 'f'] 
```

#### 字符串到数组

spread 运算符可用于将字符串转换为数组:

```
var str = "hello";
var chars = [...str];
console.log(chars);
// ['h', 'e',' l',' l', 'o'] 
```

**新阵副本未引用**

```
var arr = ['a', 'b', 'c'];
var arr2 = [...arr];
console.log(arr2);
// ['a', 'b', 'c'] 
```

arr2 不是 arr 的引用。

### 承诺

Promises 是一种 ES6 技术，用于降低异步编程的复杂性。
首先创建一个承诺对象:

```
var mypromise = new Promise(function(resolve, reject) {
// asynch code block
// call resolve() if task successfully completed
if success {
resolve(phone)
}
else {
// call reject() if task has failed
reject(reason)
}
}) 
```

promise 构造函数接收一个匿名回调函数作为参数。这个
函数有两个函数作为参数:一个解析函数和一个拒绝函数。匿名函数的主体是将要运行的异步代码。如果异步任务执行的结果成功，则执行 resolve 函数，否则执行 reject 函数。无论哪种情况，回调函数的参数都是由异步任务返回的。

异步任务完成后，promise 对象的 then 方法被执行:

```
myPromise.then(
function(success) {
},
function(error) {
console.log('Error ' + error)
} 
```

然后接收两个匿名函数。如果异步任务成功，则运行 then()中的第一个函数，如果失败，则运行第二个函数。我们也可以使用 catch()方法来处理错误。

第一个函数接收的参数是 promise 对象中 resolve 函数的参数。第二个函数接收的参数是 promise 对象中 reject 函数的参数。

承诺是可以连锁的。当 success 函数返回一个新的
promise 对象时，就会出现这种情况。

这里有一个例子:

```
myPromise
.then(function (result) {
return new promise object;
})
.then(function (result) {
return another promise object;
})
.then(function (result) {
return something;
}, function (err) {
// Something in the above chain went wrong?
// Print reject output.
console.error(err);
}); 
```

### JavaScript 类

ES6 为 Javascript 引入了传统的继承和类。类别定义如下:

```
class Rectangle {
constructor(height, width) {
this.height = height;
this.width = width;
}
// Getter
getArea() {
return this.calcArea();
}
// Method
calcArea() {
return this.height * this.width;
}
} 
```

*构造函数*方法是一种特殊的方法，用于初始化从类中创建的对象。*这个*是指从类中创建的对象。类中的函数称为方法。

一旦我们创建了一个类，您就可以使用 new 关键字:
创建该类的一个新实例

```
var rect = new Rectangle(5, 6) 
```

这将创建一个名为 *rect* 的新矩形对象，高度为 5，宽度为 6。

可以用点运算符
调用类实例 *rect* 中的方法

```
var area = rect.getArea() 
```

#### 静态方法

当类方法前面有 static 关键字时，这将在类中定义一个静态方法。静态方法在没有实例化(创建)定义它们的类的情况下被调用，而且不能通过类实例被调用。在下面的例子中，我们定义了一个静态方法，然后调用它。

```
class Point {
constructor(x, y) {
this.x = x;
this.y = y;
}
static distance(a, b) {
const dx = a.x - b.x;
const dy = a.y - b.y;
return Math.hypot(dx, dy);
}
}
console.log(Point.distance(p1, p2)); 
```

#### 小类

extends 关键字在类声明或类表达式中用于创建一个类作为另一个类的子类。

```
class Animal {
constructor(name, age) {
this.name = name;
this.age = age
}
getage() {
return age
}
speak() {
console.log(this.name + ' makes a noise.');
}
}
class Dog extends Animal {
// the age method is inherited from Animal
speak() {
console.log(this.name + ' barks.');
}
}
var d = new Dog('fido', 5);
d.speak(); // fido barks. 
```

#### 超级

**super** 关键字用于调用对象的父对象(超类)上的函数。在上面的例子中，Animal 是 Dog 类的超类。

```
class Cat {
constructor(name) {
this.name = name;
}
speak() {
console.log(this.name + ' makes a noise.');
}
}
class Lion extends Cat {
speak() {
super.speak(); //speak() method in the superclass is called
console.log(this.name + ' roars.');
}
}
var l = new Lion('Fuzzy');
l.speak();
// Fuzzy makes a noise, Fuzzy roars. 
```

### ES6 模块

JavaScript 模块是包含变量和代码的文件。直到最近，使用模块化 JavaScript 的唯一方法是合并一个可以处理导入和导出模块的库。现在，有了 ES6，JavaScript 本身支持模块。

除非导出，否则 Javascript 模块中的所有内容都是模块私有的。

export 关键字用于导出将在
另一个模块中使用的任何 JavaScript 类型。

```
Sometimes you may want to export only one variable from a module. In these cases you
can use export default: 
```

const freel = new Expedition()
导出默认 freel

```
 Modules are consumed in other JavaScript files using the **import** statement. Modules with multiple exports can take advantage of object destructuring. Modules that use export default are imported into a single variable. 
```

从'导入{打印，日志}。/mymodulefile '
import freel from '。/mt-freel'

```
 We can scope module variables locally under different variable names: 
```

从'导入{ print as p，log as l }。/mymodulefile '

#### CommonJS

CommonJS 是 Node.js 所有版本都支持的模块模式，你仍然可以在 Babel 和 webpack 中使用这些模块。对于 CommonJS，使用**模块导出 JavaScript 对象**

```
mymodulefile.js
const print(message) =>log(message, new Date())
const log(message, timestamp) =>console.log(“now”)
module.exports = { print, log} 
```

**模块**通过**需求**功能导入:

` `const { log, print } = require ( './mymodulefile')`

`*******