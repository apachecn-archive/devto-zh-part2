# 用 Purescript 格式化日期

> 原文：<https://dev.to/rinn7e/format-date-with-purescript-3g66>

这将是一个关于如何用 purescript 使用`purescript-formatters`解析日期的简单教程。我将使用 postgres: `2018-09-30T12:27:33.168791`中的日期作为例子

我们需要两个函数，一个将字符串转换为日期时间，另一个将显示日期时间值。

```
parseDate :: String -> Either DateTime
displayDate :: DateTime -> String 
```

Enter fullscreen mode Exit fullscreen mode

我们需要 2 个格式化程序来解码和显示:

```
parseFormatter :: Formatter
parseFormatter =  unsafePartial fromRight $  parseFormatString "YYYY-MM-DDTHH:mm:ss"

displayFormatter :: Formatter
displayFormatter =  unsafePartial fromRight $  parseFormatString "hh:mm:ss, DD MMM YYYY" 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以在之前的函数中使用这个格式化程序

```
parseDate :: String -> Either String DateTime
parseDate s = 
    unformat parseFormatter s

displayDate :: DateTime -> String
displayDate d = 
    format displayFormatter d 
```

Enter fullscreen mode Exit fullscreen mode

我们可以把它们结合在一起

```
parseAndDisplay :: String -> String
parseAndDisplay s = 
    either (\err -> err) (\d -> displayDate d) $ parseDate s 
```

Enter fullscreen mode Exit fullscreen mode

如果您运行这个程序，您可能会注意到一个运行时错误。因为我们的秒是零头。我们不需要那个，所以我们可以把它们放下。

```
parseAndDisplay :: String -> String
parseAndDisplay s = 
    either (\err -> err) (\d -> displayDate d) $ parseDate $ String.take 19 s 
```

Enter fullscreen mode Exit fullscreen mode

如果你用输入`2018-09-30T12:27:33.168791`运行这个，你将得到`12:27:33, 30 Sep 2018`

这就是全部代码

```
import Prelude 

import Data.Either (Either, either, fromRight)
import Data.String as String
import Data.DateTime (DateTime)

import Data.Formatter.DateTime (Formatter, parseFormatString, unformat, format)
import Partial.Unsafe (unsafePartial)

parseFormatter :: Formatter
parseFormatter =  unsafePartial fromRight $ parseFormatString "YYYY-MM-DDTHH:mm:ss"

displayFormatter :: Formatter
displayFormatter =  unsafePartial fromRight $ parseFormatString "hh:mm:ss, DD MMM YYYY"

parseDate :: String -> Either String DateTime
parseDate s = 
    unformat parseFormatter s

displayDate :: DateTime -> String
displayDate d = 
    format displayFormatter d

parseAndDisplay :: String -> String
parseAndDisplay s = 
    either (\err -> err) (\d -> displayDate d) $ parseDate $ String.take 19 s 
```

Enter fullscreen mode Exit fullscreen mode