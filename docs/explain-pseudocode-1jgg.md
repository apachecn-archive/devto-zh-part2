# 解释伪代码

> 原文：<https://dev.to/dragonwocky/explain-pseudocode-1jgg>

好吧。我刚刚开始学习 Python，我发现它比伪代码更容易理解。这个帖子不是一个严肃的辩论，而是为了满足我的好奇心。

通常我想出一个想法，没有太多的计划就投入编码，所以在此之前写伪代码对我来说是一个未知数。我在谷歌上搜索了一下，基本概念很简单——“伪”(fake)代码，用简单的英语编写，这样开发者就可以很容易地将其转换成几乎任何语言。有道理。

现在，我的问题是语法。它实际上有任何商定/推荐的语法吗，或者它只是字面上写什么？如果它推荐了大多数开发者都同意的语法，那会是什么呢？我在网上找到的大多数东西可能都有一两句关于伪代码的话，大部分都是关于流程图的，或者看起来彼此完全不一致。

举个例子，这个简单的抛硬币的 Python 脚本:

```
# import modules import random
import time

# flip coin def flip():
    coin = ''
    rand = bool(random.getrandbits(1))
    if rand:
        coin = 'heads'
    else:
        coin = 'tails'
    return coin

# allow for multiple games playagain = 'Y'
while playagain.lower() in {'y', 'yes', ''}:
    print('The coin spins in midair...')
    time.sleep(1)
    print('...and lands on \033[1m' + flip() + '\033[0m!')
    playagain = input('\nDo you wish to flip the coin again (Y/N)? ') 
```

Enter fullscreen mode Exit fullscreen mode

我试图为此写一些伪代码。人们会说下面哪一个更好？谁能演示一下他们将如何为此编写伪代码？

```
# attempt 1

set playagain to yes
while (playagain is y, yes, or an empty string (ignoring capitalisation)) do
    tell user the coins spins in midair
    wait 1 second
    run function "flip"
        set rand to a randomly generated booleen (true/false)
        if (rand is true)
            set coin to heads
        else (rand is false)
            set coin to tails
        return coin
    tell user what the result of "flip" was
    ask user if they want to play more games and set playagain to user response

# attempt 2

FUNCTION flip() =
    rand = random booleen
    if (rand)
        coin = heads
    else
        coin = tails
    return coin

playagain = yes
while (playagain == y, yes, or an empty string (ignoring capitalisation)) do
    output "The coin spins in midair..."
    sleep 1 second
    ouput "...and lands on " flip()
    output "Do you wish to flip the coin again (Y/N)?"
    playagain = input 
```

Enter fullscreen mode Exit fullscreen mode

对你们大多数人来说，这可能看起来很荒谬，但我会感谢任何帮助！