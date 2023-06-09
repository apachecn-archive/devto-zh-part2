# ClojureScript 练习模板

> 原文：<https://dev.to/deciduously/a-clojurescript-exercise-template-37ke>

Clojure 是一种奇妙的语言。它非常简单——需要学习的语法非常少，并且它绝对支持您选择的任何范式，同时仍然提供与大规模 Java 和 JavaScript 生态系统的无缝(真的，无缝)互操作。

然而不幸的是，使用它可能很棘手，而且对于快速实验来说有点过重。你不希望仅仅为了运行一个快捷的函数而启动一个 JVM 和引导语言。

ClojureScript 有一个名字，使它听起来像一个完全独立的东西，但它几乎是完全相同的语言。只是由 JavaScript 而不是 Java 托管。这样做的一个结果是脚本变得可行。你可以用 JVM Clojure 来做这件事，但是仍然有更多的启动时间。我最喜欢的选项叫做[普朗克](http://planck-repl.org/)。

几乎是瞬间开始的。我已经利用它来进行语言无关的代码练习——代码的出现指日可待！planck guide 应该有助于回答任何问题，并且还列出了许多非常有用的与 shell 交互和发出网络请求的内置功能——您可以使用它来代替 Python 或 Ruby 执行脚本任务。

这是我的空白模板:

```
(ns  exercise.core  (:require  [cljs.test  :refer-macros  [deftest  is  run-tests]]  ;[clojure.string :refer [split]] ; you can import clojure fns!  [planck.core  :refer  [slurp]]))  ;; IMPLEMENTATION  (defn  part1  "Write your first function here!"  [word]  (str  "Hello "  word)  ;; TESTS  (deftest  test-part1  (is  (=  (part1  "CLJS")  "Hello, World")))  ;; RUN  (defn  -main  []  (let  [puzzle  (slurp  "realinput.txt")]  (run-tests)  (println  (str  "Part 1 output: "  (part1  puzzle)))  (set!  *main-cli-fn*  -main) 
```

Enter fullscreen mode Exit fullscreen mode

这个设置将运行你的测试，然后使用内置的`slurp`来读取你给它的文件的内容到一个字符串中。

那你就跑吧！运行`planck exercise.cljs`并观察您的测试失败:)

下面是一个完整练习的例子:

```
(ns  d4.core  (:require  [cljs.test  :refer-macros  [deftest  is  run-tests]]  [clojure.string  :refer  [split]]  [planck.core  :refer  [slurp]]))  ;; IMPLEMENTATION  (defn  part1  "Get how many times w occurs in ws"  [w  ws]  (->>  ws  (filter  #{w})  count))  (defn  part2  "Get how many anagrams of w occur in ws"  [w  ws]  (->>  ws  (filter  #(=  (sort  w)  (sort  %)))  count))  (defn  valid?  "Check s for validity according to f"  [f  s]  (let  [words  (split  s  " ")]  (->>  (map  #(f  %  words)  words)  (filter  #(>  %  1))  (empty?))))  (defn  count-valid  "Given string, returns how many lines are valid"  [f  s]  (->>  (split  s  "\n")  (filter  #(valid?  f  %))  (count)))  ;; TESTS  (deftest  sample1  (is  (valid?  part1  "aa bb cc dd ee"))  (is  (not  (valid?  part1  "aa bb cc dd aa")))  (is  (valid?  part1  "aa bb cc dd aaa")))  (deftest  sample2  (is  (valid?  part2  "abcde fghij"))  (is  (not  (valid?  part2  "abcde xyz ecdab")))  (is  (valid?  part2  "a ab abc abd abf abj"))  (is  (valid?  part2  "iiii oiii ooii oooi oooo"))  (is  (not  (valid?  part2  "oiii ioii iioi iiio"))))  ;; RUN  (defn  -main  []  (let  [puzzle  (slurp  "d4.txt")]  (run-tests)  (println  (str  "Part 1 output: "  (count-valid  part1  puzzle)))  (println  (str  "Part 2 output: "  (count-valid  part2  puzzle)))))  (set!  *main-cli-fn*  -main) 
```

Enter fullscreen mode Exit fullscreen mode

编码快乐！