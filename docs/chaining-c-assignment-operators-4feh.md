# 链接 C#赋值运算符

> 原文：<https://dev.to/fabulousyap/chaining-c-assignment-operators-4feh>

这一切都是从`swap()`开始的，一个简单的编程问题加入了我的面试题库。最简单的形式是，当我们交换两个整数时，引入一个临时变量作为占位符:

```
static void swap(ref int a, ref int b)
{
  int tmp;

  tmp = a;
  a = b;
  b = tmp;
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，在不使用临时变量的情况下，我们有下面的方法来写`swap()`:

```
static void swap(ref int a, ref int b)
{
  a = a ^ b;
  b = a ^ b;
  a = a ^ b;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用赋值运算符来进一步简化它:

```
static void swap(ref int a, ref int b)
{
  a ^= b;
  b ^= a;
  a ^= b;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，作为一个来自`C/C++`的书呆子，我们知道我们可以把上面的缩写成:

```
/* C using pointer */
void swap(int *a, int *b)
{
  *a ^= *b ^= *a ^= *b;
} 
```

Enter fullscreen mode Exit fullscreen mode

在`C`语言或`C++`中...

```
// C++ using reference
void swap(int& a, int& b)
{
  a ^= b ^= a ^= b;
} 
```

Enter fullscreen mode Exit fullscreen mode

使用赋值运算符。这些作为赋值操作符的作品以**右关联**的方式进行评估...这里是等价的`C#`代码...

```
static void swap(ref int a, ref int b)
{
  a ^= b ^= a ^= b;
} 
```

Enter fullscreen mode Exit fullscreen mode

但令我惊讶的是，上面的`C#`代码并没有像预期的那样工作。试试下面的程序，你会发现它并没有真正交换这两个整数...

```
using System;

namespace AssignmentOperatorChaining
{
    class Program
    {
        static void Main(string[] args)
        {
            int x = 0, y = 0;

            x = 10; y = 20;
            Console.WriteLine("x: {0},    y: {1}", x, y);
            swap(ref x, ref y);
            Console.WriteLine("x: {0},    y: {1}", x, y);
        }

        static void swap(ref int a, ref int b)
        {
            a ^= b ^= a ^= b;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

调用`swap()`后，结果是`x: 0, y: 10`而不是`x: 20, y: 10`。通过谷歌搜索，我没有找到太多关于这种行为的信息。
此外，对上面的分配链接代码做一点小小的修改就可以了:

```
static void swap(ref int a, ref int b)
{
  b ^= a ^= b;
  a ^= b;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，我真的很困惑...但是自从。Net 反汇编程序随处可得，所以我决定先看看到底发生了什么。创建了下面的程序来查看交换 2 个整数的几种不同方式之间的区别...

```
using System;

namespace AssignmentOperatorChaining
{
    class Program
    {
        static void Main(string[] args)
        {
            int x = 0, y = 0;

            x = 10; y = 20;
            Console.WriteLine("x: {0},    y: {1}", x, y);
            swap1(ref x, ref y);
            Console.WriteLine("x: {0},    y: {1}", x, y);

            x = 10; y = 20;
            Console.WriteLine("x: {0},    y: {1}", x, y);
            swap2(ref x, ref y);
            Console.WriteLine("x: {0},    y: {1}", x, y);

            x = 10; y = 20;
            Console.WriteLine("x: {0},    y: {1}", x, y);
            swap3(ref x, ref y);
            Console.WriteLine("x: {0},    y: {1}", x, y);

            x = 10; y = 20;
            Console.WriteLine("x: {0},    y: {1}", x, y);
            swap4(ref x, ref y);
            Console.WriteLine("x: {0},    y: {1}", x, y);

            x = 10; y = 20;
            Console.WriteLine("x: {0},    y: {1}", x, y);
            swap5(ref x, ref y);
            Console.WriteLine("x: {0},    y: {1}", x, y);
            Console.ReadLine();
        }

        static void swap1(ref int a, ref int b)
        {
            int tmp;
            tmp = a;
            a = b;
            b = tmp;
        }
        static void swap2(ref int a, ref int b)
        {
            a = a ^ b;
            b = a ^ b;
            a = a ^ b;
        }
        static void swap3(ref int a, ref int b)
        {
            a ^= b;
            b ^= a;
            a ^= b;
        }
        static void swap4(ref int a, ref int b)
        {
            b ^= a ^= b;
            a ^= b;
        }
        static void swap5(ref int a, ref int b)
        {
            a ^= b ^= a ^= b;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

以及由此产生的 il...
[![IL codes Compare](img/fc9ad7e36304cbbba81e752f10f21452.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d2ha_xZs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kxdw4rz19suvto3hqx7f.PNG) 
非常有趣的输出。更有意思的是当把 exe 喂给。Net 反汇编程序，比如 JustDecompile，逆向工程`swap5()`的代码`C#`实际上是:

```
private static void swap(ref int a, ref int b)
{
  int num = a ^ b;
  int num1 = num;
  a = num;
  int num2 = b ^ num1;
  num1 = num2;
  b = num2;
  a ^= num1;
} 
```

Enter fullscreen mode Exit fullscreen mode

(我也用 JetBrains 的 dotPeek 测试过，然而，`swap5()`的反汇编输出在 dotPeek 中触发了一种恐慌模式，导致了不可编译的`C#`代码。)

如果我们仔细看看，`swap2()`和`swap3()`真的没有区别，似乎`swap1()`产生了最短的`IL`代码，这也可以在运行在反汇编 x86 模式时得到证实:
[![x86 instructions](img/d5c91547b906a4f6bd475b55a247977b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IB13HB28--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1voyjqlqi9ihj4apuzv0.PNG)

不管我们是否认为`a ^= b ^= a ^= b`赋值操作符链接是一个`C#`编译器错误(测试也是使用。Net Core 2.1 在 Windows 和 Linux 中，同样的结果)，这个故事的寓意是，有时，最简单的事情往往可能是最好的和/或最快的- K.I.S.S .更不用说同时 swap1()也更容易阅读。

这种赋值操作符链接的奇怪行为不仅影响到`^=`操作符，其他人也会受到影响，这里有一个小程序来测试:

```
using System;

namespace AssignmentOperatorsChaining
{
    class Program
    {
        static void Main(string[] args)
        {
            int x=0, y=0;

            Console.WriteLine("-----------========== ^= operator ===========-----------");
            Console.WriteLine("chaining:");
            x = 10; y = 20;
            Console.WriteLine("x: {0},    y:{1}", x, y);
            x ^= y ^= x ^= y;
            Console.WriteLine("x: {0},    y:{1}", x, y);

            Console.WriteLine("non-chaining:");
            x = 10; y = 20;
            Console.WriteLine("x: {0},    y:{1}", x, y);
            x ^= y;
            y ^= x;
            x ^= y;
            Console.WriteLine("x: {0},    y:{1}", x, y);
            Console.WriteLine("");

            Console.WriteLine("-----------========== *= operator ===========-----------");
            Console.WriteLine("chaining:");
            x = 10; y = 20;
            Console.WriteLine("x: {0},    y:{1}", x, y);
            x *= y *= x *= y;
            Console.WriteLine("x: {0},    y:{1}", x, y);

            Console.WriteLine("non-chaining:");
            x = 10; y = 20;
            Console.WriteLine("x: {0},    y:{1}", x, y);
            x *= y;
            y *= x;
            x *= y;
            Console.WriteLine("x: {0},    y:{1}", x, y);
            Console.WriteLine("");

            Console.WriteLine("-----------========== += operator ===========-----------");
            Console.WriteLine("chaining:");
            x = 10; y = 20;
            Console.WriteLine("x: {0},    y:{1}", x, y);
            x += y += x += y;
            Console.WriteLine("x: {0},    y:{1}", x, y);

            Console.WriteLine("non-chaining:");
            x = 10; y = 20;
            Console.WriteLine("x: {0},    y:{1}", x, y);
            x += y;
            y += x;
            x += y;
            Console.WriteLine("x: {0},    y:{1}", x, y);
            Console.WriteLine("");

            Console.WriteLine("-----------========== -= operator ===========-----------");
            Console.WriteLine("chaining:");
            x = 10; y = 20;
            Console.WriteLine("x: {0},    y:{1}", x, y);
            x -= y -= x -= y;
            Console.WriteLine("x: {0},    y:{1}", x, y);

            Console.WriteLine("non-chaining:");
            x = 10; y = 20;
            Console.WriteLine("x: {0},    y:{1}", x, y);
            x -= y;
            y -= x;
            x -= y;
            Console.WriteLine("x: {0},    y:{1}", x, y);
            Console.WriteLine("");

            Console.WriteLine("-----------========== /= operator ===========-----------");
            Console.WriteLine("chaining:");
            x = 20; y = 20;
            Console.WriteLine("x: {0},    y:{1}", x, y);
            x /= y /= x /= y;
            Console.WriteLine("x: {0},    y:{1}", x, y);

            Console.WriteLine("non-chaining:");
            x = 20; y = 20;
            Console.WriteLine("x: {0},    y:{1}", x, y);
            x /= y;
            y /= x;
            x /= y;
            Console.WriteLine("x: {0},    y:{1}", x, y);
            Console.ReadKey();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后但并非最不重要的是，如果以前在某个地方记录/讨论过这一点，或者我在代码中犯了错误，我将非常感谢 dev.to community 的集体智慧为我指出正确的方向。谢谢大家，祝大家愉快。