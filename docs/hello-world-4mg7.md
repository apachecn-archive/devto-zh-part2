# 你好世界！

> 原文：<https://dev.to/awwsmm/hello-world-4mg7>

你好，世界！程序是打印字符串“Hello world！”的一小段代码到终端/输出等。在大多数语言中，这是一项简单而无聊的任务:

# C

```
#include <stdio.h>  int main() {
  printf("Hello world!\n");
  return EXIT_SUCCESS;
} 
```

Enter fullscreen mode Exit fullscreen mode

# C++

```
#include <iostream>  int main() {
  std::cout << "Hello world!\n";
} 
```

Enter fullscreen mode Exit fullscreen mode

# Java

```
class HelloWorld {
  public static void main(String[] args) {
    System.out.println("Hello world!");
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

# Scala

```
object HelloWorld {
  def main(args: Array[String]): Unit = {
    println("Hello world!")
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

在 rosettacode.org 还有数百个这样的例子。我最喜欢的一些(我认为[最有趣的](https://en.wikipedia.org/wiki/Esoteric_programming_language))包括:

# 蜜蜡

[蜂蜡](https://esolangs.org/wiki/Beeswax)是一种二维编程语言，其中“蜜蜂”(指令指针)围绕着六边形的字符网格移动，从全局堆栈中拾取值，并在自己的“个人”(本地)固定大小(3 * UInt64)堆栈上操纵它们。bee 可以从任何地方获取值，修改它们，并把它们放到源代码中的任何地方，允许自我修改代码。一个*你好世界！*蜂蜡中的程序可能看起来像:

```
 W o    l   `
  ``     e  H
  `*`r`#`l`_`ld!
   ``      \/
   , o 
```

Enter fullscreen mode Exit fullscreen mode

这可以更容易地写成

```
 _`Hello, World! 
```

Enter fullscreen mode Exit fullscreen mode

但是这有什么意思呢？

# 厨师

Chef 是一种设计成看起来像菜谱的编程语言。程序由“湿”和“干”的“成分”组成，它们可以混合、搅拌、液化和组合在一起。迈克·沃斯[为*你好世界开发了一个配方*](http://www.mike-worth.com/2013/03/31/baking-a-hello-world-cake/)！巧克力蛋糕，厨师写的，这也是 IRL 巧克力蛋糕的一个不错的食谱:

```
Hello World Cake with Chocolate sauce.

This prints hello world, while being tastier than Hello World Souffle. The main
chef makes a " world!" cake, which he puts in the baking dish. When he gets the
sous chef to make the "Hello" chocolate sauce, it gets put into the baking dish
and then the whole thing is printed when he refrigerates the sauce. When
actually cooking, I'm interpreting the chocolate sauce baking dish to be
separate from the cake one and Liquify to mean either melt or blend depending on context.

Ingredients.
33 g chocolate chips
100 g butter
54 ml double cream
2 pinches baking powder
114 g sugar
111 ml beaten eggs
119 g flour
32 g cocoa powder
0 g cake mixture

Cooking time: 25 minutes.

Pre-heat oven to 180 degrees Celsius.

Method.
Put chocolate chips into the mixing bowl.
Put butter into the mixing bowl.
Put sugar into the mixing bowl.
Put beaten eggs into the mixing bowl.
Put flour into the mixing bowl.
Put baking powder into the mixing bowl.
Put cocoa  powder into the mixing bowl.
Stir the mixing bowl for 1 minute.
Combine double cream into the mixing bowl.
Stir the mixing bowl for 4 minutes.
Liquify the contents of the mixing bowl.
Pour contents of the mixing bowl into the baking dish.
bake the cake mixture.
Wait until baked.
Serve with chocolate sauce.

chocolate sauce.

Ingredients.
111 g sugar
108 ml hot water
108 ml heated double cream
101 g dark chocolate
72 g milk chocolate

Method.
Clean the mixing bowl.
Put sugar into the mixing bowl.
Put hot water into the mixing bowl.
Put heated double cream into the mixing bowl.
dissolve the sugar.
agitate the sugar until dissolved.
Liquify the dark chocolate.
Put dark chocolate into the mixing bowl.
Liquify the milk chocolate.
Put milk chocolate into the mixing bowl.
Liquify contents of the mixing bowl.
Pour contents of the mixing bowl into the baking dish.
Refrigerate for 1 hour. 
```

Enter fullscreen mode Exit fullscreen mode

# HQ9+

HQ9+ ( [如前文中提到的](https://dev.to/awwsmm/worlds-smallest-quine-guaranteed-b5m))是一种深奥的编程语言，只有四个命令:

*   *H* :打印“你好，世界！”
*   *Q* :打印程序的源代码(a quine)
*   *9* :打印歌曲《99 瓶啤酒》的歌词
*   *+* :递增一个不可访问的累加器(基本不做任何事情)

这种语言不是图灵完整的，它的解释器可以用不到 300 字节的 Haskell 代码编写。

# 提花织机

查尔斯·巴贝奇在 1837 年提出的[分析引擎](https://en.wikipedia.org/wiki/Analytical_Engine)(尽管不幸的是从未建造)将会是第一台通用的图灵完全计算机。与现代二进制计算机不同，它被设计为处理十进制数，并且它将完全是机械的——计算机的“记忆”被编码在轮轴上轮子的物理状态中。受 [Joseph Marie Jacquard 的机器](https://en.wikipedia.org/wiki/Jacquard_loom)的启发，分析引擎被设计成用穿孔卡编程，用于在织机上自动化编织图案。[在一个完全机械的过程中](http://www.vam.ac.uk/content/videos/j/video-jacquard-weaving)，这些打孔卡可以用来在织机上“编程”编织图案。下面的卡片序列编织了一串“你好，世界！”在提花织机上:

```
+---------------+
|               |
|    *    *     |
|*   *    *  *  |
|*           * *|
|*           * *|
|*  *         * |
|   *     *   * |
|         *     |
+---------------+

+---------------+
|               |
|*   *    *     |
|*   *    *     |
|            * *|
|            * *|
|*  *         * |
|*  *     *   * |
|         *     |
+---------------+

+---------------+
|               |
|*   **   * *   |
|*******  *** * |
| **** *   * ***|
| **** *  ******|
| ******   ** * |
|   * *   *   * |
|         *     |
+---------------+

+---------------+
|               |
|*******  *** * |
|*******  *** * |
|           ** *|
|*        *  * *|
|*******   ** * |
|*******  *** * |
|         *     |
+---------------+

+---------------+
|               |
|*******  *** * |
|*******  *** * |
|      *  *  * *|
|      *  *  * *|
|*******  **  * |
|*******  **  * |
|         *     |
+---------------+

+---------------+
|               |
|***** *  *** * |
|*******  *** * |
|     * * *  *  |
|     * *    *  |
|******   **  * |
|******   **  * |
|         *     |
+---------------+

+---------------+
|               |
|    *    * *   |
|***** *  ***** |
|***** **  * ***|
|***** **  * ***|
|*******   * ** |
|   * *   *   * |
|         *     |
+---------------+

+---------------+
|               |
|               |
|     * *       |
|     * *       |
|     *         |
|     *         |
|               |
|               |
+---------------+ 
```

Enter fullscreen mode Exit fullscreen mode

# LOLCODE

受 00 年代中期 LOLcats 迷因的启发， [LOLCODE](https://en.wikipedia.org/wiki/LOLCODE) 是一种带有`KTHX`和`BTW`等关键词的编程语言。一个*你好世界！* LOLCODE 程序可以写成:

```
HAI 1.2
CAN HAS STDIO?
VISIBLE "HAI WORLD!"
KTHXBYE 
```

Enter fullscreen mode Exit fullscreen mode

尽管 LOLCODE 解释器和编译器是用。NET、JavaScript 和 Python，并且已经为分布式/并行化计算编写了这种语言的版本，但它仍然是一种笑话语言，存在错误和冗余。例如，在上面的代码中，版本(1.2)是不必要的，大部分都被忽略了，第二行也是如此，它意味着导入一个 IO 库。在 https://repl.it/repls/ExtrasmallWastefulFolders 的[有一个 LOLCODE 翻译器。](https://repl.it/repls/ExtrasmallWastefulFolders)

# உயிர் / Uyir

உயிர் (Uyir)是泰米尔语的“生命力”、“灵魂”或“活力”的意思(根据谷歌翻译的“活力”)，目前正在开发中。它的目标是成为第一个 *omniglot* 编程语言，其中关键字、命令等。可以用任何人类语言编写并被解释器/编译器理解。不过，目前它似乎只在泰米尔语中有效。注意[有许多编程语言](https://en.wikipedia.org/wiki/Non-English-based_programming_languages)的关键字基于除英语之外的语言，有些使用非 ASCII 字符集(丙正正、γλψσσα、 *قلب* )，还有几个根本没有语言特定关键词的(APL、Brainfuck、Piet、Whitespace 等。).

这篇文章最初以稍微不同的形式出现在我的 Wordpress 博客上。