# 我熟悉并喜爱的板条箱

> 原文：<https://dev.to/deciduously/crates-i-have-known-and-loved-3jhc>

# 我所知道和喜爱的板条箱

我喜欢写 Rust 有很多原因，但最重要的是工具设计得有多好。就我而言,`cargo`是我想从包管理器中得到的一切，如果需要，你可以创建自己的子命令。

[这本书](https://doc.rust-lang.org/stable/book/2018-edition/index.html)做了一件了不起的工作，让你开始运行，但它没有触及机箱生态系统。现在我已经在我的生锈之旅中深入了几个项目，我已经决定了几个“必须拥有的”,我的空白项目几乎总是以它们结束。我希望我从一开始就有这个列表——在我找到解决方案之前，我已经手工实现了这个功能太久了。如果你有更多的，让我知道！

## `error-chain`

[单据](https://docs.rs/error-chain/0.12.0/error_chain/)

这几乎总是我第一次加入任何项目。

Rust 的一个我最喜欢的特性是`?`操作符。如果一个操作返回一个`Result<T, E>`，你可以在上面加上一个问号，得到你通常想要的逻辑——成功将继续执行，失败将提前返回一个`Err`。

然而，这只有在你所在的函数返回完全相同的类型时才有效。通常情况不是这样——你可能有一个特定于应用程序的错误类型用于包含函数，但在内部调用来自`std::io`的东西——在这种情况下，除非你自己在所有地方实现错误类型转换，否则它不会起作用。这个`error-chain`箱子让你，嗯，链错误:

```
fn get_dir_listing(dir_str: &str) -> errors::Result<Vec<PathBuf>> {
    let dir_listing: Vec<PathBuf> = read_dir(dir_str)
                                    .chain_err(|| "could not read dir!")?
    //etc...
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我的错误只是一个简单的字符串，我将在连接到生成的底层`IO::Error`的 stacktrace 中看到它！它还提供了一个定制的`Result<T>`类型，可以自动使用您的错误链。这很好。

在应用程序代码中，使用这样的字符串通常可以完成工作，但是对于一个库来说，您会想要一个更健壮的自定义错误类型——这个机箱还提供了一个自以为是的结构来定义一个错误类型。到目前为止，我满足于让设置完全为空——你所需要的只是主页面中的以下内容，你就可以开始了:

```
extern crate error_chain;

mod errors {
    error_chain!{}
} 
```

Enter fullscreen mode Exit fullscreen mode

然后你改变`main()`——这是直接来自文档:

```
fn main() {
    if let Err(ref e) = run() {
        error!("error: {}", e);

        for e in e.iter().skip(1) {
            debug!("caused by: {}", e);
        }

        if let Some(backtrace) = e.backtrace() {
            trace!("backtrace: {:?}", backtrace);
        }

        ::std::process::exit(1);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，`run()`实际上是我们的主函数，但是它被适当地错误链接(返回一个`errors::Result`)——你的整个应用程序都是这样覆盖的。`if let`语法以简洁明了的方式准确表达了我们想要的行为。

只要在你需要的地方加上`use errors::*`。

## `structopt`

[单据](https://docs.rs/structopt/0.2.12/structopt/)

Structopt 感觉像作弊。搭建命令行应用的黄金标准是[鼓掌](https://clap.rs/)。Structopt 使得*比`clap`更容易*。它让你写下以下内容(从文档链接中):

```
#[derive(Debug, StructOpt)]
#[structopt(name = "example", about = "An example of StructOpt usage.")]
struct Opt {
    /// Activate debug mode
    #[structopt(short = "d", long = "debug")]
    debug: bool,
    /// Set speed
    #[structopt(short = "s", long = "speed", default_value = "42")]
    speed: f64,
    /// Input file
    #[structopt(parse(from_os_str))]
    input: PathBuf,
    /// Output file, stdout if not present
    #[structopt(parse(from_os_str))]
    output: Option<PathBuf>,
} 
```

Enter fullscreen mode Exit fullscreen mode

它会自动给你生成一个`clap::App`！代码片段中的三重斜杠 docstrings 将成为每个参数的帮助行。作为比较，下面是我在受到启发之前构建的一个项目中的“常用”方法:

```
let matches = App::new("ar-bot")
        .version(VERSION)
        .author("deciduously <ben@deciduously.com>")
        .about("Batching of auto email alerts")
        .arg(
            Arg::with_name("config")
                .short("c")
                .long("config")
                .value_name("CONFIG_FILE")
                .takes_value(true)
                .help("Specify an alternate toml config file"),
        )
        .arg(
            Arg::with_name("digest")
                .short("d")
                .long("digest")
                .takes_value(false)
                .help("Finalizes a digest with the emails in the brain. Make sure to preview first!")
        )
        .arg(
            Arg::with_name("email")
                .short("e")
                .long("email")
                .takes_value(false)
                .help("Placeholder command for developing email functionality"),
        )
        .arg(
            Arg::with_name("preview")
                .short("p")
                .long("preview")
                .takes_value(false)
                .help("Displays the current contents of the batch"),
        )
        .arg(
            Arg::with_name("report")
                .short("r")
                .long("report")
                .takes_value(false)
                .help("Daily report comparing inputs to outputs for the day"),
        )
        .arg(
            Arg::with_name("verbose")
                .short("v")
                .multiple(true)
                .help("Set RUST_LOG verbosity.  There are three levels: info, debug, and trace.  Repeat the flag to set level: -v, -vv, -vvv.")
        )
        .get_matches(); 
```

Enter fullscreen mode Exit fullscreen mode

对于同一个结束游戏，需要更多的输入，最后一切都已经方便地存储在您的`Opt` struct 中。Struct-opt:)

## `envy`

[github](https://github.com/softprops/envy)

这个板条箱与`structopt`相似，但环境变量不同。你定义了一个结构，它可以用任何存在的环境变量自动填充它:

```
#[macro_use]
extern crate serde_derive;
extern crate envy;

#[derive(Deserialize, Debug)]
struct Config {
  foo: u16,
  bar: bool,
  baz: String,
  boom: Option<u64>
}

fn main() {
    match envy::from_env::<Config>() {
       Ok(config) => println!("{:#?}", config),
       Err(error) => panic!("{:#?}", error)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，它将在运行时自动读取 FOO、BAR、BAZ 和 BOOM env 变量。

这是另一个手工做起来并不困难的任务，但是很乏味，你可能会一遍又一遍地做很多次。

## `serde`

[serde.rs](https://serde.rs)

至少对我来说，Serde 与 Rust 交织在一起，我敢肯定这对任何人来说都不奇怪，但这是一个非常可靠的解决方案。超级声音，惊人的速度。快速说五遍。

撇开字面意思不谈，当您需要进行任何序列化或反序列化时，serde 是一个不用动脑筋的人...通常情况下。我甚至没有包括一个代码片段，因为在大多数情况下，它可以通过一个注释获得您需要的所有功能，如果您需要的话，自己动手实现这些特征并不困难。又快又简单！

## `cargo-watch`

[github](https://github.com/passcod/cargo-watch)

观察文件的变化，重新运行您选择的`cargo`子命令，例如使用`cargo watch -x test -x run`。我没什么可说的了，这已经很说明问题了。这是我的必备。

## `pretty_env_logger`

[github](https://github.com/seanmonstar/pretty-env-logger)

这有点一箭双雕——它是一个彩色的包装，包裹着 [`env-logger`](https://docs.rs/env_logger/0.5.13/env_logger/) 。我没有开始使用后者，直到我发现这个箱子，虽然，颜色很好。

`env-logger`允许您通过环境变量设置记录输出级别。然后使用 [`log`](https://docs.rs/log/0.4.6/log/) 机箱中的宏:`info!`、`warn!`、`debug!`、`trace!`。运行代码时，将只显示指定级别的代码。这是对 println 调试的重大改进——您可以将调试打印输出留在中，然后只需设置一个“verbose”标志来抑制它们的正常使用。

我确信有一个更好的方法可以做到这一点，但是我已经把下面的函数放到了每个使用日志工具的项目中，它对我来说已经足够好了:

```
fn init_logging(level: u64) -> Result<()> {
    let verbosity = match level {
        0 => "warn",
        1 => "info",
        2 => "debug",
        3 | _ => "trace",
    };
    if verbosity == "trace" {
        set_var("RUST_BACKTRACE", "1");
    };
    set_var("RUST_LOG", verbosity);
    info!(
        "Attempting to set logger to {}",
        var("RUST_LOG").chain_err(|| "Failed to set verbosity level")?
    );
    pretty_env_logger::init();
    info!(
        "Set verbosity to {}",
        var("RUST_LOG").chain_err(|| "Failed to set verbosity level")?
    );
    Ok(())
} 
```

Enter fullscreen mode Exit fullscreen mode

它稍微简化了级别，使其更容易使用 0、1、2 或 3 个级别(`-v`、`-vv`、`-vvv`)的详细度标志，并确保如果您设置了`RUST_BACKTRACE`，无论如何您都会获得`trace`级别，如果您通过了`-vvv`，它将为您设置`RUST_BACKTRACE`。

## `pretty_assertions`

[github](https://github.com/colin-kiegel/rust-pretty-assertions)

这和`pretty_env_logger`一脉相承。这是彩色输出的`assert_eq!`的替代产品。您只需添加板条箱，不需要任何代码更改。当然，你是一个负责任的开发人员，并且到处都在使用`assert_eq!`——这只是让输出更容易阅读。

## `indicatif`

[链接](https://github.com/mitsuhiko/indicatif)

这个机箱提供了多个进度条和微调器，供您在命令行应用程序中使用。请参见 github 自述文件中的一些动画示例。

## `r2d2`

[单据](https://docs.rs/r2d2/0.8.2/r2d2/)

如果你做过任何数据库工作，这个箱子可能很熟悉，但我还是会把它扔进去，因为它很好。这是数据库的一个连接池。自述文件中:

> 每次需要时都打开一个新的数据库连接不仅效率低下，而且在高流量条件下会导致资源耗尽。连接池维护一组到数据库的开放连接，分发它们以供重复使用。

它是后端不可知的，很容易进入你的应用程序。有一个适配器可以轻松地将它与 [`diesel`](https://diesel.rs) ORM 一起使用。现在，当您需要时，不是直接连接到您的数据库，而是从池中请求一个连接，这一切都像预期的那样工作。我喜欢不费吹灰之力的性能提升，你呢？

## `pest`

[害虫种类](https://pest.rs)

这不会在所有项目中都有用，但它是我当前解析需求的首选。它比像`nom`这样自己动手的解析器组合器库更容易使用。你在一个单独的文件中定义你的整个语法。然后在你的锈码:

```
#[derive(Parser)]
#[grammar = "grammar.pest"]
struct GrammarParser; 
```

Enter fullscreen mode Exit fullscreen mode

作为一个例子，这里有一个小的(正在开发中的)前缀计算器的语法:

```
COMMENT = _{ "/*" ~ (!"*/" ~ ANY)* ~ "*/" }
WHITESPACE = _{ " " }

num = @{ int ~ ("." ~ digit*)? }
    int = { ("+" | "-")? ~ digit+ }
    digit = { '0'..'9' }

symbol = @{ "+" | "-" | "*" | "/" | "%" | "^" | "add" | "sub" | "mul" | "div" | "rem" | "pow" | "max" | "min" | "list" | "eval" }

sexpr = { "(" ~ expr* ~ ")" }

qexpr = { "{" ~ expr* ~ "}" }

expr = { num | symbol | sexpr | qexpr }

blispr = { SOI ~ expr* ~ EOI } 
```

Enter fullscreen mode Exit fullscreen mode

和相应的代码来读取解析后的输入:

```
fn lval_read(parsed: Pair<Rule>) -> Box<Lval> {
    match parsed.as_rule() {
        Rule::blispr | Rule::sexpr => {
            let mut ret = lval_sexpr();
            for child in parsed.into_inner() {
                // here is where you skip stuff
                if is_bracket_or_eoi(&child) {
                    continue;
                }
                ret = lval_add(&ret, lval_read(child));
            }
            ret
        }
        Rule::expr => lval_read(parsed.into_inner().next().unwrap()),
        Rule::qexpr => {
            let mut ret = lval_qexpr();
            for child in parsed.into_inner() {
                if is_bracket_or_eoi(&child) {
                    continue;
                }
                ret = lval_add(&ret, lval_read(child));
            }
            ret
        }
        Rule::num => lval_num(parsed.as_str().parse::<i64>().unwrap()),
        Rule::symbol => lval_sym(parsed.as_str()),
        _ => unreachable!(),
} 
```

Enter fullscreen mode Exit fullscreen mode

这个图书馆非常容易使用。我喜欢它将你的语法与代码完全分开的方式，而且 PEG 格式很容易理解。试一试吧！

## `actix_web`

[actix.rs](https://actix.rs)

这在一般情况下不太适用，但是如果我正在编写一个 web 服务器，这就是我想要的，毫不犹豫。Rust 生态系统中的 web 服务器之间的许多选择都可以归结为个人品味，但我喜欢这个服务器的速度，并且它自推出以来一直在稳定的分支上运行。

我没有机会在没有 web 服务器的情况下使用 actor 模型，但它看起来也很棒！

## `ggez`

[GGE . RS](http://ggez.rs/)

这是一个受[love](https://love2d.org/)启发的游戏框架，有一个更 Rustier 的 API。它很容易安装和运行——非常适合原型开发。

对于更大的游戏，我建议看看[紫水晶](https://www.amethyst.rs/)。它似乎是目前最有前途的发动机，并包裹着 [`specs`](https://github.com/slide-rs/specs) ，一个实体-组件系统。`specs`是不是只有*是我个人用过的* ECS，所以我真的不能拿它和其他的东西比较..不过话说回来，我觉得挺好的？

## svgbob

这个获得荣誉奖是因为它很酷，而不是因为它是一个图书馆。[去看看](https://ivanceras.github.io/svgbob-editor/) -它将 ASCII 图转换成 SVG。

如果我错过了你最喜欢的板条箱，在下面向我问好！