# 不要误解我:口译员的即兴测试

> 原文：<https://dev.to/adambrandizzi/dont-interpret-me-wrong-improvising-tests-for-an-interpreter-15je>

我爱上了 [*神石口译*](http://craftinginterpreters.com/) 的书。在这本书里， [Bob Nystrom](http://journal.stuffwithstuff.com/) 教我们如何通过实现一个叫做 [Lox](http://craftinginterpreters.com/the-lox-language.html) 的小编程语言来编写一个解释器。我已经很久没有这么开心地编程了！除了写得好之外，这本书还很有趣，而且比我想象的更有教育意义。但是我有一个问题。

bug 中的代码片段是以一种我们可以复制粘贴的方式编写的。然而，这本书在每一章的结尾都有挑战，这些挑战没有源代码，有时它们会迫使我们改变很多解释器。我做了这些练习中的每一个，结果我的解释者偏离了书中的来源太多。因此，我经常破坏我的解释器的某些部分。

如何解决这个问题？

由于代码结构经常变化，Unity 测试会变得脆弱。在这种情况下，端到端测试似乎更实用。所以，对于语言的每一个新特性，我都写了一个小程序。例如，我的解释器应该创建[闭包](http://craftinginterpreters.com/functions.html#local-functions-and-closures)，并确保我将下面的 Lox 程序复制到文件 [`counter.lox`](https://bitbucket.org/brandizzi/jlox/src/default/examples/counter.lox?at=default&fileviewer=file-view-default) :

```
fun makeCounter() {
  var i = 0;
  fun count() {
    i = i + 1;
    print i;
  }

  return count;
}

var counter = makeCounter();
counter(); // "1".
counter(); // "2". 
```

这个程序结果应该是打印在不同行的数字 1 和 2。所以我把这些值放在一个名为 [`counter.lox.out`](https://bitbucket.org/brandizzi/jlox/src/default/examples/counter.lox.out) 的文件中。程序也不能失败，所以我创建了一个名为 [`counter.lox.err`](https://bitbucket.org/brandizzi/jlox/src/default/examples/counter.lox.out) 的空文件。(在某些情况下，有必要确保 Lox 程序会失败。在这些情况下，文件`.lox.err`应该有内容。)

嗯，我为各种例子编写了程序和输出文件；现在我需要将程序的结果与预期的输出进行比较。我决定在紧急时刻使用[对我帮助最大的工具](https://dev.to/adambrandizzi/exchanging-sticker-cards-with-the-terminal-2ki9) : shell 脚本。我编写了一个 Bash 脚本，用一个`for`遍历所有的例子:

```
for l in examples/*.lox
do

done 
```

对于每个例子，我都执行 Lox 程序，将输出重定向到临时文件:

```
for l in examples/*.lox
do out=$(mktemp)
  err=$(mktemp)
  java -classpath target/classes/ br.com.brandizzi.adam.myjlox.Lox $l > $out 2> $err
done 
```

现在，我们通过 [`diff`](https://www.gnu.org/software/diffutils/) 比较实际输出和预期输出。当比较两个文件时， [`diff`若无差异则返回 0，若有差异则返回 1，若有错误则返回 2](https://stackoverflow.com/questions/6971284/what-are-the-error-exit-values-for-diff)。因为在 Bash 中，条件`if`认为 0 为真，所以我们只检查`diff`的退出代码的否定。

如果程序在标准输出中打印的东西不同于它的`.lox.out`文件中的东西，我们就失败了:

```
for l in examples/*.lox
do out=$(mktemp)
  err=$(mktemp)
  java -classpath target/classes/ br.com.brandizzi.adam.myjlox.Lox $l > $out 2> $err

  if ! diff $l.out $out
  then FAIL=1
  fi
done 
```

我们还检查了标准误差和`.lox.err`文件:

```
for l in examples/*.lox
do out=$(mktemp)
  err=$(mktemp)
  java -classpath target/classes/ br.com.brandizzi.adam.myjlox.Lox $l > $out 2> $err

  if ! diff $l.out $out
  then FAIL=1
  fi

  if ! diff $l.err $err
  then FAIL=1
  fi
done 
```

最后，我检查是否有故障并报告结果:

```
for l in examples/*.lox
do out=$(mktemp)
  err=$(mktemp)
  java -classpath target/classes/ br.com.brandizzi.adam.myjlox.Lox $l > $out 2> $err

  if ! diff $l.out $out
  then FAIL=1
  fi

  if ! diff $l.err $err
  then FAIL=1
  fi

  if ["$FAIL" = "1"]
  then echo "FAIL" $l
  else echo "PASS" $l
  fi
done 
```

然而，并不是我所有的 Lox 程序都能被检查。例如，有一个对循环执行进行计时的程序,不可能预测它将打印的值。因此，我增加了跳转一些程序的可能性:我们只需要创建一个扩展名为的文件[:](https://bitbucket.org/brandizzi/jlox/src/default/examples/timeit.lox.skip)

```
for l in examples/*.lox
do
  if [-e $l.skip]
  then echo SKIP $l
    continue
  fi out=$(mktemp)
  err=$(mktemp)
  java -classpath target/classes/ br.com.brandizzi.adam.myjlox.Lox $l > $out 2> $err

  if ! diff $l.out $out
  then FAIL=1
  fi

  if ! diff $l.err $err
  then FAIL=1
  fi

  if ["$FAIL" = "1"]
  then echo "FAIL" $l
  else echo "PASS" $l
  fi
done 
```

然而，如果我有一个 Lox 示例，它没有预期的输出文件(也没有`.lox.skip`文件),那么我有一个问题，整个脚本失败:

```
for l in examples/*.lox
do
  if [-e $l.skip]
  then echo SKIP $l
    continue
  elif [! -e $l.out ] || [! -e $l.err ]
  then echo missing $l.out or $l.err
    exit 1
  fi out=$(mktemp)
  err=$(mktemp)
  java -classpath target/classes/ br.com.brandizzi.adam.myjlox.Lox $l > $out 2> $err

  if ! diff $l.out $out
  then FAIL=1
  fi

  if ! diff $l.err $err
  then FAIL=1
  fi

  if ["$FAIL" = "1"]
  then echo "FAIL" $l
  else echo "PASS" $l
  fi
done 
```

至此，我的测试脚本完成了。让我们看看它是如何表现的:

```
$ ./lcheck.sh
PASS examples/attr.lox
PASS examples/bacon.lox
PASS examples/badfun.lox
PASS examples/badret.lox
PASS examples/bagel.lox
PASS examples/bostoncream.lox
PASS examples/cake.lox
PASS examples/checkuse.lox
PASS examples/circle2.lox
PASS examples/circle.lox
1d0
< 3
1c1
<
---
> [line 1] Error at ',': Expect ')' after expression.
FAIL examples/comma.lox
PASS examples/counter.lox
PASS examples/devonshinecream.lox
PASS examples/eclair.lox
PASS examples/fibonacci2.lox
PASS examples/fibonacci.lox
PASS examples/func.lox
PASS examples/funexprstmt.lox
PASS examples/hello2.lox
PASS examples/hello3.lox
PASS examples/hello.lox
PASS examples/math.lox
PASS examples/notaclass.lox
PASS examples/noteveninaclass.lox
PASS examples/point.lox
PASS examples/retthis.lox
PASS examples/scope1.lox
PASS examples/scope.lox
PASS examples/supersuper.lox
PASS examples/thisout.lox
PASS examples/thrice.lox
SKIP examples/timeit.lox
PASS examples/twovars.lox
PASS examples/usethis.lox
PASS examples/varparam.lox 
```

糟糕，显然我不小心移除了对[逗号操作符](https://en.wikipedia.org/wiki/Comma_operator)的支持。还好我写了这个剧本，对吧？

我希望这个帖子有一点点意思！现在，我要修复我的逗号运算符，继续阅读[这本精彩的书](http://craftinginterpreters.com/)。

*(此贴翻译自[no me interpret Mal:independent Ando Testes para um interpretator](http://suspensao.blog.br/descrenca/nao-me-interprete-mal-improvisando-testes-para-um-interpretador/)。)*