# 编写 Java 命令行工具很麻烦...或者是？？？

> 原文：<https://dev.to/argherna/writing-java-command-line-tools-is-cumbersome-or-is-it--p42>

## 简单的 Java 工具

JDK 自带了许多支持开发的优秀命令行工具。但是在我做过的每一个项目中，我发现我需要从一个工具中得到更多的东西，这通常意味着我必须自己编写。我写的每一个工具都是为我自己而写的，通常足够完成工作。

我写了很多命令行工具来做额外的工作。与我一起工作的大多数 Java 开发人员都讨厌编写命令行 Java 工具的想法。它们必须被编译，然后运行。使用 [Maven](https://maven.apache.org/) 帮助完成`exec:java`目标。没有 Maven，你要编写长的`javac`命令来编译，然后编写长的`java`命令来运行它。但是如果你小心谨慎，坚持用 JDK 中的东西编写一个小工具来完成工作，你可以使用[一个我几年前看到的 shell 的技巧](https://gist.github.com/swankjesse/4742818)来使编写 Java 更像编写 Python。

## 一个简单的 Bash/Java 例子

之前，我提到过我是 Bash 的超级粉丝。你所需要的只是一个 Java 源文件，你可以很快写出有用的东西。考虑这个源文件:

```
/*bin/mkdir -p /tmp/.java/classes 2> /dev/null

# Compile the program.
#
javac -d /tmp/.java/classes $0

# Run the compiled program only if compilation succeeds.
#
[[ $? -eq 0 ]] && java -cp /tmp/.java/classes $(basename ${0%.*}) "$@"
exit
*/

class SimpleGreeting {
    public static void main(String... args) {
        System.out.println("Hello");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在 shell 中运行这个就像:
一样简单

```
$ bash SimpleGreeting.java
Hello
$ 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了什么？为什么这么容易？

答案就在顶部的评论里。Shell 脚本通常以一个解释器指令开始。顶部带有该注释的 Java 源文件运行 bash 中的命令。

## 分解解释器指令

很容易看出这里发生了什么:

*   在`/tmp`下创建一个子目录，如果该目录已经存在，则将输出重定向到`/dev/null`(我们不关心错误消息)。
*   编译源文件，把`.class`文件写到我们刚刚做的目标目录下。
*   从刚刚写入的目录运行`class`文件，去掉源文件的后缀。但是只在编译时运行它。
*   使用`java`运行的返回代码退出程序。

以这种方式编写工具的最大好处是，您可以修改代码并再次“运行”它，而不用担心是否需要进行编译。如果你有一个错误，编译器会告诉你它在哪里，它不会试图运行你的程序。

## 提示

高效开发工具意味着您应该:

*   在 JDK 之外使用尽可能少的依赖项(最好没有)
*   保持你的工具简单、简短，放在一个源文件中。
*   使用`bash SourceFile.java <args>`从命令行运行。
*   在解释器指令中避免过多的逻辑；但是如果必要的话，不要害怕使用逻辑。

## 样本工具

我写了一些工具，它们极大地帮助了我:

*   从本地 Maven 存储库中的 JDK 文档 zip 文件和 javadoc jar 文件提供 javadoc
*   从密钥库中转储 Base64/raw 密钥
*   从密钥库中转储私钥
*   将外部生成的密钥导入密钥库
*   检查 LDAP/JDBC 连接

仅仅使用 JDK，你就可以快速地做很多事情。它可能没有包括所有的[电池](https://www.python.org/dev/peps/pep-0206/#batteries-included-philosophy)，但是它有你不用去专卖店就能找到的所有种类的电池。

## 回执

我想再次感谢[杰西·威尔森](https://github.com/swankjesse)，他的主旨`Rip.java`(上面链接)启发了这篇文章。他是一个聪明的家伙，喜欢快速完成事情(很明显)。

我的例子在我的[点文件 GitHub 库](https://github.com/argherna/Dotfiles)中。查看脚本目录，您会发现我的工具以及我使用的所有其他东西。