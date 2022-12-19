# 在 Go 中处理命令行选项

> 原文：<https://dev.to/nasa9084/go-3fma>

*这篇文章最初出现在[blog . web-apps . tech](https://blog.web-apps.tech/)T3 上*

## TL；速度三角形定位法(dead reckoning)

*   `github.com/jessevdk/go-flags`很方便

## 标志封装

[![Goでコマンドラインオプションを処理する](img/dd5740205bc897e3a70eeee6816673e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--U6N-EOGd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.web-apps.tech/conteimg/2018/02/gopher.png)

当您想要创建命令行工具时，无法避免的是如何处理命令行选项。

在 Go 语言中，标准软件包中存在一个名为`flag`的软件包，通过它可以解析命令行选项。

但是，`flag`打包中无法一次性定义长期权和短期权，另外，长期权也好短期权也好`-XXX`这种连字符为一个的形式

这不太常见，<sup>[[1]](#fn1)</sup> ，很难说方便吧。

因此，方便的是[`jessevdk/go-flags`](https://github.com/jessevdk/go-flags)封装。

## go-flags 封装

[`jessevdk/go-flags`](https://github.com/jessevdk/go-flags)软件包正如其名，是处理命令行选项的软件包。

短选项、长选项自不必说，汇总指定短选项，用不同参数多次指定同一选项，支持从环境变量的读取、默认值的指定等，是一般的选项

选项可以定义为结构体，因此在解析后的处理中也很容易处理。

让我们看一个简单的例子。

```
type options struct {
    Name string `short:"n" long:"name" description: "listen address"`
}

func main() {
    var opts options
    if _, err := flags.Parse(&opts); err != nil {
        // some error handling
        return
    }
    fmt.Printf("Hello, %s\n", opts.name)
} 
```

虽然省略了包声明和导入一节，但如果将以上内容作为 main.go 执行，则会输出以下内容。

```
$ go run main.go -h
Usage:
  main [OPTIONS]

Application Options:
  -n, --name= listen address

Help Options:
  -h, --help Show this help message

$ go run main.go -n Foo
Hello, Foo 
```

## 字段的设定

`jessevdk/go-flags`在包中，为了定义选项而制作结构体。

结构的每个字段对应于一个选项。

因此，细致的设定将通过标签进行。

以下，介绍这个软件包中可以使用的主要标签。

*   `short`
    *   确定短选项的名称。 因为是短选项，所以在找一个字。
*   `long`
    *   指定长选项。
*   `description`
    *   指定在帮助中显示的可选说明文本。
*   `no-flag`
    *   指定不将字段作为选项处理时。 值为非空值。
*   `env`
    *   指定要读取的环境变量。 从环境变量中读取的值是可选的规定值。 (选项中指定的优先)
*   `env-delim`
    *   从环境变量中读取值到 slice 或 map 选项时，将使用此标记中指定的值进行拆分。
*   `default`
    *   设定规定值。 对于 map 或 slice 值，也可以多次指定。
*   `default-mask`
    *   指定在“帮助”的“默认值”栏中显示的值。 没有指定的情况下会显示`default`的值，但是指定这个标签的话可以复盖显示。 如果指定`-`，则设定为不显示规定值。
*   `choice`
    *   可以定义选项的可能值。 定义多个选择时多次指定。
*   `required`
    *   如果需要选项，则指定。 值为非空值。 如果在运行时未提供给定的选项，则返回 ErrRequired。
*   `positional-args`
    *   可以通过为结构指定此标记来定义位置参数。

它还支持选项组、选项的名称空间和子命令的定义等。

* * *

1.  java 的选项等，并不是完全不看，但是