# 如果您使用 SBCL，请使用 rlwrap

> 原文：<https://dev.to/taroyanaka/if-you-use-sbcl-use-rlwrap--1o8e>

也许你会尖叫“为什么↑键对 SBCL·REPL 不起作用”
我知道你的感受
lisper 可能认为 lisp 很棒，但 SBCL 是个笨蛋

SBCL 不保存历史，所以使用 rlwrap。

```
$ brew install rlwrap 
```

Enter fullscreen mode Exit fullscreen mode

把这一行加进去。巴沙尔还是喜欢那种

```
alias lisp="rlwrap sbcl --load quicklisp.lisp" 
```

Enter fullscreen mode Exit fullscreen mode