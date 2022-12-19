# 遵循惯例的重要性

> 原文：<https://dev.to/donaldkellett/the-importance-of-following-conventions-g5a>

考虑下面的 Java 代码片段。它们有什么相似之处？它们有什么不同？是其中一个还是两个都适合生产？

```
/*
 * Program1.java
 */

public class Program1 {

    public static void main(String[] args) {
        System.out.println("Hello World!");
    }
} 
```

```
// program_2.java
public class program_2
{
public static void main(String CmdLineArgs[]) {System.out.println("Hello World!");}
} 
```

如果你懂一点 Java，应该很明显这两个代码片段做的*完全是*一样的事情——它们都在屏幕上打印文本`Hello World!`(后跟一个换行符)。然而，只有`Program1.java`适合生产——事实上，`program_2.java`应该让你热血沸腾。那么，如果`program_2.java`做的事情和`Program1.java`完全一样，那么它有什么问题呢？它不遵循既定的惯例——例如，类/程序名应该是`Program2`而不是`program_2`。那么什么是惯例，为什么它们很重要？

“惯例”的一般定义可以是“鼓励(程序员)团体遵守的一套非正式的规则”。例如，在 Java 中，存在以下约定:

*   类名应该总是每个单词都大写，单词之间没有分隔符(这被称为`PascalCase`或`StudlyCaps`)。例如，`HelloWorld`是一个好的类名，而`helloWorld`和`hello_world`则不是。
*   变量/方法名应该总是每个单词*都大写，除了第一个单词*，单词之间没有分隔符(称为`camelCase`)。例如，`main`、`saveProgress`和`fileHandle`是很好的变量/方法名，而`SayHello`和/或`nth_fibonacci`则不是。
*   常量(标有`final`修饰符的变量)应该全部大写，单词之间用一个下划线作为分隔符，例如`PI`和`MAX_VALUE`是好的常量名称，而`Pi`和`minValue`不是。

所以，如果这些规则仅仅是非正式的，并不影响程序的功能，那么我们为什么要遵循它们呢？

通过遵循既定的惯例，其他程序员更容易理解你的代码，从而提高生产率。例如，如果每个 Java 程序员都使用`PascalCase`代表所有*的类名，`camelCase`代表所有*的变量/方法名，`SNAKE_CASE_WITH_ALL_CAPS`代表所有*的常量名，那么当一个 Java 程序员阅读生成的代码时，他/她可以通过进行以下简化假设来快速浏览代码:***

*   如果在`PascalCase`中看到一个标识符，那么这个标识符就是一个类名
*   如果在`camelCase`中看到一个标识符，那么这个标识符要么是一个变量名，要么是一个方法名
*   如果在`SNAKE_CASE_WITH_ALL_CAPS`中看到一个标识符，那么该标识符是一个不可变的常量

相比之下，如果一些常量在`camelCase`中，而一些变量也在`SNAKE_CASE_WITH_ALL_CAPS`和/或`PascalCase`中，那么每当阅读您的程序的 Java 程序员同事看到这样的标识符时，他/她可能需要浏览整个*的*程序(或者甚至整个项目，如果有多个文件在它们之间导入),在最坏的情况下，只是为了弄清楚所述标识符是引用一个类、变量/方法还是常量。这将大大降低程序员阅读和理解你的代码的速度，从而降低生产率。

当然，约定不限于命名标识符或缩进——设计模式也是一种约定。例如，在 Java 中，包含一个又一个类的面向对象代码是标准的，但是如果您只是用 JavaScript 打印一个`Hello World!`,您可能会看到奇怪的外观:

```
// How NOT to write JavaScript!
// Assuming a Nodejs environment with ES6 or later

class HelloWorld {
    static main(args) {
        console.log("Hello World!");
    }
}

HelloWorld.main(process.argv); 
```

最后，尽管在你从事的任何语言/领域中遵循既定惯例通常是最佳实践，但在某些情况下可能并不适用。以代码高尔夫为例——当您试图最小化程序中的字节数来完成特定任务时，所有形式的最佳实践(如描述性变量名和缩进)都会自动退出窗口。虽然约定往往很稳定，但它们有时会随着时间而改变——例如，在 PHP 中，`snake_case`以前是函数/方法名的首选，但近年来逐渐转向使用`camelCase`。关于改变约定的唯一事情是，如果你打算改变你的领域/语言中的某个编程约定，你应该总是提供这样做的令人信服的理由，例如，我们应该在 PHP 中使用`camelCase`而不是`snake_case`作为函数/方法名，因为它们更容易键入，或者我们应该在我们的项目中使用`snake_case`而不是`PascalCase`，因为当有显式分隔符时，更容易立即看到单个单词。