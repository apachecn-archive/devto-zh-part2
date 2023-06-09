# 如何为 UnitTest 编写“生产代码”？

> 原文：<https://dev.to/koduki/how-do-you-write-production-code-for-unittest-5f30>

## 简介

单元测试对于保持代码质量非常重要。所以几乎所有的项目都需要编写单元测试。

结果，我会复习奇怪的 UnitTest 或者我会被他们问到“我不会写 UnitTest！”。

为什么？因为他们没有考虑单元测试的生产代码。他们只考虑关于 UnitTest 的 UnitTest 代码。这可不好。

在编写单元测试时，我们应该将“业务逻辑”和“不受控制的值”从产品代码中分离出来。这对“可读性”也有好处。

基本规则只有两条。

*   分离“业务逻辑”和“不受控制的值，如 I/O、日期、随机”
*   通过参数、构造器、非私有字段注入依赖项

在本文中，我将解释如何为 UnitTest 编写产品代码。

## 典型例子

首先，我解释典型案例。

### 案例 01:“我要输出到标准输出”

你应该像一个`System.out.println`或`Logger.info`一样分离逻辑和输出方法。
你做一个简单的方法来构建“输出字符串”。

```
// Production Code:

public class Example01Good {
    public static void main(String[] args) {
        String message = args[0];
        System.out.println(makeMessage(message));
    }

    static String makeMessage(String message) {
        return "Hello, " + message;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// UnitTest Code:

@Test
public void testMakeMessage() {
    assertThat(Example01Good.makeMessage("world"), is("Hello, world"));
} 
```

Enter fullscreen mode Exit fullscreen mode

### 案例 02:“我要用随机数”

随机数不能修正结果。所以我们需要将逻辑和数字生成器分开。
这是一个简单的骰子游戏逻辑，用于检查“奇数”或“偶数”

```
// Production Code:

public class Example02Good {
    public static void main(String[] args) {
        System.out.println(check(throwDice(Math.random())));
    }

    static String check(int num) {
        return (num % 2 == 0) ? "Win" : "Lose";
    }

    static int throwDice(double rand) {
        return (int) (rand * 6);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// Test Code:

@Test
public void testCheck() {
    assertThat(Example02Good.check(1), is("Lose"));
    assertThat(Example02Good.check(2), is("Win"));
    assertThat(Example02Good.check(3), is("Lose"));
    assertThat(Example02Good.check(4), is("Win"));
    assertThat(Example02Good.check(5), is("Lose"));
    assertThat(Example02Good.check(6), is("Win"));
} 
```

Enter fullscreen mode Exit fullscreen mode

### 案例 03:“我想计算明天、1 年前或以后的日历”

计算日历是非常通用的代码。但是`current time`和`today`一样是不受控制的值。
您需要将日历计算逻辑和获取`current time`分开。

可以像随机数一样使用`method arguments`。这是很好的实践。
但是有时候`getting current time`用在很多地方。在这种情况下，你也可以使用`factory pattern`。

```
// Production Code

class SystemDate {
    public LocalDate today() {
        return LocalDate.now();
    }
}

public class Example03Good {
    SystemDate systemDate = new SystemDate();

    public LocalDate tomorrow2() {
        return systemDate.today().plusDays(1);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// Test Code

@Test
public void testTomorrow2() {
    Example03Good target = new Example03Good();
    target.systemDate = new SystemDate() {
        @Override
        public LocalDate today() {
            return LocalDate.of(2017, 1, 16);
        }
    };
    assertThat(target.tomorrow2(), is(LocalDate.of(2017, 1, 17)));
} 
```

Enter fullscreen mode Exit fullscreen mode

### 案例 04:“我想管理文件 I/O”

基本上，这与标准 I/O 情况相同。您应该将`building Text or Binary`的逻辑和读或写的逻辑分开。这非常好。

但有时你需要非常多的文本大小，检查行的顺序等等。
在这种情况下，您可以使用读取器/写入器和输入流/输出流来分离逻辑和 I/o。

```
// Production Code

public class Example04Good {
    public static void main(String[] args) throws Exception {
        System.out.println("hello");
        try (Reader reader = Files.newBufferedReader(Paths.get("intput.txt"));
                Writer writer = Files.newBufferedWriter(Paths.get("output.txt"));) {
            addLineNumber(reader, writer);
        }
    }

    static void addLineNumber(Reader reader, Writer writer) throws IOException {
        try (BufferedReader br = new BufferedReader(reader);
                PrintWriter pw = new PrintWriter(writer);) {
            int i = 1;
            for (String line = br.readLine(); line != null; line = br.readLine()) {
                pw.println(i + ": " + line);
                i += 1;
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// Test Code

@Test
public void testAddLineNumber() throws Exception {
    Writer writer = new StringWriter();
    addLineNumber(new StringReader("a\nb\nc\n"), writer);
    writer.flush();
    String[] actuals = writer.toString().split(System.lineSeparator());

    assertThat(actuals.length, is(3));
    assertThat(actuals[0], is("1: a"));
    assertThat(actuals[1], is("2: b"));
    assertThat(actuals[2], is("3: c"));
} 
```

