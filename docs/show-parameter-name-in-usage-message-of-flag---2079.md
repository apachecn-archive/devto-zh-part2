# 在标志的用法消息中显示参数名

> 原文：<https://dev.to/yyagi/show-parameter-name-in-usage-message-of-flag---2079>

标志包提供了关于命令行标志解析的功能。它当然包括一个用法信息。例如，它显示如下。

```
package main

import (
    "flag"
    "os"
)

func main() {
    var importFile string
    var config bool
    var number int

    flags := flag.NewFlagSet("mycmd", flag.ExitOnError)
    flags.StringVar(&importFile, "import", "", "import file.")
    flags.BoolVar(&config, "c", false, "edit config.")
    flags.IntVar(&number, "n", 0, "number of executions.")

    flags.Parse(os.Args[1:])
} 
```

```
$ ./mycmd --help
Usage of mycmd:
  -c    edit config.
  -import string
        import file.
  -n int
        number of executions. 
```

这是自动生成的。参数名使用标志的类型。但有时要自定义参数名。
例如，在上面的例子中，我想用`file`作为`-import`的参数，用`number`作为`-n`的参数(因为这样更自然；)).

这可以从`usage`自变量中指定`xxVar`方法。如果在`usage`中包含一个反引号名称，它将用作参数名称。

```
package main

import (
    "flag"
    "os"
)

func main() {
    var importFile string
    var config bool
    var number int

    flags := flag.NewFlagSet("mycmd", flag.ExitOnError)
    flags.StringVar(&importFile, "import", "", "Import `file`.")
    flags.BoolVar(&config, "c", false, "Edit config.")
    flags.IntVar(&number, "n", 0, "`Number` of executions.")

    flags.Parse(os.Args[1:])
} 
```

```
$ ./mycmd --help 
Usage of mycmd:
  -c    edit config.
  -import file
        import file.
  -n number
        number of executions. 
```

我知道这件事的时间不长。这在 [`PrintDefaults` func](https://golang.org/pkg/flag/#PrintDefaults) 的文档中有描述。

希望这能帮助到某个人。