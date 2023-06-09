# 在 Ruby 中使用 kot Lin/groovy 的“it”

> 原文：<https://dev.to/hanachin/use-kotlins--groovys-it-in-ruby-2haf>

我做了一个 gem“thas _ it”，有了这个 gem，你就可以用`it`的方法得到块的单个参数。

```
require "thats_it"

p [1, 2, 3].map { it * 2 }
# [2, 4, 6] 
```

Enter fullscreen mode Exit fullscreen mode

[https://github.com/hanachin/thats_it/](https://github.com/hanachin/thats_it/)

## 它是如何工作的？

基本思想是在调用之前重写块的 iseq。

```
# rewrites this block ↓
[1, 2, 3].map { it * 2 }

#          to this ↓
[1, 2, 3].map {|it| it * 2 } 
```

Enter fullscreen mode Exit fullscreen mode

我用日语写了一个实现细节。
[https://qiita.com/hanachin_/items/8df68325142d46a7fb0b](https://qiita.com/hanachin_/items/8df68325142d46a7fb0b)

如果你感兴趣，你可以通过谷歌翻译阅读。
[https://translate.google.com/translate?hl=ja&sl = ja&TL = en&u = https % 3A % 2F % 2f qiita . com % 2f hanachin _ % 2f items % 2f 8df 68325142d 46 a7 fb0b](https://translate.google.com/translate?hl=ja&sl=ja&tl=en&u=https%3A%2F%2Fqiita.com%2Fhanachin_%2Fitems%2F8df68325142d46a7fb0b)