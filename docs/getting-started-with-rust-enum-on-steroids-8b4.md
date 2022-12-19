# Rust 入门:类固醇上的枚举！

> 原文：<https://dev.to/mnivoliez/getting-started-with-rust-enum-on-steroids-8b4>

*最初发布在我的[博客](https://www.mathieu-nivoliez.com/posts/2018-04-01-getting-started-with-rust-enum-on-steroids.html)*

大家好！今天的主题很难决定。但由于前一个相当乏味，我决定去一个更容易说的主题。所以，今天我们就来聊聊 Rust 里的 enum！

## “是啊，....但是什么是枚举呢？”

我先解释一下这个。枚举是一种列出了所有可能值的类型。
举个例子，比方说我可以照顾不同的狗:doggo，doge，pupper。

然后我们可以定义一个枚举来表示:

```
enum DogKind {
  Doggo,
  Doge,
  Pupper
} 
```

Enter fullscreen mode Exit fullscreen mode

我们来分解一下。首先，我们使用关键字`enum`来表达我们将要定义一个枚举。然后是 enum 的名字，在我们的例子中是 *DogKind* 。然后，在`{ ... }`之间，我们定义与昏迷分离的 *DogKind* 的所有可能值。

我们现在可以这样使用它:

```
fn take_care_of_dog(kind: DogKind) {
  match kind {
    DogKind::Doggo => take_care_of_doggo(),
    DogKind::Doge => take_care_of_doge(),
    DogKind::Pupper => take_care_of_pupper(),
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你还记得在以前的文章中我说过大声读出代码是一个好习惯吗？Rust 中的枚举是一个很好的实践。让我们为了训练而做它。
我们声明了一个名为`take_care_of_dog`的函数`fn`，它带有一个`DogKind`类型的参数`kind`。我们知道`DogKind`是之前定义的一个枚举。然后我们检查参数`kind`与`DogKind`的哪个值匹配。对于每一个可能的匹配，我们定义一个要遵循的动作。这里箭头可以读作*然后，做那个*，所以匹配语句可以读作*如果它匹配 Doggo，那么做那个*。

## “好吧，我以前也用过其他语言，这只是一种切换情况，不是吗？”

很公平。但这仅仅是开始。让我给你看些真正有趣的东西。

让我们再举一次之前的例子，稍微升级一下:

```
enum DogKind {
  Doggo(String),
  Doge(String, String, String),
  Pupper { age: f32 }
} 
```

Enter fullscreen mode Exit fullscreen mode

## “搞什么鬼......脆皮鸭？”

如果你从来没有使用过带标记联合的语言，这通常是你的回答。让我解释一下，对于 *DogKind* 的每个*值*，我们希望有特定的值，特定的属性。
你可以这样用这个:

```
fn say_something_about_dog(kind: DogKind) {
  match kind {
    DogKind::Doggo(name) => println!("Good boy, {}", name),
    DogKind::Doge(such, wow, much) => println!("Such {}, wow {}, much {}", such, wow, much),
    DogKind::Pupper { age } => println!("Oh this pupper is {} years old", age)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## “哇，这样的变种...但是我们是否忽略了将所有可能的情况表达到匹配语句中？”

匹配是详尽的，这意味着你必须描述所有的案例。
嗯，有一个 syntaxic 元素可以让你描述“默认情况”:占位符`_`。

```
fn say_something_about_dog(kind: DogKind) {
  match kind {
    DogKind::Doggo(name) => println!("Good boy, {}", name),
    DogKind::Doge(such, wow, much) => println!("Such {}, wow {}, much {}", such, wow, much),
    DogKind::Pupper { age } => println!("Oh this pupper is {} years old", age),
    _ => println!("What is that? it's not a dog... Is it alive?"),
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是，我再怎么强调你也不为过，默认情况是不可靠的。例如，假设您添加了一种狗:T2 狗。那么带有`_`的匹配语句将不会编译失败，因为这种新的匹配语句将属于默认情况。
再说一遍，`_`不是*邪恶的*，它也是一种强大的力量，必须小心使用。

## “做同样的事情换不同的价值怎么办？”

我们可以用`|` :
轻松做到这一点

```
enum DogKind {
  Doggo,
  Doggy,
  Doge,
  Pupper
}

fn great_dog(kind: DogKind) {
  match kind {
    Doggo | Doggy => println!("Who is a good boy? It's you!"),
    Doge => println!("Wow, such elagance, much raffinment"),
    Pupper => println!("How adorable!"),
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## “好的，关于 enum 和 match 表达式，还有其他我们需要知道的吗？”

是啊。首先你可以在匹配表达式中使用括号:

```
enum DogKind {
  Doggo,
  Doggy,
  Doge,
  Pupper
}

fn great_dog(kind: DogKind) {
  match kind {
    Doggo | Doggy => println!("Who is a good boy? It's you!"),
    Doge => println!("Wow, such elagance, much raffinment"),
    Pupper => {
      println!("How adorable!");
      println!("I pet the pupper");
    },
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以使用匹配表达式作为赋值。

## “等等...什么？”

没错。假设你得到了一个依赖于狗的类型的值，你可以这样写:

```
enum DogKind {
  Doggo,
  Doggy,
  Doge,
  Pupper
}

fn great_dog(kind: DogKind) {
  let action = match kind {
    Doggo | Doggy | Pupper => "Pet the dog",
    Doge => "Pay your respect to the venerable doge"
  }; // note the ";" at the end of the assignment expression.
  println!("{}", action); 
} 
```

Enter fullscreen mode Exit fullscreen mode

## “好吧，好像有用……”

是的，你想知道吗？你可能已经用过了。
`Result<T,E>`和`Option<T>`本身就是枚举。
它们是列举特征的好例子...

但是下次我们会看到特征:p

所以，下次再见，不要犹豫评论任何错误，建议或任何关于这篇文章的东西。

马修