# 用 JS、Python 和 Java 5 学习算法:FizzBuzz

> 原文：<https://dev.to/tommy3/learning-algorithms-with-js-python-and-java-5-fizzbuzz-1ik>

这是我尝试用三种不同的语言学习斯蒂芬·格里德的 Udemy 课程的第五篇文章。

当我几天前写下我的最后一篇文章时，我只有 5 个追随者，现在我有 35 个了！我也是第一次收到评论。我很高兴我开始了这个系列，但现在我觉得有点尴尬在这个时候写这么著名的问题。

肯定有成千上万种解决方案，但这里我只重点展示两个 JS 代码，并试图尽可能忠实地将它们“翻译”成 Python 和 Java。

> -说明
> 写一个程序，控制台记录从 1 到 n 的数字
> ，但是对于三的倍数
> 打印“嘶嘶”而不是数字，对于五的倍数
> 打印“嗡嗡”。对于 3 和 5 的倍数
> 的数字，打印“fizzbuzz”。
> -举例
> fizz buzz(5)；
> 1
> 2
> 嘶嘶作响
> 4
> 嗡嗡作响

## 1。简单的

JavaScript:

```
function fizzBuzz(n) {
    for (let i = 1; i <= n; i++) {
        if (i % 15 === 0) {
            console.log('fizzbuzz');
        } else if (i % 3 === 0) {
            console.log('fizz');
        } else if (i % 5 === 0) {
            console.log('buzz');
        } else {
            console.log(i);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

Python:

```
def fizz_buzz(n):
    for i in range(1, n+1):
        if i % 15 == 0:
            print('fizzbuzz')
        elif i % 3 == 0:
            print('fizz')
        elif i % 5 == 0:
            print('buzz')
        else:
            print(i) 
```

Enter fullscreen mode Exit fullscreen mode

Java:

```
public static void fizzBuzz(int n) {
    for (int i = 1; i <= n; i++) {
        if (i % 15 == 0) {
            System.out.println("fizzbuzz");
        } else if (i % 3 == 0) {
            System.out.println("fizz");
        } else if (i % 5 == 0) {
            System.out.println("buzz");
        } else {
            System.out.println(i);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 2。更简洁

JavaScript:

```
function fizzBuzz(n) {
    for (let i = 1; i <= n; i++) {
        console.log((i % 3 ? '' : 'fizz')
            + (i % 5 ? '' : 'buzz') || i);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

Python:

```
def fizz_buzz(n):
    for i in range(1, n+1):
        print(('' if i % 3 else 'fizz') +
              ('' if i % 5 else 'buzz') or i) 
```

Enter fullscreen mode Exit fullscreen mode

Java:

```
public static void fizzBuzz(int n) {
    for (int i = 1; i <= n; i++) {
        String result = (i % 3 > 0 ? "" : "fizz") 
                + (i % 5 > 0 ? "" : "buzz");
        if (result.equals("")) {
            result = String.valueOf(i);
        }
        System.out.println(result);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我对最后的 Java 代码不是很满意。我很想知道是否有一个更确切的对等词。提前感谢大家的评论！