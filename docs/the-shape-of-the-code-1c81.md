# 代码的形状

> 原文：<https://dev.to/almenon/the-shape-of-the-code-1c81>

代码是有形状的。对于一个小脚本，它可能是一个简单的箭头。东西进，东西出。一个大的脚本可能是一团意大利面条，箭头指向各个方向。十字路口，十字路口，死胡同，都形成了一个可怕的混乱。随着代码越来越多，结越来越紧，直到最终无法解开。“[放弃所有希望叶谁进入这里](https://stackoverflow.com/questions/35772001/how-to-handle-the-signal-in-python-on-windows-machine/35792192#comment81619892_35792192)”评论警告说，因为一个单一的变化可能导致整个系统着火。由于缺乏测试和来自高层的压力，大多数代码都是这样结束的。

[![alt text](img/faef571103470b522165b5e7b2acdfce.png "https://xkcd.com/844/")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BzrJOkMA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hsi2jthwsq1k6qyh9l17.png)

但是在好的系统中，它不是一个结，而是一个网格。输入遵循合理的正确路径，并返回逻辑输出。从输出来看，可以用更多的逻辑应用更多的代码。如果代码是好的，最终另一个库会把它作为一个依赖项捡起来，而那个库又捡起那个库，你最终会得到巨大的代码摩天大楼。主要例子:任何流行的 npm 包。这就像阅读 Adam 的 geneology:vs code 使用 gulp-chmod，它使用 gulp-symdest，依赖 vinyl-fs，依次依赖 glob-stream，它使用 micromatch，但它使用大括号，它有 expand-range，它当然使用 fill-range，还有 randomatic，最后是-number。这是一座塔。

[![alt text](img/2a8006bf65a72ec9a23ef4a8a42d5df2.png "you better hope is-number doesnt get hacked, or else the whole tower might fall")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Oy9UBJTd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ph44g1ijxx769q81pdxr.jpeg)

或者可能代码的形状是一个金字塔——vs code 不只有一个依赖项，它有大量的依赖项。一个库依赖于许多其他库，而其他库又依赖于其他库，如此循环往复，呈指数级增长。

[![alt text](img/d5a54405a060abcb3d58b84e60522a8c.png "If you printed out books of all the code in the world and stacked them up it would be at least this high")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HTLoPlJh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AHd1p9SEP797mkIx6_ZHpdA.jpeg)

指数级…那听起来像一棵树！一棵倒置的树，当然，但这是计算机科学的土地。只要我们在那里，为什么是一棵树？你可以把代码看作一个分形——你越深入了解它，它就越令人困惑。

[![alt text](img/e6996f7dc47ccd5b8a8df353f7d2cc5a.png "psychedelic AND healthy!")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---9W5nbkM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8tdfchs29evfumqq0ybc.jpeg)

但是为什么是分形的花椰菜呢？也许是洋葱。你剥开一层代码，还有另一层。该层中的一个函数调用更深层的一个库，该库向后端发出一个 web 请求，后端将请求传递给遗留数据库中的一个古老的存储过程，很快编辑器中就打开了 20 个文件，你就像在剥一个真正的洋葱一样在循环。

[![alt text](img/f1f928f94d183fc0467ef6fdc21d59df.png "Onions: a powerful metaphor for both ogres and code")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--V1rb5eTg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f6iqhg1ia7nldm13t0ei.jpeg)

就我个人而言，我喜欢将代码作为明星的想法。明白了吗？[明星？？](https://en.wikipedia.org/wiki/A*_search_algorithm)

….我会出去的。

* * *

你是什么形状？