Enter fullscreen mode Exit fullscreen mode

## 基本概念

在这一节，我会更深入地解释。

### 分离逻辑和输入输出

最重要的是，每次都应该将逻辑和 I/O 分开。这是单元测试的重要设计。

让我们以一个简单的程序为例，该程序用于“从命令行参数中获取文本并打印到标准输出”。

```
public class Example01Bad {
    public static void main(String[] args) {
        String message = args[0];
//         String message = "World"; // for debug.
        System.out.println("Hello, " + message);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

也许，这是最简单的代码。我建议你先写下同样的代码。
`for debug`评论搞笑。

接下来，让我们编写 UnitTest。

```
/**
 * Super bad code
 */
@Test
public void testMain() {
    String[] args = {"world"};
    Example01Bad.main(args);
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个超级简单的。但是没有断言！所以这个单元测试需要用你的眼睛来检查真假！你认为这是一个笑话吗？不幸的是，我在真实的项目中一次又一次地看到这样的单元测试...

当然，这段代码很糟糕。越来越多更优秀的人编写 blow 代码。

```
/**
 * Bad code
 */
@Test
public void testMainWithSystemOut() {
    ByteArrayOutputStream out = new ByteArrayOutputStream();
    System.setOut(new PrintStream(out));

    String[] args = {"world"};
    Example01Bad.main(args);

    assertThat(out.toString(), is("Hello, world" + System.lineSeparator()));
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码挂钩标准输出。还不错。你可以完美地将它作为单元测试来运行。但是有点复杂。

如果您不能修改目标生产代码，您应该编写它。然而，如果你能改变生产代码，单元测试就变得更容易了。

```
// Good production code
public class Example01Good {
    public static void main(String[] args) {
        String message = args[0];
        System.out.println(makeMessage(message));
    }

    static String makeMessage(String message) {
        return "Hello, " + message;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我将“构建消息逻辑”分离为 makeMessage 方法。所以 UnitTest 变成了如下。

```
// Good test code
@Test
public void testMakeMessage() {
    assertThat(Example01Good.makeMessage("world"), is("Hello, world"));
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常简单的。作为 UnitTest 它是完美的。

您可能会觉得奇怪，因为这段代码没有对 StandardOut 做任何测试。完全正确。但是作为 UnitTest 没有必要。

基本上，检查业务逻辑是单元测试中最重要的。
`Sysmte.out.println`和日志库是标准或流行的库。
这意味着他们的代码质量被其他测试所保持。你只关心你的生意。
当然，你还需要查一下标准输出之类的。但这是综合测试。

### 不要直接初始化不受控制的值

你不应该在你的每一个方法上初始化不受控制的值，比如随机数，日期，RPC(WebAPI)，DAO。请应用依赖注入的概念。

让我们举一个`calculating tomorrow`的方法的例子。

```
public class Example03Bad {
    public LocalDate tomorrow() {
        return LocalDate.now().plusDays(1);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

自然，这段代码不能进行单元测试。因为`LocalDate.now()`值每次都在变。
所以`LocalDate.now()`是不受控制的值。这是典型的初学者陷阱。
你需要将它从业务逻辑中分离出来。最简单的解决方案是使用方法参数。

```
public LocalDate tomorrow(LocalDate today) {
    return today.plusDays(1);
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，您可以修正测试代码如下。

```
@Test
public void testTomorrow() {
    Example03Good target = new Example03Good();
    assertThat(target.tomorrow(LocalDate.of(2017, 1, 16)), is(LocalDate.of(2017, 1, 17)));
} 
```

Enter fullscreen mode Exit fullscreen mode

这就够了。但是如果获取当前时间在很多地方都被使用，你也可以工厂方法模式和存根。
首先，你创建一个工厂类，它有一个方法`today()`来返回`LocalDate`。
接下来，在目标代码字段设置它。

`today()`的返回值取决于实现。在生产中是`LocalDate.now()`。

```
class SystemDate {
    public LocalDate today() {
        return LocalDate.now();
    }
}

public class Example03Good {
    SystemDate systemDate = new SystemDate();

    public LocalDate tomorrow2() {
        return systemDate.today().plusDays(1);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在测试代码中，你使用存根而不是`LocalDate.now()`。

```
@Test
public void testTomorrow2() {
    Example03Good target = new Example03Good();
    target.systemDate = new SystemDate() {
        @Override
        public LocalDate today() {
            return LocalDate.of(2017, 1, 16);
        }
    };
    assertThat(target.tomorrow2(), is(LocalDate.of(2017, 1, 17)));
} 
```

Enter fullscreen mode Exit fullscreen mode

关键是使用`package scope`而不是`private scope`或者你应该通过构造函数初始化。
如果你使用私有作用域，你需要一个模仿框架等等。

## 总结

基本规则只有两条。

*   分离“业务逻辑”和“不受控制的值，如 I/O、日期、随机”
*   通过参数、构造器、非私有字段注入依赖项

这个不难。但有时候 biginner 并不知道。另外，TDD(测试驱动开发)和 Test First 强迫你写这样的代码。这是 TDD 流行的一个原因。

函数式语言对副作用有更严格的要求。让我们也试着研究一下。