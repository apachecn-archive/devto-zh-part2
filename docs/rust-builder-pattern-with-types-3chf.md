# 带有类型的 Rust builder 模式

> 原文：<https://dev.to/mindflavor/rust-builder-pattern-with-types-3chf>

## [T1】简介](#intro)

Rust 有非常丰富的类型系统。它也有移动语义。结合使用这两个特性，您可以使用构建器模式构建您的 API。让我们看一个实际的例子来说明为什么你会想要使用构建器模式以及如何实现 is(好吧，我向你展示的是一种实现它的方法，不一定是最好的)。

### 举例

假设我们有一个带很多参数的函数:其中一些是强制的，另一些是可选的。例如:

```
fn cook_pasta(
    pasta_type: String,
    pasta_name: Option<String>,
    pasta_length: u64,
    altitude: u64,
    water_type: Option<String>,
) {
    // your code here
} 
```

Enter fullscreen mode Exit fullscreen mode

这个方法有两个可选参数和三个强制参数。用起来很丑:

```
cook_pasta("Penne".to_owned(), None, 100, 300, Some("Salty".to_owned())); 
```

Enter fullscreen mode Exit fullscreen mode

这里的问题是，我们必须显式地告诉该方法忽略一个可选字段(`pasta_name`)，并显式地指定另一个可选字段(`water_type`)。成为一个选项迫使我们用`Some()`来包装可选字段。位置字段也很难解释。例如，我们有两个`u64`字段:不看方法签名真的很难区分什么是什么。100 指的是`pasta_length`还是`altitude`？这妨碍了可读性，因为我们不得不在代码中来回跳转，只是为了弄清楚发生了什么。

使用构建器模式，我们想要实现这样的东西:

```
cook_pasta()
    .with_pasta_type("Penne".to_owned())
    .with_pasta_length(100)
    .with_altitude(300)
    .with_water_type("Salty".to_owned())
    .execute(); 
```

Enter fullscreen mode Exit fullscreen mode

这个语法确实更好:我们一看就知道什么是`pasta_length` (100)，什么是`altitude` (300)。此外，我们不需要在`Some()`中包装可选字段，也不需要对我们不想传递给函数的可选字段使用`None`。可读性更好，但代价是多了一个`execute()`方法调用。但是如何实现呢？

## 构建器结构

这里的技巧是有一个我们不断移动的`builder`对象，在这个过程中添加字段。`builder`对象将拥有所有方法字段。大概是这样:

