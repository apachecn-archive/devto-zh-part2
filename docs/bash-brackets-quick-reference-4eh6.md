# Bash 括号快速参考

> 原文：<https://dev.to/rpalo/bash-brackets-quick-reference-4eh6>

*封面图片鸣谢:[Fonts.com](https://www.fonts.com/content/learning/fontology/level-4/fine-typography/braces-and-brackets)T3】*

Bash 有许多不同种类的括号。就像，很多很多。它增加了不同括号重叠的意义，前面的美元符号意味着更不同的东西。*和*，括号的用法与其他许多语言不同。我经常发现自己在做一个 5 秒钟的搜索，寻找哪一个是正确的，因为我不是一直在写 Bash 脚本。所以在这里，我要把它们都摊开，然后把这篇文章打印出来，钉在我书桌旁的墙上。可能带有装饰框。所以我们开始吧。

有一点需要注意的是，Bash 通常喜欢看到圆括号或方括号之间的空格以及里面的内容。它不喜欢花括号所在的空间。我们将按照净总弯曲度(NTS 分数)的顺序进行讨论。

## (单括号)

单括号将运行**子外壳**中的命令。这意味着它们运行内部的所有命令，然后返回一个退出代码。任何声明的变量或环境变化都将被清除并消失。因为它在一个子 shell 中，如果你在一个循环中使用它，它会比你调用没有括号的命令*运行得慢一点。* 

```
a='This string'
( a=banana; mkdir $a )
echo $a
# => 'This string'
ls
# => ...
# => banana/ 
```

Enter fullscreen mode Exit fullscreen mode

## ((双括号))

这是用于整数运算的。您可以在这些括号内执行赋值、逻辑运算和数学运算，如乘法或模运算。但是，请注意没有输出。它们内部发生的任何变量变化都会保持不变，但是不要期望能够将结果赋给任何东西。如果里面的结果是**非零**，则返回一个**零**(成功)退出代码。如果里面的结果是**零**，则返回退出代码 **1** 。

```
i=4
(( i += 3 ))
echo $i
# => 7
(( 4 + 8 ))
# => No Output
echo $?  # Check the exit code of the last command
# => 0
(( 5 - 5 ))
echo $?
# => 1

# Strings inside get considered 'zero'.
(( i += POO ))
echo $i
# => 7

# You can't use it in an expression
a=(( 4 + 1 ))
# => bash: syntax error near unexpected token '(' 
```

Enter fullscreen mode Exit fullscreen mode

## <(尖括号)

*感谢[托马斯·H·琼斯二世](https://dev.to/ferricoxide)的评论启发了本节关于流程替代的内容*

[![ferricoxide profile image](../Images/466c896342efa0cde8b1cf9c5b9c6c25.png) ](/ferricoxide) [ Thomas H Jones II ](/ferricoxide) • [<time datetime="2018-06-20T23:37:30Z"> Jun 20 '18 </time> • Edited on <time datetime="2018-06-20T23:44:39Z">Jun 20</time>](https://dev.to/ferricoxide/comment/3pdn) 

你错过了一个真正伟大的 bashims:`<( COMMAND )`。基本上，在 subshell 中运行命令，然后通过文件描述符返回它的输出。这意味着你可以做这样的事情:

*   区分两个流
*   在 shell 中运行一个命令，为其他希望以文件而不是流的形式输入的命令创建一个输入“文件”。

另一个很棒的是`VAR=($( COMMAND ))`...它接受来自`COMMAND`的输出，并从中创建一个数组变量。

当您关心一个命令如何退出而不是它的输出时,`$( COMMAND )$?`也很有用(例如，您想测试看看 grep 是否在文件中找到一个字符串，`[[ $( grep -q PATTERN FILE )$? ]]`)。

这被称为*过程替代*。它很像管道，只是您可以在命令需要文件参数的任何地方使用它。而且可以一次用多个！

```
sort -nr -k 5 <( ls -l /bin ) <( ls -l /usr/bin ) <( ls -l /sbin )

# => Like a billion lines of output that contain many of the
# => executables on your computer, sorted in order of descending size.

# Just in case you don't magically remember all bash flags,
# -nr  means sort numerically in reverse (descending) order
# -k 5 means sort by Kolumn 5\.  In this case, for `ls -l`, that is the "file size" 
```

Enter fullscreen mode Exit fullscreen mode

这是因为 sort 命令需要一个或多个文件名作为参数。在幕后，`<( stuff )`实际上输出一个临时文件的名称(未命名的管道文件)供`sort`命令使用。

另一个方便的例子是使用`comm`命令，该命令显示文件共有的行。因为`comm`需要对其输入文件进行排序，您可以这样做:

```
# The lame way
sort file1 > file1.sorted
sort file2 > file2.sorted
comm -12 file1.sorted file2.sorted 
```

Enter fullscreen mode Exit fullscreen mode

哦，你完全可以这样做:

```
# The baller way
comm -12 <( sort file1 ) <( sort file2 ) 
```

Enter fullscreen mode Exit fullscreen mode

## $(美元单括号)

这是为了将 subshell 命令输出插入到字符串中。里面的命令在一个 subshell 中运行，然后任何输出都被放入您正在构建的任何字符串中。

```
intro="My name is $( whoami )"
echo $intro
# => My name is ryan

# And just to prove that it's a subshell...
a=5
b=$( a=1000; echo $a )
echo $b
# => 1000
echo $a
# => 5 
```

Enter fullscreen mode Exit fullscreen mode

## $(美元单括号美元 Q )$？

*大声喊出来，再次向[托马斯](https://dev.to/ferricoxide/comment/3pdn)索要小费！*

如果你想插入一个命令，但只是退出代码而不是值，这就是你所使用的。

```
if [[ $( grep -q PATTERN FILE )$? ]]
then echo "Dat pattern was totally in dat file!"
else echo "NOPE."
fi 
```

Enter fullscreen mode Exit fullscreen mode

虽然，实际上，这与其说是一个特殊的括号模式，不如说是对`$?`的一个有趣的使用，因为即使在`$( stuff )`和`$?`之间有一个空格，上面的内容仍然有效。不过，这是个不错的建议。

## $((美元双括号))

还记得正则**((双括号))**怎么什么都不输出？还记得那有多烦人吗？嗯，你可以使用 **$((美元双括号))**来执行一个**算术插值**，这只是一种花哨的说法，“将输出结果放入这个字符串中。”

```
a=$(( 16 + 2 ))
message="I don't want to brag, but I have like $(( a / 2 )) friends."
echo $message
# => I don't want to brag, but I have like 9 friends."

b=$(( a *= 2 ))         # You can even do assignments.  The last value calculated will be the output.
echo $b
# => 36
echo $a
# => 36 
```

Enter fullscreen mode Exit fullscreen mode

需要记住的一点是，这是严格的整数运算。没有小数。查看 [`bc`](https://www.lifewire.com/use-the-bc-calculator-in-scripts-2200588) 进行浮点运算。

```
echo $(( 9 / 2 ))  # You might expect 4.5
# => 4

echo $(( 9 / 2.5 ))
# => bash: 9 / 2.5 : syntax error: invalid arithmetic operator (error token is ".5 ") 
```

Enter fullscreen mode Exit fullscreen mode

## 【单方括号】

这是内置`test`的替代版本。运行里面的命令并检查“真实性”零长度的字符串是假的。长度为一个或多个的字符串(即使这些字符是空白)为真。

[这里列出了所有你可以做的与文件相关的测试](http://tldp.org/LDP/abs/html/fto.html)，比如检查一个文件是否存在或者它是否是一个目录。

[这里列出了你可以做的所有字符串相关和整数相关的测试](https://www.tldp.org/LDP/abs/html/comparison-ops.html)，比如检查两个字符串是否相等，或者一个字符串的长度是否为零，或者一个数字是否大于另一个数字。

```
if [ -f my_friends.txt ]
then echo "I'm so loved!"
else echo "I'm so alone."
fi 
```

Enter fullscreen mode Exit fullscreen mode

最后需要注意的一点是，`test`和`[`实际上是 shell 命令。`[[ ]]`实际上是*外壳语言本身*的一部分。这意味着双方括号内的内容不被视为参数。使用单方括号的原因是，如果你需要进行*分词*或*文件名扩展*。

这里有一个区别的例子。假设您以如下方式使用了双方括号。

```
[[ -f *.txt ]]
echo $?
# => 1 
```

Enter fullscreen mode Exit fullscreen mode

False，没有明确命名为“[星号]”的文件。txt”。让我们假设我们的目录中目前没有`.txt`文件。

```
# If there's no files .txt files:
[ -f *.txt ]; echo $?
# => 1 
```

Enter fullscreen mode Exit fullscreen mode

`*.txt`被扩展成一个空字符串，它不是一个文件，然后*和*测试被评估。让我们创建一个 txt 文件。

```
touch cool_beans.txt
# Now there's exactly one .txt file
[ -f *.txt ]; echo $?
# => 0 
```

Enter fullscreen mode Exit fullscreen mode

`*.txt`被扩展为一个空格分隔的匹配文件名列表:“cool_beans.txt”，然后用这个参数对测试进行评估。由于文件存在，测试通过。但是如果有两个文件呢？

```
touch i_smell_trouble.txt  # bean pun.  #sorrynotsorry
# Now there's two files
[ -f *.txt ]
# => bash: [: too many arguments. 
```

Enter fullscreen mode Exit fullscreen mode

`*.txt`被展开为“cool _ beans . txt I _ smeet _ trouble . txt”，然后对测试进行评估。Bash 将每个文件名作为一个参数，收到 3 个参数，而不是预期的 2 个，然后进行模糊处理。

为了强调我的观点:即使目前有两个`.txt`文件，下一个测试仍然失败。

```
[[ -f *.txt ]]; echo $?
# => 1\.  There is still no file called *.txt 
```

Enter fullscreen mode Exit fullscreen mode

我试着举出一些例子来说明为什么你会想要这个，但是我想不出现实的例子。

> 在很大程度上，一个好的经验法则是:如果你需要使用`test`或`[ ]`，你会知道的。如果你不确定你是否需要它，你可能不需要它，你可能应该使用 **[[双方括号]]** 来避免很多`test`命令的棘手问题。如果你的外壳足够现代，可以拥有它们。

## [[双方括号]]

真/假测试。通读上一节，了解单方括号和双方括号之间的区别。此外，双方括号支持扩展正则表达式匹配。用引号将第二个参数括起来，以强制进行原始匹配，而不是正则表达式匹配。

```
pie=good
[[ $pie =~ d ]]; echo $?
# => 0, it matches the regex!

[[ $pie =~ [aeiou]d ]]; echo $?
# => 0, still matches

[[ $pie =~ [aei]d ]]; echo $?
# => 1, no match

[[ $pie =~ "[aeiou]d" ]]; echo $?
# => 1, no match because there's no literal '[aeoiu]d' inside the word "good" 
```

Enter fullscreen mode Exit fullscreen mode

同样，在双方括号内，`<`和`>`根据您的地区进行排序。在单个方括号中，它是根据机器的排序顺序，通常是 ASCII。

## {单花括号}

单花括号用于展开。

```
echo h{a,e,i,o,u}p
# => hap hep hip hop hup
echo "I am "{cool,great,awesome}
# => I am cool I am great I am awesome

mv friends.txt{,.bak}
# => braces are expanded first, so the command is `mv friends.txt friends.txt.bak` 
```

Enter fullscreen mode Exit fullscreen mode

最酷的是你还可以做射程！带前导零！

```
echo {01..10}
01 02 03 04 05 06 07 08 09 10
echo {01..10..3}
01 04 07 10 
```

Enter fullscreen mode Exit fullscreen mode

## $ {美元大括号}

请注意，内容周围没有空格。这是用于变量插值。当普通的字符串插值变得不可思议时，你可以使用它

```
# I want to say 'bananaification'
fruit=banana
echo $fruitification
# => "" No output, because $fruitification is not a variable.
echo ${fruit}ification
# => bananaification 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用**$ { Dollar brackets }**的另一个功能是变量操作。以下是一些常见的用法。

如果变量未定义，则使用默认值。

```
function hello() {
  echo "Hello, ${1:-World}!"
}
hello Ryan
# => Hello Ryan!
hello
# => Hello World!

function sign_in() {
    name=$1
  echo "Signing in as ${name:-$( whoami )}"
}
sign_in
# => Signing in as ryan
sign_in coolguy
# => Signing in as coolguy 
```

Enter fullscreen mode Exit fullscreen mode

获取变量的长度。

```
name="Ryan"
echo "Your name is ${#name} letters long!"
# => Your name is 4 letters long! 
```

Enter fullscreen mode Exit fullscreen mode

切下符合图案的碎片。

```
url=https://assertnotmagic.com/about
echo ${url#*/}     # Remove from the front, matching the pattern */, non-greedy
# => /assertnotmagic.com/about
echo ${url##*/}    # Same, but greedy
# => about
echo ${url%/*}     # Remove from the back, matching the pattern /*, non-greedy
# => https://assertnotmagic.com
echo ${url%%/*}    # Same, but greedy
# => https: 
```

Enter fullscreen mode Exit fullscreen mode

*感谢[桑吉·帕特尔](https://dev.to/sanketjpatel)修复错误！*

可以大写匹配字母！

```
echo ${url^^a}
# => https://AssertnotmAgic.com/About 
```

Enter fullscreen mode Exit fullscreen mode

你可以得到琴弦的切片。

```
echo ${url:2:5}  # the pattern is ${var:start:len}.  Start is zero-based.
# => tps:// 
```

Enter fullscreen mode Exit fullscreen mode

你可以替换图案。

```
echo ${url/https/ftp}
# => ftp://assertnotmagic.com

# Use a double slash for the first slash to do a global replace
echo ${url//[aeiou]/X}
# => https://XssXrtnXtmXgXc.cXm 
```

Enter fullscreen mode Exit fullscreen mode

并且，可以间接使用变量*作为其他变量*的名称。

```
function grades() {
  name=$1
  alice=A
  beth=B
  charles=C
  doofus=D
  echo ${!name}
}

grades alice
# => A
grades doofus
# => D
grades "Valoth the Unforgiving"
# => bash: : bad substitution. 
# There is no variable called Valoth the Unforgiving,
# so it defaults to a blank value. 
# Then, bash looks for a variable with a name of "" and errors out. 
```

Enter fullscreen mode Exit fullscreen mode

## < <双角 Heredocs

这就是在 Bash 中创建多行字符串的方法(一种方法)。两个箭头和一个单词——你选择的任何单词——来表示字符串的开始。直到你重复你的咒语，这串才会结束。

```
read -r -d '' nice_message <<MESSAGE Hi there!  I really like the way you look
when you are teaching newbies things
with empathy and compassion!
You rock! MESSAGE echo "$nice_message"
# => Hi there!  I really like the way you look
# => when you are teaching newbies things
# => with empathy and compassion!
# => You rock!

# Note, if you store a string with newlines in a variable, 
# you have to quote it to get the newlines to actually print.
# If you just use `echo $nice_message`, it won't reflect newlines. 
```

Enter fullscreen mode Exit fullscreen mode

这个词可以是任何你想要的。我通常会使用“HEREDOC”来方便未来的我。

伊恩·柯克评论区的真正古代智慧:

[![ikirker profile image](../Images/122384c84dbad454ac29b6377d854397.png) ](/ikirker) [ Ian Kirker ](/ikirker) • [<time datetime="2018-06-21T12:46:58Z"> Jun 21 '18 </time>](https://dev.to/ikirker/comment/3pig) 

关于 HEREDOC bits 的一些事情:

*   当与命令一起使用时，这里的文件被输入到命令的`stdin`中。你在第一个例子中用来给变量赋值的方法不起作用，至少对我和我的 bash 4.4.19 来说是这样。

```
$ nice_message=<<MESSAGE
Hi there!  I really like the way you look
when you are teaching newbies things
with empathy and compassion!
You rock!
MESSAGE
$ echo $nice_message

$ 
```

Enter fullscreen mode Exit fullscreen mode

```
$ cat <<MESSAGE
Hi there!  I really like the way you look
when you are teaching newbies things
with empathy and compassion!
You rock!
MESSAGE
Hi there!  I really like the way you look
when you are teaching newbies things
with empathy and compassion!
You rock!
$ 
```

Enter fullscreen mode Exit fullscreen mode

*   如果在开始的标签上加上单引号，那么 HEREDOC 将被视为单引号字符串，而变量表达式则不包含在内。

```
$ cat <<EOF
$RANDOM
EOF
4578

$ cat <<'EOF'
$RANDOM
EOF
$RANDOM 
```

Enter fullscreen mode Exit fullscreen mode

*   您还可以使用不带标签的三倍小于符号来输入一个简单的字符串，如下所示:

```
$ cat <<EOF
beep
EOF
beep

$ cat <<<beep
beep
$ 
```

Enter fullscreen mode Exit fullscreen mode

最后一个技巧是，如果你在箭头后面添加一个破折号，它会隐藏你的 heredoc 中的任何前导制表符(*而不是空格*)。

```
cat <<-HEREDOC two leading tabs
    one leading tab
  two spaces here HEREDOC # => two leading tabs
# => one leading tab
# =>   two spaces here 
```

Enter fullscreen mode Exit fullscreen mode

## 标点符号是黑仔

希望这是有帮助的。如果你看到我错过的东西，或者这些变体中的一个有另一个很酷的用法，一定要让我知道，我会更新它，并公开赞扬你的天才。感谢阅读！