# 带有 Java 的彩色终端输出

> 原文：<https://dev.to/awwsmm/coloured-terminal-output-with-java-57l3>

我今天正在寻找一种方法来给一些终端输出添加一点风格，发现了这个 StackOverflow 帖子解释了如何使用 [ANSI 颜色代码](https://en.wikipedia.org/wiki/ANSI_escape_code)给终端输出添加颜色。下面是我的(MobaXterm / Ubuntu) shell 中以下 Java 代码的输出:

[![](img/27e8ab9e145ec6eb9162d30f972ef3d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QxGIN9mR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xej7gshfqqvevqert1fs.png)

请注意，我没有包括下划线或粗体文本，这两种文本也是可用的。还有一个由迪奥戈·努内斯开发的名为[Java Colored Debug Printer(JCDP)](https://github.com/dialex/JCDP)的库，它将 ANSI 代码翻译成 Windows command prompt 可以处理的东西，使解决方案跨平台。代码如下:

```
public class ColouredSystemOutPrintln {

  public static final String ANSI_RESET  = "\u001B[0m";

  public static final String ANSI_BLACK  = "\u001B[30m";
  public static final String ANSI_RED    = "\u001B[31m";
  public static final String ANSI_GREEN  = "\u001B[32m";
  public static final String ANSI_YELLOW = "\u001B[33m";
  public static final String ANSI_BLUE   = "\u001B[34m";
  public static final String ANSI_PURPLE = "\u001B[35m";
  public static final String ANSI_CYAN   = "\u001B[36m";
  public static final String ANSI_WHITE  = "\u001B[37m";

  public static final String ANSI_BRIGHT_BLACK  = "\u001B[90m";
  public static final String ANSI_BRIGHT_RED    = "\u001B[91m";
  public static final String ANSI_BRIGHT_GREEN  = "\u001B[92m";
  public static final String ANSI_BRIGHT_YELLOW = "\u001B[93m";
  public static final String ANSI_BRIGHT_BLUE   = "\u001B[94m";
  public static final String ANSI_BRIGHT_PURPLE = "\u001B[95m";
  public static final String ANSI_BRIGHT_CYAN   = "\u001B[96m";
  public static final String ANSI_BRIGHT_WHITE  = "\u001B[97m";

  public static final String[] FOREGROUNDS = {
    ANSI_BLACK, ANSI_RED, ANSI_GREEN, ANSI_YELLOW,
    ANSI_BLUE, ANSI_PURPLE, ANSI_CYAN, ANSI_WHITE,
    ANSI_BRIGHT_BLACK, ANSI_BRIGHT_RED, ANSI_BRIGHT_GREEN, ANSI_BRIGHT_YELLOW,
    ANSI_BRIGHT_BLUE, ANSI_BRIGHT_PURPLE, ANSI_BRIGHT_CYAN, ANSI_BRIGHT_WHITE 
  };

  public static final String ANSI_BG_BLACK  = "\u001B[40m";
  public static final String ANSI_BG_RED    = "\u001B[41m";
  public static final String ANSI_BG_GREEN  = "\u001B[42m";
  public static final String ANSI_BG_YELLOW = "\u001B[43m";
  public static final String ANSI_BG_BLUE   = "\u001B[44m";
  public static final String ANSI_BG_PURPLE = "\u001B[45m";
  public static final String ANSI_BG_CYAN   = "\u001B[46m";
  public static final String ANSI_BG_WHITE  = "\u001B[47m";

  public static final String ANSI_BRIGHT_BG_BLACK  = "\u001B[100m";
  public static final String ANSI_BRIGHT_BG_RED    = "\u001B[101m";
  public static final String ANSI_BRIGHT_BG_GREEN  = "\u001B[102m";
  public static final String ANSI_BRIGHT_BG_YELLOW = "\u001B[103m";
  public static final String ANSI_BRIGHT_BG_BLUE   = "\u001B[104m";
  public static final String ANSI_BRIGHT_BG_PURPLE = "\u001B[105m";
  public static final String ANSI_BRIGHT_BG_CYAN   = "\u001B[106m";
  public static final String ANSI_BRIGHT_BG_WHITE  = "\u001B[107m";

  public static final String[] BACKGROUNDS = {
    ANSI_BG_BLACK, ANSI_BG_RED, ANSI_BG_GREEN, ANSI_BG_YELLOW,
    ANSI_BG_BLUE, ANSI_BG_PURPLE, ANSI_BG_CYAN, ANSI_BG_WHITE,
    ANSI_BRIGHT_BG_BLACK, ANSI_BRIGHT_BG_RED, ANSI_BRIGHT_BG_GREEN, ANSI_BRIGHT_BG_YELLOW,
    ANSI_BRIGHT_BG_BLUE, ANSI_BRIGHT_BG_PURPLE, ANSI_BRIGHT_BG_CYAN, ANSI_BRIGHT_BG_WHITE };

  public static void main(String[] args) {

    System.out.println("\n  Default text\n");

    for (String fg : FOREGROUNDS) {
      for (String bg : BACKGROUNDS)
        System.out.print(fg + bg + "  TEST  ");
      System.out.println(ANSI_RESET);
    }

    System.out.println(ANSI_RESET + "\n  Back to default.\n");

  }

} 
```

Enter fullscreen mode Exit fullscreen mode

这篇文章最初以稍微不同的形式出现在我的 Wordpress 博客上。