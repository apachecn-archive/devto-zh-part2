# Rust 中的高阶函数

> 原文：<https://dev.to/deciduously/higher-order-functions-in-rust-287h>

Rust 是一种命令式语言，但它在标准库中提供了许多工具，这些工具遵循更具功能性的风格，如 [`Iterator`](https://doc.rust-lang.org/std/iter/trait.Iterator.html) 特征及其方法，如`map`、`for_each`和`filter`。这是如何在 Rust 中定义自己的高阶函数的快速运行，Rust 既可以接受闭包作为参数，也可以返回闭包，这样就可以一起使用两者。

编辑 [@theodesp](https://dev.to/theodesp) 正确地指出，这些例子没有遵循纯粹的函数式风格——我在适当的地方改变了输入。我这样写是因为标准库中已经很好地涵盖了纯解决方案，并且这个用例不太清楚，但是我已经用一些手工实现更新了 [Rust Playground](https://play.integer32.com/?version=stable&mode=debug&edition=2015&gist=8639706958a3b51389474b328331d9d8) 作为比较。

为了演示这些，我们将使用 2D 数字网格:

```
type Grid = Vec<Vec<i32>>;

fn prepare_grid(rows: i32, columns: i32) -> Grid {
    let mut ret = Vec::new();
    for _ in 0..rows {
        let mut row = Vec::new();
        for _ in 0..columns {
            row.push(0i32)
        }
        ret.push(row);
    }
    ret
} 
```

Enter fullscreen mode Exit fullscreen mode

该功能会将网格初始化为所有的【T0:】T1

```
fn main() {
    let mut grid = prepare_grid(5, 5);
    println!("{:?}", grid);
}
// [[0, 0, 0, 0, 0], [0, 0, 0, 0, 0], [0, 0, 0, 0, 0], [0, 0, 0, 0, 0], [0, 0, 0, 0, 0]] 
```

Enter fullscreen mode Exit fullscreen mode

无论邪恶(？)目的对于数字网格，一次处理一行可能会很有用。我们可以使用实现了 [`Fn`](https://doc.rust-lang.org/std/ops/trait.Fn.html) 特征的通用数据类型来接受闭包作为参数:

```
fn map_rows<F>(grid: &mut Grid, func: F)
where
    F: Fn(&mut Vec<i32>)
{
    for row in grid {
        func(row)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

例如，现在我们可以增加每一行的第一个元素:

```
map_rows(&mut grid, |row: &mut Vec<i32>| row[0] += 1);

println!("{:?}", grid);
// [[1, 0, 0, 0, 0], [1, 0, 0, 0, 0], [1, 0, 0, 0, 0], [1, 0, 0, 0, 0], [1, 0, 0, 0, 0]] 
```

Enter fullscreen mode Exit fullscreen mode

由于 Rust 管理生命周期的方式，返回一个函数有点棘手。如果你想决定第一个值增加多少。你需要返回一个`Fn(&mut Vec<i32>)`。问题是，Rust 需要在编译时计算返回函数的生命周期。我们必须明确地告诉编译器，只要输入参数存在，这个函数就会一直存在，方法是使用一个引用，并给它分配生存期`'a` :

```
fn make_incrementer<'a>(amount:&'a i32) -> Box<Fn(&mut Vec<i32>) + 'a> {
    Box::new(move |row: &mut Vec<i32>| row[0] += amount)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 [`Box`](https://doc.rust-lang.org/std/boxed/index.html) 指针类型，这样`make_incrementer`的返回类型的大小在编译时是已知的，并且使用`move`闭包来确保为闭包分配一个新的堆栈帧，我们将`amount`复制到其中，从而避开`make_incrementer`的堆栈帧。

与`map_rows`一起使用需要一些改变:

```
fn map_rows2<F>(grid: &mut Grid, func: Box<F>)
where
    F: for<'a> Fn(&'a mut Vec<i32>) + ?Sized
{
    for row in grid {
        (*func)(row)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在有一个显式的生命周期来处理，但一般来说，它至少需要和整个函数一样长。在这种情况下，编译器会抱怨，因为它只在函数内部调用闭包时才进入作用域，在我们的`for`循环中——明显比整个函数短。`for<...>`语法是一个称为高级特征界限的特性，它告诉编译器显式计算*最小*生存期来调用我们的闭包，而不是用`map_rows2<'a, F>(...)`为整个函数定义它，从而规避了这个问题。

我们还需要将整个`Box`作为我们的参数传入，因为所有局部变量*必须*是`Sized`(尽管显然未调整大小的局部变量正在工作中)。你不需要取得所有权- `&Box<F>`就可以了。然而，这将导致 Rust 反常，因为现在我们*不像以前那样*有`F`的`Sized`值。为了让`make_incrementer`编译，我们需要创建一个，但是我们已经通过解包完成了所有的工作。幸运的是，有一个逃生出口——通过增加`?Sized`我们可以放松这个要求。唯一的另一个变化是在 for 循环中使用`(*func)`进行实际的闭包。

现在我们可以去*野生* :

```
map_rows2(&mut grid, make_incrementer(&2));

println!("{:?}", grid);
// [[3, 0, 0, 0, 0], [3, 0, 0, 0, 0], [3, 0, 0, 0, 0], [3, 0, 0, 0, 0], [3, 0, 0, 0, 0]]
// we already added 1! 
```

Enter fullscreen mode Exit fullscreen mode

注意:这个例子在没有引用/生存期的情况下也能很好地工作，因为我们的`amount`有类型`i32`，它实现了`Copy`——move 闭包会为你复制它。这意味着您也可以在`map_rows2`中省略它们。只有当你的工厂函数的参数不是`Copy`时，引用才是绝对必要的——比如说`String`。

总之，在 Rust 中使用高阶函数肯定比在垃圾收集函数式语言中更麻烦，但绝对不是不可能。你只需要稍微按摩一下借款检查器，确保你知道你到底想要什么！

这是在 [Rust Playground](https://play.integer32.com/?version=stable&mode=debug&edition=2015&gist=8639706958a3b51389474b328331d9d8) 上可以修改的完整样本。