# 你用 Java 编写‘if(foo = = true)’代码吗？

> 原文：<https://dev.to/chooyan/do-you-code-if-foo--true--in-java--4imn>

有一个关于“你用 Java 编写`if (foo == true)`代码吗？”的讨论在日本科技博客 Qiita 上。

[https://qiita.com/ikemo/items/4f56a283f9e27cf98d81](https://qiita.com/ikemo/items/4f56a283f9e27cf98d81)

作者认为它是“不”的，因为它冗长(`if (foo)`就足够了)，它可能会导致`if (foo = true)`的错别字，并且它会增加步骤的数量，运行速度会更慢。

所以，代码应该是这样的。

```
 if (foo) {
    // do something
}

boolean isBuzz = !obj.getFoo().getBar();
if (!isBuzz) {
    // do something
} 
```

Enter fullscreen mode Exit fullscreen mode

而不是

```
 if (foo == true) {
    // do something
}

if (obj.getFoo().getBar() == false) {
    // do something
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，也有很多反驳的观点，认为`!`运算符容易被忽略，`== true`易于理解，两种方法都可以接受，等等。

基本上我是赞成作者观点的。`== true`对可读性没有任何好处，简单来说`if (isSuccessful())`读起来很舒服。

这个怎么看？

谢了。