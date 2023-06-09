# 不要初始化你的变量

> 原文：<https://dev.to/idanarye/dont-initialize-your-variables-40d>

来自 C 的开发人员知道变量应该总是被初始化。不初始化你的变量意味着它们包含垃圾，这会导致未定义的行为。例如:

```
#include<stdio.h> 
int main(void) {
    char buffer[256];
    char answer;
    char* name;

    printf("Do you want to enter a name? [yn] ");
    answer = getchar();

    while (getchar() != '\n') { } // because we need CR for getchar but it doesn't read the CR...

    if (answer == 'y') {
        printf("Please enter name: ");
        name = fgets(buffer, 256, stdin);
        if (name == 0) {
            name = "<too long>";
        }
    } else if (answer == 'n') {
        name = "<user refused to enter name>";
    }

    printf("The name is %s\n", name);
    return 0;
} 
```

如果用户输入一个不是`y`或`n`的字符，则`name = ...;`语句不会被执行，并且`name`仍将保持与`main`开始时相同的值。价值是什么？在释放模式 C 中，无论随机数据发生在那块内存`name`中，它都会被分配。然后我们把这个完全随机的数字传递给`printf`，在那里它将被打印出来，就像它是一个字符串指针一样！

如果幸运的话，我们会碰到一些非法的内存地址，操作系统会阻止我们。如果我们不这样做，它就会去内存中的某个随机位置，开始打印它遇到的任何东西:密码、凭证、应用程序令牌...

当然，这是不可复制的。因为每次你运行程序，在内存中的那个位置会有不同的值，你会得到不同的结果。

为了避免这些问题，C 开发人员已经习惯于总是初始化他们的变量。如果你没有什么有意义的东西要放入声明点——就放 0:

```
#include<stdio.h> 
int main(void) {
    char buffer[256] = {};
    char answer = '\0';
    char* name = 0;

    printf("Do you want to enter a name? [yn] ");
    answer = getchar();

    while (getchar() != '\n') { } // because we need CR for getchar but it doesn't read the CR...

    if (answer == 'y') {
        printf("Please enter name: ");
        name = fgets(buffer, 256, stdin);
        if (name == 0) {
            name = "<too long>";
        }
    } else if (answer == 'n') {
        name = "<user refused to enter name>";
    }

    printf("The name is %s\n", name);
    return 0;
} 
```

虽然空指针取消引用在形式上仍然是一种未定义的行为，但它仍然比随机指针取消引用好得多，因为您的操作系统可能会使它成为 SEGFAULT -这比安全漏洞要好。

## 好吧，但那是 c，那更现代的语言呢？

这在 C 语言中如此必要主要有两个原因:

1.  含有垃圾数据的未初始化变量。
2.  无法在块中间声明变量。

更现代的语言允许在块的中间声明变量，所以通常最好只在有意义的地方声明变量。

这大大减少了必须用默认值初始化的情况——但并不能防止所有的情况。例如，在我们的例子中，`name`在`if`分支中获得它的值——如果我们在那里声明它，我们就不能在`if`之后使用它。一些语言(主要是函数式语言)有简单的语法解决方案，但是在大多数主流语言中，你必须将它提取到一个函数中，或者在块外声明变量。

当采用后一种解决方案时，因为 C 是如此常见的背景，许多开发人员会初始化该值。所以如果我们把代码转换成 Java:

```
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("Do you want to enter a name? [yn] ");
        String answer = scanner.nextLine();

        String name = null;
        if ("y".equals(answer)) {
            System.out.print("Please enter name: ");
            name = scanner.nextLine();
        } else if ("n".equals(answer)) {
            name = "<user refused to enter name>";
        }

        System.out.printf("The name is %s\n", name);
    }
} 
```

当然，这是 Java，一种带有托管内存的语言，它永远不允许未初始化变量的未定义行为，所以我们真的不需要初始化`name`到`null`，但是安全总比抱歉好，对吧？

# 不对！

Java 分析代码路径，以确保没有变量在没有初始化的情况下不能使用。所以如果我们去掉初始化:

```
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("Do you want to enter a name? [yn] ");
        String answer = scanner.nextLine();

        String name;
        if ("y".equals(answer)) {
            System.out.print("Please enter name: ");
            name = scanner.nextLine();
        } else if ("n".equals(answer)) {
            name = "<user refused to enter name>";
        }

        System.out.printf("The name is %s\n", name);
    }
} 
```

我们将得到一个编译错误:

```
$ javac Main.java 
Main.java:18: error: variable name might not have been initialized
        System.out.printf("The name is %s\n", name);
                                              ^
1 error 
```

我刚破了编译，不过这是好事——编译器发现了一个 bug！我们在 C 版本中遇到的同样的错误——如果用户输入了不是`y`或`n`的东西怎么办。Java 编译器发现有三条可能的代码路径到达最后一行，但是我们只初始化了其中的两条。

为了能够再次编译，我们必须告诉 Java 在用户给出无效答案的情况下该做什么。失败也是一种选择——只要我们有意为之:

```
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("Do you want to enter a name? [yn] ");
        String answer = scanner.nextLine();

        String name;
        if ("y".equals(answer)) {
            System.out.print("Please enter name: ");
            name = scanner.nextLine();
        } else if ("n".equals(answer)) {
            name = "<user refused to enter name>";
        } else {
            System.err.printf("Illegal answer \"%s\". The only legal answers are \"y\" and \"n\".", answer);
            return;
        }

        System.out.printf("The name is %s\n", name);
    }
} 
```

现在仍然有三个代码路径，但是在第三个中，我们在打印`name`之前，从函数 early`return`开始。Java 编译器可以确定没有使用`name`的代码路径，而没有先赋值——因此编译成功。

# 这仍然是初始化

尽管标题是 clickbaity，但我们确实初始化了`name`。我们不做声明，但我们仍然在初始化它。这将编译:

```
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("Do you want to enter a name? [yn] ");
        String answer = scanner.nextLine();

        final String name;
        if ("y".equals(answer)) {
            System.out.print("Please enter name: ");
            name = scanner.nextLine();
        } else if ("n".equals(answer)) {
            name = "<user refused to enter name>";
        } else {
            System.err.printf("Illegal answer \"%s\". The only legal answers are \"y\" and \"n\".", answer);
            return;
        }

        System.out.printf("The name is %s\n", name);
    }
} 
```

*Wait - how？他们没教过我们不能改变一个`final`变量的值吗？*

是的，但是我们并没有改变任何`final`变量的值——我们只是初始化它。由于`name`以前从未在赋给它的路径中被赋值，这些赋值实际上是初始化——这对`final`变量来说非常好。它不能与`final String name = null`一起工作，但是没有声明上的初始化也没关系，甚至没有`final` `name`也可以在 lambdas 中使用(假设它们在第一次赋值之后出现**)。**

# 结论

一定要初始化你的变量——但是当你不能用一个合适的值初始化它们的时候，不要总是强制使用默认值。了解您的语言如何处理未初始化的变量，并选择发现错误的最佳策略。