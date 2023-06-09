# StormScript:有什么新的和更好的

> 原文：<https://dev.to/stormprograms/stormscript-whats-new-and-whats-better-64m>

# [T1】简介](#intro)

周日，我发了一个帖子解释 StormScript 是什么，以及为什么我认为它很重要。人们指出了他们不喜欢的语法，接受程度基本上是积极的。我换了两个。我还更改了[存储库](https://github.com/stormprograms/StormScript),以便更容易找出所有东西的位置。此外，我添加了类方法。

# 语法变化

## `end;`

对我的帖子发表评论的两个人都谈到他们如何不喜欢每个代码块结尾的`end;`。我同意了，实际上我已经考虑改变这一点有一段时间了。

之前:

```
do {
    int x: 3;
    int y: 4:

    if x is y {
         printl x, " is equal to ", y;
    }else{
        printl x, " is not equal to ", y;
    }end;
}end; 
```

之后:

```
do {
    int x: 3;
    int y: 4:

    if x is y {
         printl x, " is equal to ", y;
    }else{
        printl x, " is not equal to ", y;
    }
} 
```

## 函数参数

函数参数的声明方式是一场噩梦。本来我觉得还可以，现在才知道占了*太多*不必要的空间。

之前:

```
@args: int x;
func foo {
    printl x;
}

do{
    foo => x: 3;
} 
```

之后:

```
func foo => int x {
    printl x;
}

do{
    foo => x: 3;
} 
```

# 类方法

类方法被声明为与普通函数相同，但是在`type`范围内。

```
type person {
    int age;
    str name;

    func birthday {
        age+: 1;
    }
}

do {
    person p;
    p|age: 22;
    p|name: "Joe";

    printl p|name, " is ", p|age, " years old!";

    p|birthday;

    printl "It's ", p|name, "'s birthday. ", p|name, " is ", p|age, " years old!";
} 
```

我认为我收到的反馈是有价值的，并将使 StormScript 变得更好。请继续给我发送建议。

当然，请查看[库](https://github.com/stormprograms/stormscript)，如果您想看到任何添加的内容，请务必提交或打开一个功能请求。