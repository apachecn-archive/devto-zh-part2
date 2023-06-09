# CSS 选择器和逻辑运算

> 原文：<https://dev.to/ln_north/css-selector-and-logical-operation-3dch>

[日文版(Qiita)](https://qiita.com/ln-north/items/bbfd0c271594ce7c1536)

# 或

[![](img/2b24ac93af26a1372cfbc7d4ddb3d82c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xlP75xKf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/99270/aa2c63d0-977e-b8f2-9108-19ed1b33b375.png)T3】

```
A, B {} 
```

Enter fullscreen mode Exit fullscreen mode

# 和

[![](img/83b998caa3156df222e91cac0b163f98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KEgGduBt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/99270/093fb92b-6ade-4de3-ed7a-37f3607a56d0.png)T3】

```
AB {} 
```

Enter fullscreen mode Exit fullscreen mode

# 不

[![](img/a7fd2d54518c7062e618a24e6976bda5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t32zOFQi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/99270/9a2ec2b9-954b-a253-fd65-98ed28f12bc5.png)T3】

```
:not(A) {} 
```

Enter fullscreen mode Exit fullscreen mode

# 也不是

[![](img/f5def5dfd72a894447a39664fdf12991.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TSYuRCP1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/99270/f770b801-30a4-ba31-e59c-aa3b5017c7a4.png)T3】

```
:not(A, B) {} /* Currently undefined syntax  */ 
```

Enter fullscreen mode Exit fullscreen mode

那条路是那么简单，但现在不行了。正在起草中的第 4 级选择器定义了这个语法。

在这种时候，德摩根定律是很方便的。

既然 NOR 不是“非`A`”和“非`B`”，那么可以这样写。

```
:not(A):not(B) {} 
```

Enter fullscreen mode Exit fullscreen mode

如果有三个或更多选择器，选择器会变脏。我希望实现四级选择器。

# 与非

[![](img/bd492e6f11de42d6dfe0635f1c3dc5d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n7vEUuyc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/99270/817bb4ec-f242-853e-2d4a-d5caaaf778ad.png)

那就更简单了。

```
:not(AB) {} 
```

Enter fullscreen mode Exit fullscreen mode

用与 NOR 相同的方式修改 Domorgan 定律，所以

```
:not(A), :not(B) {} 
```

Enter fullscreen mode Exit fullscreen mode

是可以的。

# 异或

[![](img/828de79594631dfb03f76f742606e9f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qTqv6olk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/99270/848af736-b41c-6814-77d7-f05aa897d7a3.png)

XOR 是**`A`而非`B`或者“非`A`而`B`**，所以可以这样写。

```
A:not(B), :not(A)B {} 
```

Enter fullscreen mode Exit fullscreen mode

(也许，`A:not(B), B:not(A)`更简单。)

# 印象

实际上，我正在考虑做什么事情，并在触发时开始考虑这个问题。

在做一些复杂的事情时，记住聚合逻辑，做这类事情，似乎很容易处理。