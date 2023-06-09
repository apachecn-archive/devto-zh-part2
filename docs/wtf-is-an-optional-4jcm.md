# WTF 是可选的？

> 原文：<https://dev.to/vatdaell/wtf-is-an-optional-4jcm>

我正在开始一系列的 java 教程，题目是 WTF 是 a(n) ___？在本教程中，我将从期权开始。

## 所以 WTF 是可选的？

可选类是 Java 8 中引入的一个类，它被引入来处理流中的空检查。尽管它是为流引入的，但我们可以用它来创建更干净的空检查。

例如，以下代码将失败，因为没有执行空检查。

```
public class Main {
    // No null check is done here, a null pointer exception will be raised
    public static void main(String[] args) {
        System.out.println(printSecondWord("ONEWORD").toLowerCase());
    }

    // Return null if 2 words not there else return 2nd word
    private static String printSecondWord(String words){
        String[] wordsList = words.split(" ");
        return wordsList.length < 2 ? null : wordsList[1];
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在 Java 8 之前(你现在也可以这样做)，你必须像这样做一个手工的空值检查:

```
public class Main {

    public static void main(String[] args) {
        String secondWord = printSecondWord("ONEWORD");
        if(secondWord != null) {
            System.out.println(secondWord.toLowerCase());
        }
    }

    // Return null if 2 words not there else return 2nd word
    private static String printSecondWord(String words){
        String[] wordsList = words.split(" ");
        return wordsList.length < 2 ? null : wordsList[1];
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这种方式很好，但是它带来了一个导致空指针异常的问题，也就是十亿美元的错误。静态方法假设另一个开发人员将进行空检查。传统的空指针检查的主要问题是开发人员忘记进行空检查，这可能是个问题，因为一个错误可能导致整个应用程序失败。

构建代码并迫使开发人员进行空检查的一种更现代的方法是返回一个可选的

```
public class Main {

    public static void main(String[] args) {
        printSecondWord("ONEWORD").ifPresent(secondWord -> {
            System.out.println(secondWord);
        });

    }

    private static Optional<String> printSecondWord(String words){
        String[] wordsList = words.split(" ");
        return Optional.ofNullable(wordsList.length < 2 ? null : wordsList[1]);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

返回可选的是一个更好的选择，因为开发人员必须通过从静态方法调用`ifPresent`来手动访问结果。可选的。

或者，您也可以捕获`ifPresent`的布尔值，并决定做什么。

```
import java.util.Optional;

public class Main {

    public static void main(String[] args) {
        Optional<String> secondWord = printSecondWord("ONEWORD");

        boolean isTheWordThere = secondWord.isPresent();
        // Print NOT HERE if not present
        if(!isTheWordThere){
            System.out.println("NOT HERE");
        }
        else{
            System.out.println(secondWord.get());
        }

    }
    // Return null if 2 words not there else return 2nd word
    private static Optional<String> printSecondWord(String words){
        String[] wordsList = words.split(" ");
        return Optional.ofNullable(wordsList.length < 2 ? null : wordsList[1]);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我希望这篇文章可以帮助你们开始使用期权，并且你们可以在你们的项目中更深入地探索期权。

因为这是我的第一篇帖子，我希望得到一些反馈，欢迎任何评论！！

继续黑！！！