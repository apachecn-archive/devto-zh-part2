# 让不可能的事情变得不可能

> 原文：<https://dev.to/stevensonmt/making-impossible-things-impossible-in-rust-3bkg>

我看了理查德·费尔德曼的演讲[让不可能的状态变得不可能](https://www.youtube.com/watch?v=IcgmSRJHu_8)当我还在学一点榆树的时候。我不确定我是否理解了他演讲中描述的执行过程，但是用正确的数据结构来避免错误的概念一直困扰着我。我正在进行迄今为止我最雄心勃勃的 Rust 项目，并努力获得合适的数据结构。

问题集中在需要为应用于 30 多个数据库集的 web API 创建搜索字符串，每个数据库都有唯一但重叠的可用搜索字段，最多可达 70 个。我需要它不可能为给定的数据库选择一个不合适的字段。

在下面的一般化代码中，我想分享一些我在设计过程中逐渐明白的方法。在这样做的过程中，我希望提高我自己和其他人对 Rust 中类型安全、特征实现和数据结构的理解。

```
enum Foo {
    Foo1(Foo1Field),
    Foo2(Foo2Field),
}

enum Foo1Field {
    Foo1Field1,
    Foo1Field2,
}

enum Foo2Field {
    Foo2Field1,
    Foo2Field2,
}

struct Bar {
    foo: Foo
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这种组织，你就知道每个 Bar 实例总是有一个“field”类型适合于指定的 Foo 变量。不过，尝试访问这些“字段”有点麻烦。这部分是因为 Foo1 和 Foo2 的元组是不同类型的。

另一种选择是更多地利用特征。

```
enum Foo1Field {
    Foo1Field1,
    Foo1Field2,
}

enum Foo2Field {
    Foo2Field1,
    Foo2Field2,
}

trait Foo {}

impl Foo for Foo1Field {}
impl Foo for Foo2Field {}

struct Bar<T: Foo> {
    foo: T
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这个结构，我不再需要一个`Foo`枚举，我只需要“field”枚举来实现`Foo`特征。Bar struct 现在接受任何为其`foo`字段实现了`Foo`特征的枚举。访问字段值现在变得很简单。

```
let bar = Bar { foo: Foo1Field::Foo1Field1 };
bar.foo // returns Foo1Field1 //
let bar2 = Bar { foo: Foo2Field::Foo2Field2 };
bar2.foo // returns Foo2Field2 
```

Enter fullscreen mode Exit fullscreen mode

这也允许我在不复制字段的情况下向各种数据库集合添加字段。我只是为新集合实现了一个特征。希望其他人会发现这很有帮助。我期待着听到更多的见解或对这类问题的替代方法的建议。感谢阅读。