```
#[derive(Debug, Clone, Default)]
struct CookPastaBuilder {
    pub pasta_type: String,
    pub pasta_name: Option<String>,
    pub pasta_length: u64,
    pub altitude: u64,
    pub water_type: Option<String>,
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，out `cook_pasta()`函数(我们称之为`cook_pasta2()`以区别于以前的版本)只是创建了该结构的一个默认实例。

```
fn cook_pasta2() -> CookPastaBuilder {
    CookPastaBuilder::default()
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的`cook_pasta`代码将使用上面定义的`CookPastaBuilder`:
运行

```
impl CookPastaBuilder {
    fn execute(&self) {
        // your code here
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以这样使用它:

```
let mut cpb = cook_pasta2();
cpb.water_type = Some("Penne".to_owned());
cpb.pasta_length = 100;
cpb.altitude = 300;
cpb.water_type = Some("Salty".to_owned());

cpb.execute(); 
```

Enter fullscreen mode Exit fullscreen mode

不完全是我们想要的，但比原来的方法好。这个解决方案有两个问题:首先，你没有办法强制强制强制字段。如果你忘记设置一个强制字段，你会在运行时注意到它(坏，坏，坏！).其次，语法繁琐。我们将首先解决第二个问题。

### 四处移动

我们可以使用*移动*方法来公开内部字段，而不是访问内部字段。比如:

```
impl CookPastaBuilder {
    fn with_pasta_type(self, pasta_type: String) -> CookPastaBuilder {
        CookPastaBuilder {
            pasta_type,
            pasta_name: self.pasta_name,
            pasta_length: self.pasta_length,
            altitude: self.altitude,
            water_type: self.water_type,
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

注意三件事:

1.  我们是*消费*的`self`对象。这意味着我们不能再在它上面调用方法(方法定义中的`self`)。
2.  我们构造了一个新的`CookPastaBuilder`，复制了前一个*中的所有字段，除了我们想要赋值的字段*(在示例中为`pasta_type`)。
3.  我们将新生成的`CookPastaBuilder`传递给调用者(它成为新的所有者)。这样，我们可以将这些调用链接起来，因为它们是在同一个对象上进行的。事实上，我们在每次调用时都在改变`CookPastaBuilder`。

我们也可以去掉`Some()`包装，把它放在 setter 函数中:

```
 fn with_water_type(self, water_type: String) -> CookPastaBuilder {
        CookPastaBuilder {
            pasta_type: self.pasta_type,
            pasta_name: self.pasta_name,
            pasta_length: self.pasta_length,
            altitude: self.altitude,
            water_type: Some(water_type),
        } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以这样做:

```
cook_pasta2()
        .with_pasta_type("Penne".to_owned())
        .with_pasta_length(100)
        .with_altitude(300)
        .with_water_type("Salty".to_owned())
        .execute(); 
```

Enter fullscreen mode Exit fullscreen mode

不错！但是我们仍然必须解决强制现场检查的问题。例如，这个编译很好:

```
cook_pasta2().execute(); 
```

Enter fullscreen mode Exit fullscreen mode

但是这一点也不好(例如，烹饪意大利面而不首先实际上有意大利面是不会好的)。

### 四面类型

假设现在我们有两个`builder`而不是一个。`CookPastaBuilderWithoutPastaType`和`CookPastaBuilderWithPastaType`(是的，我知道，他们很烂，但请原谅我)。

如果我们只在`WithPasta`变体上定义`execute`方法，我们可以确保没有人能够在`WithoutPasta`变体上调用它。在编译时。这很好。

所以我们的逻辑流程是:

1.  调用`cook_pasta2()`会产生一个`CookPastaBuilderWithoutPastaType`。
2.  调用`with_pasta_type(..)`将消耗`CookPastaBuilderWithoutPastaType`并返回一个`CookPastaBuilderWithPastaType`。
3.  现在调用`execute()`将会起作用，因为`CookPastaBuilderWitPastaType`实现了该方法。

如果我们先调用`execute()`而不调用`with_pasta_type(..)`，我们会得到一个编译器错误。

但是，嘿，我们刚刚处理了一个必填字段。我们有三个！当然，你可以想出像`CookPastaBuilderWithoutPastaTypeWithoutAltitudeWithoutPastaLength`和*这样的东西，所有的排列*(这里是 12 个)，像`CookPastaBuilderWithPastaTypeWithoutAltitudeWithPastaLength`和`CookPastaBuilderWithoutPastaTypeWithoutAltitudeWithPastaLength`...但是有一个更好的方法，用更少的按键就可以做到。

### 仿制药？

我们可以使用泛型来实现更好的折衷。我们有三个必填字段:

1.  `pasta_type`
2.  `pasta_length`
3.  `latitude`

我们可以模拟一个布尔类型的值的*存在*或*不存在*。类似于:

```
#[derive(Debug, Default)]
pub struct Yes;
#[derive(Debug, Default)]
pub struct No; 
```

Enter fullscreen mode Exit fullscreen mode

我们的新班级变成了:

```
struct CookPastaBuilder<PASTA_TYPE_SET, PASTA_LENGTH_SET, ALTITUDE_SET> 
```

Enter fullscreen mode Exit fullscreen mode

这些通用类型可以是`No`或`Yes`，表示特定字段已经被赋值。例如设置`pasta_type`字段:

```
 fn with_pasta_type(
        self,
        pasta_type: String,
    ) -> CookPastaBuilder<Yes, PASTA_LENGTH_SET, ALTITUDE_SET> {
        CookPastaBuilder {
            pasta_type,
            pasta_name: self.pasta_name,
            pasta_length: self.pasta_length,
            altitude: self.altitude,
            water_type: self.water_type,
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

这个函数将返回一个新的`CookPastaBuilder`实例，在`Yes`上设置了`PASTA_NAME_SET`，这样我们就知道这个字段已经被设置了。注意，在这种情况下，我们返回了`PASTA_LENGHT_SET`和`ALTITUDE_SET`，因为我们不想改变这些字段的底层类型(它们可以被设置，也可以不设置，我们在这里不做改变)。

对每个强制字段都这样做意味着我们最终得到这样的类型:

```
CookPastaBuilder<Yes, Yes, Yes> 
```

Enter fullscreen mode Exit fullscreen mode

这为我们提供了编译时保证，我们的调用者已经设置了所有的强制字段。我们所需要做的就是将`execute()`函数的执行约束到这个特定的类型:

```
impl CookPastaBuilder<Yes, Yes, Yes> {
    fn execute(&self) {
        // your code here
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结果

从客户端的角度来看，我们的 API 还是一样的，这个调用是有效的:

```
cook_pasta2()
    .with_pasta_type("Penne".to_owned())
    .with_pasta_length(100)
    .with_altitude(300)
    .with_water_type("Salty".to_owned())
    .execute(); 
```

Enter fullscreen mode Exit fullscreen mode

但这并不是因为我们忘记了设置强制的`altitude`参数:

```
cook_pasta2()
    .with_pasta_type("Penne".to_owned())
    .with_pasta_length(100)
    .with_water_type("Salty".to_owned())
    .execute(); 
```

Enter fullscreen mode Exit fullscreen mode

错误将是这样的:

[![](img/8f29b8cc3cef7ea470e5de0110b881c9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k68cHTPq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l8qp2ouatf69tsq8hfp2.png)

`no method named execute found for type CookPastaBuilder<Yes, Yes, No> in the current scope`不是很有帮助，但至少你可以在运行前发现错误。至少泛型类型名将帮助您确定缺少什么:

[![](img/a452407b9bf9c84da3c6e1167a55e5dc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W90GBKtp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iisn0oqqmdutkfj9y97p.png)

也就是说，如果您将必要的`WHERE`添加到结构声明:

```
#[derive(Debug, Clone, Default)]
pub struct CookPastaBuilder<PASTA_TYPE_SET, PASTA_LENGTH_SET, ALTITUDE_SET>
where
    PASTA_TYPE_SET: ToAssign,
    PASTA_LENGTH_SET: ToAssign,
    ALTITUDE_SET: ToAssign,
{
    pasta_type_set: PhantomData<PASTA_TYPE_SET>,
    pasta_length_set: PhantomData<PASTA_LENGTH_SET>,
    altitude_set: PhantomData<ALTITUDE_SET>,

    pasta_type: String,
    pasta_name: Option<String>,
    pasta_length: u64,
    altitude: u64,
    water_type: Option<String>,
} 
```

Enter fullscreen mode Exit fullscreen mode

### 最后一位:幻影

这就是我们想要的:具有优雅语法的构建器模式，编译时检查。但是我们使用了一个*假的*泛型，这是一个在我们的代码中没有被积极使用的泛型。Rust 对此表示不满，所以为了让他高兴，我们添加了三个新字段，类型为`PhantomData`，每个泛型一个字段。不要担心，它不会在运行时出现，所以添加它没有成本(除了拥有它们的麻烦之外)。

这一个的最终代码。让我知道你对它的想法！

```
use std::fmt::Debug;
use std::marker::PhantomData;

#[derive(Debug, Default)]
pub struct Yes;
#[derive(Debug, Default)]
pub struct No;

pub trait ToAssign: Debug {}
pub trait Assigned: ToAssign {}
pub trait NotAssigned: ToAssign {}

impl ToAssign for Yes {}
impl ToAssign for No {}

impl Assigned for Yes {}
impl NotAssigned for No {}

pub fn cook_pasta(
    pasta_type: String,
    pasta_name: Option<String>,
    pasta_length: u64,
    altitude: u64,
    water_type: Option<String>,
) {
    // your code here
    println!(
        "cooking pasta! -> {:?}, {:?}, {:?}, {:?}, {:?}",
        pasta_type, pasta_name, pasta_length, altitude, water_type
    );
}

#[derive(Debug, Clone, Default)]
pub struct CookPastaBuilder<PASTA_TYPE_SET, PASTA_LENGTH_SET, ALTITUDE_SET>
where
    PASTA_TYPE_SET: ToAssign,
    PASTA_LENGTH_SET: ToAssign,
    ALTITUDE_SET: ToAssign,
{
    pasta_type_set: PhantomData<PASTA_TYPE_SET>,
    pasta_length_set: PhantomData<PASTA_LENGTH_SET>,
    altitude_set: PhantomData<ALTITUDE_SET>,

    pasta_type: String,
    pasta_name: Option<String>,
    pasta_length: u64,
    altitude: u64,
    water_type: Option<String>,
}

impl<PASTA_TYPE_SET, PASTA_LENGTH_SET, ALTITUDE_SET>
    CookPastaBuilder<PASTA_TYPE_SET, PASTA_LENGTH_SET, ALTITUDE_SET>
where
    PASTA_TYPE_SET: ToAssign,
    PASTA_LENGTH_SET: ToAssign,
    ALTITUDE_SET: ToAssign,
{
    pub fn with_pasta_type(
        self,
        pasta_type: String,
    ) -> CookPastaBuilder<Yes, PASTA_LENGTH_SET, ALTITUDE_SET> {
        CookPastaBuilder {
            pasta_type_set: PhantomData {},
            pasta_length_set: PhantomData {},
            altitude_set: PhantomData {},
            pasta_type,
            pasta_name: self.pasta_name,
            pasta_length: self.pasta_length,
            altitude: self.altitude,
            water_type: self.water_type,
        }
    }

    pub fn with_pasta_name(
        self,
        pasta_name: String,
    ) -> CookPastaBuilder<PASTA_TYPE_SET, PASTA_LENGTH_SET, ALTITUDE_SET> {
        CookPastaBuilder {
            pasta_type_set: PhantomData {},
            pasta_length_set: PhantomData {},
            altitude_set: PhantomData {},
            pasta_type: self.pasta_type,
            pasta_name: Some(pasta_name),
            pasta_length: self.pasta_length,
            altitude: self.altitude,
            water_type: self.water_type,
        }
    }

    pub fn with_pasta_length(
        self,
        pasta_length: u64,
    ) -> CookPastaBuilder<PASTA_TYPE_SET, Yes, ALTITUDE_SET> {
        CookPastaBuilder {
            pasta_type_set: PhantomData {},
            pasta_length_set: PhantomData {},
            altitude_set: PhantomData {},
            pasta_type: self.pasta_type,
            pasta_name: self.pasta_name,
            pasta_length,
            altitude: self.altitude,
            water_type: self.water_type,
        }
    }

    pub fn with_altitude(
        self,
        altitude: u64,
    ) -> CookPastaBuilder<PASTA_TYPE_SET, PASTA_LENGTH_SET, Yes> {
        CookPastaBuilder {
            pasta_type_set: PhantomData {},
            pasta_length_set: PhantomData {},
            altitude_set: PhantomData {},
            pasta_type: self.pasta_type,
            pasta_name: self.pasta_name,
            pasta_length: self.pasta_length,
            altitude,
            water_type: self.water_type,
        }
    }

    pub fn with_water_type(
        self,
        water_type: String,
    ) -> CookPastaBuilder<PASTA_TYPE_SET, PASTA_LENGTH_SET, ALTITUDE_SET> {
        CookPastaBuilder {
            pasta_type_set: PhantomData {},
            pasta_length_set: PhantomData {},
            altitude_set: PhantomData {},
            pasta_type: self.pasta_type,
            pasta_name: self.pasta_name,
            pasta_length: self.pasta_length,
            altitude: self.altitude,
            water_type: Some(water_type),
        }
    }
}

impl CookPastaBuilder<Yes, Yes, Yes> {
    pub fn execute(&self) {
        // your code here
        println!("cooking pasta! -> {:?}", self);
    }
}

pub fn cook_pasta2() -> CookPastaBuilder<No, No, No> {
    CookPastaBuilder::default()
}

fn main() {
    cook_pasta("Penne".to_owned(), None, 100, 300, Some("Salty".to_owned()));

    cook_pasta2()
        .with_pasta_type("Penne".to_owned())
        .with_pasta_length(100)
        .with_water_type("Salty".to_owned())
        .with_altitude(300)
        .execute();
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

快乐编码

弗朗切斯科·科努