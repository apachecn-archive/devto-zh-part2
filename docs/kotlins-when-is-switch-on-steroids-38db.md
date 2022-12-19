# 科特林氏症是什么时候转变的，靠的是类固醇

> 原文：<https://dev.to/tedhagos/kotlins-when-is-switch-on-steroids-38db>

Kotlin 没有一个 *switch* 语句，但是它有一个构造时的*。它看起来很像*开关*，但它更有冲击力。最简单的形式是，它可以这样实现* 

```
val d = Date()
 val c = Calendar.getInstance()
 val day = c.get(Calendar.DAY_OF_WEEK)

 when (day) {
   1 -> println("Sunday")
   2 -> println("Monday")
   3 -> println("Tuesday")
   4 -> println("Wednesday")
 } 
```

Enter fullscreen mode Exit fullscreen mode

这个想法是将自变量(变量`day`)与分支`1`、`2`、`3`或`4`进行匹配。测试从上到下进行(1，然后 2，然后 3，然后 4)，当匹配时，细箭头`->`右侧的语句(或程序块)被执行。不像在 *switch* 语句中，当发现一个匹配时，它不会流过或级联到下一个分支，所以，你不需要放一个 *break* 语句。

*when* 构造也可以用作表达式，这样使用时，每个分支都成为表达式的返回值。请参见下面的代码示例。

```
val d = Date()
 val c = Calendar.getInstance()
 val day = c.get(Calendar.DAY_OF_WEEK)

var dayOfweek = when (day) {
   1 -> "Sunday"
   2 -> "Monday"
   3 -> "Tuesday"
   4 -> "Wednesday"
   else -> "Unknown"
 } 
```

Enter fullscreen mode Exit fullscreen mode

如果你把它作为一个表达式来使用，记住要包括 *else* 子句。编译器会彻底检查所有可能的路径，并且需要做到详尽无遗，这就是为什么 *else* 子句成为一个要求的原因。

您不局限于数字文字，您可以为分支使用多种类型，如下面的代码。

```
fun main(args: Array<String>) {

  print("What is the answer to life? ")

  var response:Int? = readLine()?.toInt()  // (1) 
  val message = when(response){
    42 -> "So long, and thanks for the all fish"
    43, 44, 45 -> "either 43,44 or 45" // (2) 
    in 46 .. 100 ->  "forty six to one hundred" // (3) 
    else -> "Not what I'm looking for" // (4) 
  }
  println(message)
} 
```

Enter fullscreen mode Exit fullscreen mode

**(1)** `readLine()`从控制台读取一个输入。现在不要担心问号，我们将在接下来的部分中讨论这个问题

**(2)** 分支条件可以用逗号组合

我们可以检查它是否是某个范围或集合的成员

当 when 用作表达式时，else 子句是必需的

当不带参数时，你也可以用*，就像这个* 

```
val a = 3
val b = 2
val c = 1
val str = "str"

when {
  a > b -> println("a is greater than b")
  b > c -> println("b is greater than c")
  b == 2 -> println("b is equal to 2")
  str == "str" -> println("str is actually equal to $str")
} 
```

Enter fullscreen mode Exit fullscreen mode

这实际上使它相当于一个 *if-elseif* 结构；不过看起来干净多了。但是请稍等，再次阅读上面的示例代码，这一次，仔细检查它。你是否注意到所有分支中的所有条件都应该评估为真？确实如此。但是，如果您运行上面的代码，它将只打印第一个分支，`a > b`。像 *if-elseif* 一样，当一个分支的计算结果为真时，其余的后续分支将被忽略。将不再对它们进行评估。最好注意这一点。

当构造时，您也可以在*中使用`is`操作符。如果你这样做了，你也可以得到智能转换的好处。有关示例，请参见以下代码。* 

```
when (arg) {
  is Int -> println("The square of $arg is ${arg * arg}")
  is String -> println("Hello" + arg)
  is IntArray -> println(arg.sum())
} 
```

Enter fullscreen mode Exit fullscreen mode

另一个(稍微复杂一点)在构造中使用`is`操作符的例子。

```
fun <T> fooBar(arg:T) : T {   
  var retval:T = 0 as T
  when (arg) {
    is String -> {           
      retval = "Hello world" as T   
    }
    is Number -> {
      retval = 100 as T
    }
  }
  return retval
} 
```

Enter fullscreen mode Exit fullscreen mode

以下是你如何在 Android 应用程序中使用它。

```
class MainActivity : AppCompatActivity() {
  val Log = Logger.getLogger(MainActivity::class.java.name)

  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
  }

  override fun onCreateOptionsMenu(menu: Menu?): Boolean {
    Log.info("onCreateOptionsMenu")
    menu?.add("File")
    menu?.add("Exit")
    return super.onCreateOptionsMenu(menu)
  }

  override fun onOptionsItemSelected(item: MenuItem?): Boolean {
    when (item?.toString()) {
      "File" -> {
        Log.info("LOG File menu")
      }
      "Exit" -> {
        Log.info("LOG Exit menu")
      }
    }
    return true
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/39ab190e8fd2faa3a8b77394c4bb4d70.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9PDtppuT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/WorkingDev/%257E4/zJVCV_KQYoo)