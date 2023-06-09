# JavaScript 数据类型和运算符

> 原文：<https://dev.to/howtocodejs/javascript-data-types--operators-3i0i>

[![image](img/4d9e2771c0ce4f5aefa6fba8ce4c27cb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VwtxwTGO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g9b0ju4n5r2gtt7tppta.png)

你也可以在 howtocodejs.com 身上找到这一课。您可以使用我们的实时 JavaScript 代码编辑器 Lex 进行编码。

打开你的 Twitter 或 Instagram 账户，你会看到一个登录屏幕，提示你输入信息。当您输入用户名和密码时，您就已经输入了数据。你填写了一份调查，你喜欢一个帖子，你在亚马逊上订购了十个坐立不安纺纱机——所有这些都是数据。

在 JavaScript 中，这些数据分为三组:

### 1)编号:101010

数字就是你一生都知道的数字...1，50，22.3，5…整数，小数，分数。

JavaScript 在数字方面非常友好，因为您不必指定数字的类型。我们称这种行为**为非类型化**。JavaScript 是非类型化的，因为确定一个数字是整数还是小数(浮点数)是由该语言的解释器负责的。

你为什么不试着在编辑器中输入`typeof 5;`并点击运行按钮。你应该得到`'number'`。另外，请注意分号。它们就像英语中的句号。确保将它们放在每个语句或表达式的末尾。

```
typeof 5; 
```

Enter fullscreen mode Exit fullscreen mode

> 亲提示:`typeof 5`是一个语句。您编写了 JavaScript 代码，并期望得到一个值作为回报。这就是陈述的意义所在。如果你把`undefined`作为一个值，你很可能有一个表达式。

你也可以试试这个很酷的工具:

```
console.log(typeof 5); 
```

Enter fullscreen mode Exit fullscreen mode

> Pro 提示:`console.log()`是一个将 JavaScript 代码打印到控制台的 web 工具。每个网络浏览器都有一个你可以访问的控制台。“网络工具”构成了所谓的网络应用编程接口。把 API 想象成一套让你的工作变得更加容易的工具要容易得多。在 Windows 和 Linux 上，按`CTRL+SHIFT+J`访问控制台。在 Mac 上按下`Command + Option + J`。

还有两个特别值得一提的数字:`Infinity`和`NaN`。

1.无穷大是最大的数字量。很简单，它是无限的。

```
console.log(typeof Infinity); // > 'number'
console.log(typeof -Infinity); // > 'number'
console.log(1/ 0); // Infinity 
```

Enter fullscreen mode Exit fullscreen mode

2.`NaN`(不是数字)是当你试图对非数字
执行不兼容的算术运算时会出现的错误

```
console.log(typeof NaN); // > 'number'
console.log(1/ 0); // Infinity 
```

Enter fullscreen mode Exit fullscreen mode

### 2)弦乐:“你好”

字符串只是文本字段。甚至你现在正在读的单词也形成了一个字符串。为了把这些词括起来，我们用引号。请记住，字符串不仅限于连续的句子。

在 JavaScript 中，这也是一个字符串:`"123";`

```
typeof "123";// > 'string'
typeof "hello world"; // 'string' 
```

Enter fullscreen mode Exit fullscreen mode

字符串可以是双精度的、单精度的和反勾的。

```
 "hello world";
 'hello world';
 `hello world`; 
```

Enter fullscreen mode Exit fullscreen mode

双 qoute 和单 qoute 可能看起来相似，但双 qoute 更安全，因为它们在单词定界方面做得更好。

举个例子:

```
 "I'm Sally"; // Correct
 'I'm Sally'; // incorrect 
```

Enter fullscreen mode Exit fullscreen mode

反勾号允许您创建多行:

```
 `My Amazing Poem:
  Sally sells
  sea shells
  by the sea shore`; 
```

Enter fullscreen mode Exit fullscreen mode

如果您想对 double 或 single qoutes 做同样的事情，您将需要使用一个转义符`/n`

```
 "My Amazing Poem:/nSally sells/nsea shells/nby the sea shore"; 
```

Enter fullscreen mode Exit fullscreen mode

反斜线也允许所谓的**字符串插值** :

```
let name = 'Ron';
`I am ${name}`; // > 'I am Ron' 
```

Enter fullscreen mode Exit fullscreen mode

如果您想用 double 或 single qoutes 得到相同的结果，您需要将变量添加到字符串中。这叫做**字符串串联**。我们用加号运算符将字符串粘合在一起。

```
let name = 'Ron';
'I am' + name; // > 'I amRon'
'I am' + "  " + name; // > 'I am Ron' 
```

Enter fullscreen mode Exit fullscreen mode

### 3)布尔型:真，假

不要让这个名字把你搞糊涂了。它与数学家乔治·布尔同名。布尔只有两个值:true 和 false。

```
typeof true; // > 'boolean'
typeof false; // > 'boolean' 
```

Enter fullscreen mode Exit fullscreen mode

正如您将会知道的，当涉及到向我们的程序添加逻辑时，这些是重要的数据类型。只有这两个值，您可以创建一个复杂的循环和条件系统。

但是我们不要想太多。我们将在另一个时间探索条件和循环的深度。

### 4)空

Null 没有任何意义。如果不希望变量有类型或值，可以有意将变量设置为 null。

```
let empty = null; 
```

Enter fullscreen mode Exit fullscreen mode

### 5)未定义

未定义不是错误。这是一种数据类型，它只是简单地声明了一个变量还没有被声明。

```
//this is an expression
let learning = 'cool' // > undefined;

//=============
//this is an expression
let learning = 'cool'
//this is a statement
learning; // > 'cool' 
```

Enter fullscreen mode Exit fullscreen mode

对于函数，如果不显式使用 return 语句，将返回 undefined。

```
 function adder(){
  2 + 2;
}
adder(); // > undefined

//========
function adder(){
  return 2 + 2;
}
adder(); // > 4 
```

Enter fullscreen mode Exit fullscreen mode

### 6)符号

符号类型是最新进入 JavaScript 语言的基本类型。为了更好地理解符号，我们需要探索 JavaScript 对象

## 奖金数据类型

有六种“原始”数据类型，但是还有一种称为 object 的额外数据类型。这是所有数据类型的始祖。毫不夸张地说。事实上，你可以有把握地说，除了 null 和 undefined 之外，每一种原始数据类型都是对象。

这里有证据:

```
 typeof(Number.prototype); // > 'object'
 typeof(String.prototype); // > 'object'
 typeof(Boolean.prototype); // > 'object'
 typeof(Symbol.prototype); // >'object' 
```

Enter fullscreen mode Exit fullscreen mode

你所看到的是内置对象，它们的方法允许你对原始数据类型做一些很酷的事情，比如:

```
"hello".toUpperCase(); // > "HELLO"
123.toString(); // > '123' 
```

Enter fullscreen mode Exit fullscreen mode

原型是怎么回事？抓紧你的饮料，因为大多数，不是全部，物体都是功能。原因如下:

```
 /*
 See the uppercase? By convention, this is how
 you declare an object in JavaScript.
You can clearly see that it's still a function.
  */
function Number() {

}

typeof Number; // > 'function'

/*
By accessing the prototype, we're asking to see its parent.
*/
typeof Number.prototype; // >'object' 
```

Enter fullscreen mode Exit fullscreen mode

知道了所有这些，我们可以得出结论，在一开始就有对象。JavaScript 中的其他东西都是 Object 的后代。

更基本的对象称为对象文字，它保存一组数据。是这么写的:

```
{siteName: "howtocodejs", url:"https://howtocodejs.com", description: "Learn JavaScript the Easy Way"}; 
```

Enter fullscreen mode Exit fullscreen mode

关于物体，我们可以说的还有很多。现在，让我们来看看是什么让这些数据变得有价值。

## 运算符

如果你不能对数据做任何事情，数据有什么用？这就是运营商的用武之地。每种数据类型(数字、字符串、布尔型)都有一组操作符，您可以用它们来想出解决问题的绝妙方法。

事实上，如果你一直在编码，你一直在使用一个忍者操作符。

如果您猜对了`typeof`运算符，那么您答对了。它评估数据的类型。这种类型的运算符称为一元运算符。想想 uni，或者 *uno* ，或者 one。它只对单个值起作用。对两个值进行运算的运算符称为二元运算符。

```
2 + 2; //  the plus operator is binary
-4; //  in this context, the minus operator is unary. 
```

Enter fullscreen mode Exit fullscreen mode

作为一名 JavaScript 开发人员，有四种重要的操作符，它们是

### 1)算术运算符

#### 加法(+) -

**编号:**

```
1234 + 4579; // > 5813 
```

Enter fullscreen mode Exit fullscreen mode

**字符串:**

```
"hello" + "Jerry"; // > 'helloJerry' 
```

Enter fullscreen mode Exit fullscreen mode

你可以添加字符串。对此有一个奇特的术语叫做字符串连接。如果您在代码编辑器中尝试这样做，您可能会注意到这两个字符串是如何组合在一起的。我们可以通过在中间添加一个空字符串来解决这个问题。

```
"hello" + "  " + "Jerry"; // > 'hello Jerry' 
```

Enter fullscreen mode Exit fullscreen mode

**布尔:**

```
true + false; // > 1 
```

Enter fullscreen mode Exit fullscreen mode

对布尔值执行算术运算实际上会返回一个值。在这种情况下，值 1 不是普通的 1。它是一个按位 1。用计算机的语言来说，这可以解释为真。所以，我们可以得出结论，真加假等于真。

为什么会这样呢？一旦我们把真假转换成我们电脑实际看到的，你就明白了。

```
//Beep boop bop...
true: 1
false: 0

result:1 + 0 or 0 + 1 = 1 
```

Enter fullscreen mode Exit fullscreen mode

#### 减法(-) -

**编号:** `1234 - 1234;`

**字符串:** `NaN`

> 注意:NaN(不是一个数字)是当你试图减去字符串值时得到的错误。

**布尔:** `true - false; `或`false - true;`

#### 除法(/)

**编号:** `1234 / 1234;`

**字符串:** `NaN`

**布尔:** `true / false;`或`false/true;`

#### 乘法(\*\) -

**编号:** `1234 * 1234;`

**字符串:** `NaN`

**布尔:** `true * false;`或`false * true;`

#### 模数(%) -

这个 cool 运算符告诉我们两个值相除的余数。

**编号:** `10%3;`

**字符串:** `NaN`

**布尔:** `true % false;`或`false % true;`

#### 增量(++)

`++`是对任何值加 1 的一种奇特说法。把增量器放在哪里很重要。哦，顺便说一下，我们现在需要变量。如果变量中没有保存 10，JavaScript 的解释器就不能读取`++10`。为什么？因为加号，加号运算符就是我们所说的语法糖。这是为了让开发人员的生活更轻松而创造的，因为事实证明我们非常懒惰。我们不再说 10 + 1，而是放弃加 1。因为加号，加号在技术上不是一个真正的算术运算符，你需要定义一个变量以便你不会得到错误。

在我们开始变量课之前，试着摆弄一下它们。将此输入代码编辑器:

```
var cookies = 5;
console.log(cookies++);// > 5
console.log(++cookies);// > 7 
```

Enter fullscreen mode Exit fullscreen mode

> 亲提示:`var cookies = 5;`叫做表达式。您定义了 cookie 的价值，但没有询问它的价值。正如您之前所了解的，`cookies;`是一个语句。

那么…为什么我们没有得到我们期望的价值呢？？？

嗯，在`++`之前写变量，在它被增加之前给我们原始值，反之亦然。

可以这样想:我们向面包师要了 5 块饼干，他才知道我们想在订单上再加一块(饼干)(++)。

我们收到一张收据，上面写着我们点了五个，但是当我们要求再来一个时，面包师跑回来给我们再拿一个(所以，我们现在有 6 个饼干了)。

面包师回来了，但是我们又要了一个(++)(饼干)。

最后，当我们要求我们的饼干，我们的总数是 7 饼干。

谈到递增和递减，JavaScript 的解释器是个可怜的面包师。

#### 减量(-) -

**编号:** `-- number`

**字符串:** `NaN`

**布尔:** `--true`

### 2)赋值运算符

#### 等于(=)

```
var favoriteSoda = 'Coke'; 
```

Enter fullscreen mode Exit fullscreen mode

这是另一个简单的例子，展示了变量的作用。在这种情况下，等号和你在数学中使用的等号是不同的。你现在明白了，我们在比较运算符中使用双等号是有原因的。单个 equals 仅仅意味着您希望将一个特定的数据类型赋给自己创建的变量名。使用的运算符称为赋值运算符。

#### 加号等于(+=)

原来程序员比你想象的更懒。是的，还有更多算术捷径。

假设您有`var score = 5;`，您想将分数增加 6，而不是增加 1。

通常你会写，`score = score + 6;`

对于加号等于(+=)，你只需要把它写成，`score += 6;`

为什么不尝试使用不同的算术运算符呢？

```
score *= 6;
score -= 6;
score /= 6; 
```

Enter fullscreen mode Exit fullscreen mode

### 3)比较运算符

```
//this isn't code =)
equals: ==, 
not equal: !=,
greater: >,
less: <,
greater than or equal: >=,
less than or equal: <=, 
```

Enter fullscreen mode Exit fullscreen mode

比较运算符返回布尔值(真或假)。没有它们，我们就不会拥有所有可用的复杂应用程序。

还有一种特殊的三重等于`===`。这将检查以确保类型也是相同的。

试试这个:`3 == '3';`。你拿到了`true`吧？JavaScript 忽略我们的字符串`'3'`会导致一些不必要的错误。要解决这个问题，请添加另一个 equals。现在你应该得到假的。这是因为三重等于也确保了类型完全相同。

**编号:** `1 === 2;`

**字符串:** `'string' === 'string';`

**布尔:** `false != true;`

#### 额外收获:比较运算符和假设

大多数比较运算符都用在条件语句中。所以，让我们来研究一下 if/else 语句。

```
if('you feel overwhelmed by this new topic'){
 'Do not worry. Sometimes the best way to learn is to try and fail'
}else {
 "Let's get this show on the road!"
} 
```

Enter fullscreen mode Exit fullscreen mode

请尝试使用这个真正的 if/else 语句。

```
if(2==3){
 console.log('correctomundo');
}else {
 console.log('wrooong');
} 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:if 语句的结尾没有分号。
> 
> 专业提示:从任何以括号结尾的语句或表达式中排除分号。确保在 if/else 语句中使用不同的数据类型。您甚至可以使用到目前为止已经学过的所有操作符。

### 4)逻辑运算符

#### &&(和)，||(或)，！(不是)

逻辑运算符允许我们增加条件语句的复杂性。实际上，如果你想最大限度地控制一个条件，你应该使用&&因为所有的条件都必须满足才能为真。相反，如果您希望条件更具包容性，您应该使用逻辑 OR，因为只有一个条件必须为真才能获得真的返回值。

让我们看看&&和||操作符的工作情况。

```
//&&
if(2==2&&3==3&&3==2){
 console.log('correctomundo');
}else {
console.log('wrooong');
}

//||

if(2==2||3==3||3==2){
 console.log('correctomundo');
}else {
console.log('wrooong');
} 
```

Enter fullscreen mode Exit fullscreen mode

你看到发生了什么吗？真的很酷。并且，从左边开始，拼命寻找第一个假值。

或者从左边开始，并满足于第一个“真”值。

如果我们把这些操作员想象成盲目的嗅探器，我们会变得非常有创造力。让我们检查 OR 操作符，因为它确实是动态的。

假设我们想要创建一个欢迎框，打印用户的社交媒体名称。我们虚构的网站只接受 Twitter、Instagram、脸书和 Whatsapp 用户名。

问题是我们不确定用户会输入哪个社交媒体名称。他们可能输入一个、两个或根本不输入。

我们可以用 OR 运算符解决这个问题。让我们假设一个用户刚刚填写了一个表单，现在我们从一个 Ajax 请求中得到响应。

```
 //This is a make believe user. Variables are being used for simplicity's sake
let instaName = null,
fbName = null,
twitterName = '@cachememesoutside',
whatsappName =  null;
defaultName = 'add a social name, plox'

let socialName = (instaName || fbName || twitterName || whatsappName || defaultName);  

console.log(socialName); // >'@cachememesoutside' 
```

Enter fullscreen mode Exit fullscreen mode

Null 和 OR 是一个有效的组合。Null 返回一个 falsey 值，因此逻辑操作符将继续寻找 true 值，这些值在我们的社会名称中不是空的。

如果想证明 null 为假，试试这个:

```
console.log(!!null) // > false 
```

Enter fullscreen mode Exit fullscreen mode

`!!` not 运算符将数据类型转换为布尔值。

的！简单地给出相反的布尔值。

```
!true; // >false
!false; // >true 
```

Enter fullscreen mode Exit fullscreen mode

#### 短路

我们也可以**短路**OR 操作符。假设我们想提示用户输入一个社会名称或其他！！！我们可以试试这个。

```
let instaName = null,
fbName = null,
twitterName = null,
whatsappName =  null,
errorMessage;

//socialName will be null
let socialName = (instaName || fbName || twitterName || whatsappName );

//you must wrap the right side of the OR operator
socialName || (errorMessage = "put a darn social name in, man");

console.log(errorMessage); // > 'put a darn social name in, man' 
```

Enter fullscreen mode Exit fullscreen mode

短路意味着我们找到通向一个值的最短路径。如果左侧始终为真，则不会触发错误消息。如果我们网站的用户总是输入至少一个社交名，我们的错误消息就会一直短路。

## 总结

我们覆盖了...

1)六种原始数据类型:

*   用线串
*   数字
*   布尔运算
*   标志
*   不明确的
*   空

2)一种额外的数据类型:

*   目标

3)四个运算符:

*   算术:`+,-,/...`
*   分配:`=, +=,-=...`
*   对比:`<, >=, != ...`
*   逻辑:`&&, ||, !`

### 挑战:

安吉拉，一个虚构的同事，仔细看了看我们的代码，说有另一种方法来写短路。我们可以使用三元运算符。

她向我们展示了如何使用它

```
 (true ? 'true':'false'); // > 'true'
 (false ? 'true':'false');// > 'false' 
```

Enter fullscreen mode Exit fullscreen mode

> TODO:尝试重写短路表达式。如果有社交名，应该恭喜用户。如果没有，也许我们不应该对他们大喊大叫。写一条清晰的错误信息，告诉用户你希望他们做什么。

```
 let instaName = null,
 fbName = null,
 twitterName = null,
 whatsappName =  null,
 errorMessage;

 let socialName = (instaName || fbName || twitterName || whatsappName );

 socialName || (errorMessage = "put a darn social name in, man");  //Rewrite me! 
```

Enter fullscreen mode Exit fullscreen mode