# 更简洁代码的简单技巧

> 原文：<https://dev.to/blackcat_dev/a-simple-tip-for-cleaner-code--1ib0>

当我在一个有很多遗留代码的代码库中工作时，我注意到很多函数都是这样写的。出于某种原因，团队中的老开发人员更喜欢这样写，让我很恼火的是他们仍然这样写。

```
 // Not so nice.

        function foo($bar)
        {

            if ($bar > 0) {
                return "YES";
            } else if ($bar == 0) {
                return "MAYBE"
            } else {
                return "NO";
            }
        }

        // Not so nice as well.

        function foo($bar)
        {
            $value = '';            

            if ($bar > 0) {
                $value = "YES";
            } else if ($bar == 0) {
                $value = "MAYBE"
            } else {
                $value = "NO";
            }

            return $value;
        } 
```

Enter fullscreen mode Exit fullscreen mode

在我看来，这对你的大脑来说是太多的噪音，我们可以通过利用早期回报使它更具可读性。

```
 // Very much nice, yes, yes, sexy.

        function foo($bar) 
        {
            if ($bar > 0) {
                return "YES";
            }

            if ($bar == 0) {
                return "MAYBE";
            }

            return "NO";    
        } 
```

Enter fullscreen mode Exit fullscreen mode

现在不是干净多了吗？不要做恐龙，用早收益！