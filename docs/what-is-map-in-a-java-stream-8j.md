# Java 流中的“映射”是什么

> 原文：<https://dev.to/monknomo/what-is-map-in-a-java-stream-8j>

*[最初发表于 www.gunnargissel.com](https://www.gunnargissel.com/map-in-java-stream.html)*

映射是如何用流将一种类型的对象转换成另一种类型的对象。假设您有一套`Fruit`并且您想向人们展示您的套中有什么。有一个水果名称的列表会有所帮助。

```
fruitList.stream().map(fruit::getName).collect(Collectors.toList); 
```

Enter fullscreen mode Exit fullscreen mode

这很简单。你可以想象在现实生活中如何用一篮水果做到这一点。

拿起一个水果，写下它的名字。拿起另一个水果，写下它的名字，等等。

贴图还可以让你在现实生活中不容易模拟。假设你有一个水果集，你想要橙子，而不是苹果(我觉得这比交换更接近嬗变，但这是个比喻，ymmv)。

你可以用 Java 做到这一点:

```
fruitList.stream().map(fruit -> {
    if( fruit instanceof Apple){
        return new Orange();
    }
    return  fruit;
}).collect(Collectors.toSet); 
```

Enter fullscreen mode Exit fullscreen mode

**如果你喜欢这篇文章，[注册我的邮件列表，每月更新有趣的编程文章](https://www.gunnargissel.com/pages/email-signup-1.html)**