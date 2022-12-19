# 国际化(i18n)和本地化(l10n)的分步指南

> 原文：<https://dev.to/theodesp/a-step-by-step-guide-to-go-internationalization-i18n--localization-l10n-4ig2>

[![image](../Images/23ac12b06297403e0d52f792faea9220.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ADnJUKGI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hgy5ofyy75tfjz5o6s3u.png)

[**阅读原文**T3】](https://phrase.com/blog/posts/internationalization-i18n-go/)

Go 是一种静态编译语言，由于其简单、高性能并且非常适合开发云应用程序，最近受到了广泛的欢迎。它有一个强大的、但文档不完善的子包级基础库，处理许多与国际化(i18n)和本地化(l10n)相关的方面，比如字符编码、文本转换和特定于地区的文本处理。让我们看看我们能做些什么来掌握这个库，并使我们的 Go 应用程序具有区域意识。

我们所指的包是`golang.org/x/text`,如果正确使用，你可以在全球化应用时覆盖很多部分。它附带了一组抽象，使得处理可翻译的消息、格式、复数规则、Unicode 等等更加容易。

这篇文章将由两部分组成。第一部分概述了`golang.org/x/text`包及其在格式化和本地化方面提供的工具。Go 擅长构建基于微服务的架构，因此在第二部分中，为了不打破这一传统，我们将制作一个本地化服务器微服务，它将帮助我们了解 Go 中 i18n 和 l10n 支持的整体情况。

出于本教程的目的，我将使用最新的 **Go v1.10** ，本教程的代码托管在 [Github](https://github.com/PhraseApp-Blog/go-internationalization) 上。

我们走吧。

## 包的概述

Go 程序中的大多数消息要么通过`fmt`要么通过一个模板包。
`golang.org/x/text`由多层次的子程序包组成，这些子程序包提供了许多实用程序和函数，使用 fmt 风格的 API 来格式化本地化的字符串。让我们看看如何在实践中使用它。

### 消息和目录

**消息**是要传达给用户的某种形式的内容。每条消息由一个**键**标识，它可以有多种形式。你可以创建一个这样的消息打印机:

```
p := message.NewPrinter(language.BritishEnglish)

p.Printf("There are %v flowers in our garden.", 1500) 
```

Enter fullscreen mode Exit fullscreen mode

当您调用 NewPrinter 函数时，需要提供一个**语言标签**。语言[标签](https://godoc.org/golang.org/x/text/language#Tag)在你想要指定一种语言的时候使用。有许多方法可以创建标签，例如:

*   使用预定义的标签。例如:

```
language.Greek, language.BrazilianPortuguese 
```

Enter fullscreen mode Exit fullscreen mode

此处列出了预定义标签的完整列表[。](https://godoc.org/golang.org/x/text/language#Tag)

*   从字符串值。例如:

```
language.Make("el"), language.Parse("en-UK") 
```

Enter fullscreen mode Exit fullscreen mode

*   通过组成部分类型标签，基地，脚本，区域，变量，[]变量，扩展，[]扩展或错误。例如:

```
ja, _ := language.ParseBase("ja") 
jp, _ := language.ParseRegion("JP") 
jpLngTag, _ := language.Compose(ja, jp) 
fmt.Println(jpLngTag) // prints ja-JP 
```

Enter fullscreen mode Exit fullscreen mode

如果你指定了一个无效的语言标签，你将得到一个代表未定义标签的**和**标签的实例。

```
fmt.Println(language.Compose(language.ParseRegion("AL"))) // prints Und-AL 
```

Enter fullscreen mode Exit fullscreen mode

如果你想了解更多关于语言 API 的知识，请看这里的文档。

回到我们的消息，我们可以分配一个使用不同语言的新打印机，并打印格式化的字符串。该库将为您处理任何本地化的格式变体:

```
package main

import (
 "golang.org/x/text/message"
 "golang.org/x/text/language"
)

func main()  {
 p := message.NewPrinter(language.BritishEnglish)
 p.Printf("There are %v flowers in our garden.\n", 1500)

 p = message.NewPrinter(language.Greek)
 p.Printf("There are %v flowers in our garden.", 1500)
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你运行这个程序，你会得到:

```
$ go run main.go
There are 1,500 flowers in our garden.
There are 1.500 flowers in our garden. 
```

Enter fullscreen mode Exit fullscreen mode

现在为了打印翻译后的消息，我们需要将它们添加到消息目录中，这样**打印机**就可以为正确的语言标签找到它们。

一个**目录**定义了翻译后的格式字符串的集合。可以把它想象成一组每种语言的字典，其中包含一组关键字的翻译。为了使用目录，我们需要用翻译填充它们。

在实践中，翻译将从翻译人员提供的数据源中自动注入。让我们看看如何手动操作:

```
package main

import (
 "golang.org/x/text/message"
 "golang.org/x/text/language"
 "fmt"
)

func init()  {
 message.SetString(language.Greek, "%s went to %s.",  "%s πήγε στήν %s.")
 message.SetString(language.AmericanEnglish, "%s went to %s.",  "%s is in %s.")
 message.SetString(language.Greek, "%s has been stolen.",  "%s κλάπηκε.")
 message.SetString(language.AmericanEnglish, "%s has been stolen.",  "%s has been stolen.")
        message.SetString(language.Greek, "How are you?", "Πώς είστε?.")
}

func main()  {
 p := message.NewPrinter(language.Greek)
 p.Printf("%s went to %s.", "Ο Πέτρος", "Αγγλία")
 fmt.Println()
 p.Printf("%s has been stolen.", "Η πέτρα")
 fmt.Println()

 p = message.NewPrinter(language.AmericanEnglish)
 p.Printf("%s went to %s.", "Peter", "England")
 fmt.Println()
 p.Printf("%s has been stolen.", "The Gem")
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您运行这个程序，您将得到以下输出:

```
$ go run main.go
Ο Πέτρος πήγε στήν Αγγλία.
Η πέτρα κλάπηκε.
Peter is in England.
The Gem has been stolen.% 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:当你使用`SetString`方法时，你指定的键是区分大小写和行的，这意味着如果你试图使用`PrintLn`或者添加一个行尾字符`\n`，那么它将不起作用:

```
p := message.NewPrinter(language.Greek)

p.Printf("%s went to %s.\n", "Ο Πέτρος", "Αγγλία") // will print Ο Πέτρος went to Αγγλία.
p.Println("How are you?") // will print How are you? 
```

Enter fullscreen mode Exit fullscreen mode

通常你不创建目录，而是让图书馆为你处理它们。您还可以选择使用[目录以编程方式构建它们。构建器](https://godoc.org/golang.org/x/text/message/catalog#Builder)功能。

### 处理复数

对于需要根据多个值添加多个字符串翻译的情况，需要在翻译目录中添加特殊的调用来进行配置。子程序包`golang.org/x/text/feature/plural`公开了一个名为 [SelectF](https://godoc.org/golang.org/x/text/feature/plural#Selectf) 的函数，用于在一个文本中定义多种语言复数。

下面我给出了这个函数的一些典型用法:

```
func init() {
        message.Set(language.Greek, "You have %d. problem",
 plural.Selectf(1, "%d",
 "=1", "Έχεις ένα πρόβλημα",
 "=2", "Έχεις %[1]d πρόβληματα",
 "other", "Έχεις πολλά πρόβληματα",
 ))
        message.Set(language.Greek, "You have %d days remaining",
 plural.Selectf(1, "%d",
 "one", "Έχεις μία μέρα ελεύθερη",
 "other", "Έχεις %[1]d μέρες ελεύθερες",
 ))

}

func main()  {
 p := message.NewPrinter(language.Greek)
 p.Printf("You have %d. problem", 1)
 fmt.Println()
 p.Printf("You have %d. problem", 2)
 fmt.Println()
 p.Printf("You have %d. problem", 5)
 fmt.Println()
 p.Printf("You have %d days remaining", 1)
 fmt.Println()
 p.Printf("You have %d days remaining", 10)
 fmt.Println()
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您运行这个程序，您将得到以下输出:

```
$ go run main.go
Έχεις ένα πρόβλημα
Έχεις 2 πρόβληματα
Έχεις πολλά πρόβληματα
Έχεις μία μέρα ελεύθερη
Έχεις 10 μέρες ελεύθερες 
```

Enter fullscreen mode Exit fullscreen mode

该功能提供的案例可以支持`zero`、`one`、`two`、`few`、`many`等多种变化，也可以匹配`>x`或`<x`等比较。

### 消息中的字符串插值

在其他一些情况下，如果您想要处理消息的更多可能的变体，您可以分配占位符变量来处理语言特征的一些特定情况。例如，在前面的例子中，我们使用的复数可以写成:

```
func init() {
        message.Set(language.Greek, "You are %d minute(s) late.",
 catalog.Var("minutes", plural.Selectf(1, "%d", "one", "λεπτό", "other", "λεπτά")),
 catalog.String("Αργήσατε %[1]d ${minutes}."))

}

func main()  {
 p := message.NewPrinter(language.Greek)
 p.Printf("You are %d minute(s) late.", 1) // prints Αργήσατε 1 λεπτό
 fmt.Println()
 p.Printf("You are %d minute(s) late.", 10)// prints Αργήσατε 10 λεπτά
 fmt.Println()
} 
```

Enter fullscreen mode Exit fullscreen mode

`catalog.Var`为第一个字符串参数`minutes`分配了一个特殊的标签，因此可以根据`%d`参数的值用一个更相关的翻译来替换它。

### 格式化货币

包`golang.org/x/text/currency`处理货币格式规则。
对于货币，有一些有用的函数可以打印关于金额的特定于地区的字符串。例如，这里有一些你可以格式化它们的方法:

```
package main

import (
 "golang.org/x/text/message"
 "golang.org/x/text/language"
 "fmt"
 "golang.org/x/text/currency"
)

func main()  {
        p := message.NewPrinter(language.English)
        p.Printf("%d", currency.Symbol(currency.USD.Amount(0.1)))
 fmt.Println()
 p.Printf("%d", currency.NarrowSymbol(currency.JPY.Amount(1.6)))
 fmt.Println()
 p.Printf("%d", currency.ISO.Kind(currency.Cash)(currency.EUR.Amount(12.255)))
 fmt.Println() 
```

Enter fullscreen mode Exit fullscreen mode

结果将是:

```
$ go run main.go  
US$ 0.10
¥ 2
EUR 12.26 
```

Enter fullscreen mode Exit fullscreen mode

### 加载消息

当您处理翻译时，通常需要先加载翻译，以便应用程序可以使用它们。您可以将这些文件视为静态资源。关于如何使用应用程序部署这些文件，您有几个选项:

#### 手动设置翻译字符串

组织翻译的最简单方法是将它们分配到应用程序二进制文件中。您必须手动创建一个条目数组，用于在 init 上将消息加载到默认目录中。然后在您的应用程序中，您只需要使用`NewPrinter`函数来切换语言环境。

下面是一个在 init:
上加载翻译的示例应用程序

```
package main

import (
 "golang.org/x/text/language"
 "golang.org/x/text/feature/plural"
 "golang.org/x/text/message"
 "golang.org/x/text/message/catalog"
)

type entry struct {
 tag, key string
 msg      interface{}
}

var entries = [...]entry{
 {"en", "Hello World", "Hello World"},
 {"el", "Hello World", "Για Σου Κόσμε"},
 {"en", "%d task(s) remaining!", plural.Selectf(1, "%d",
 "=1", "One task remaining!",
 "=2", "Two tasks remaining!",
 "other", "[1]d tasks remaining!",
 )},
 {"el", "%d task(s) remaining!", plural.Selectf(1, "%d",
 "=1", "Μία εργασία έμεινε!",
 "=2", "Μια-δυο εργασίες έμειναν!",
 "other", "[1]d εργασίες έμειναν!",
 )},
}

func init()  {
 for _, e := range entries {
     tag := language.MustParse(e.tag)
     switch msg := e.msg.(type) {
     case string:
         message.SetString(tag, e.key, msg)
     case catalog.Message:
         message.Set(tag, e.key, msg)
     case []catalog.Message:
         message.Set(tag, e.key, msg...)
     }
 }
}

func main()  {
 p := message.NewPrinter(language.Greek)

 p.Printf("Hello World")
 p.Println()
 p.Printf("%d task(s) remaining!", 2)
 p.Println()

 p = message.NewPrinter(language.English)
 p.Printf("Hello World")
 p.Println()
 p.Printf("%d task(s) remaining!", 2)

} 
```

Enter fullscreen mode Exit fullscreen mode

如果你运行这个程序，它将打印:

```
$ go run examples/static/main.go         
Για Σου Κόσμε
Μια-δυο εργασίες έμειναν!
Hello World
Two tasks remaining!% 
```

Enter fullscreen mode Exit fullscreen mode

在实践中，虽然这种方式实现起来很简单，但是可扩展性不够。它只适用于很少翻译的小应用程序。你将不得不手动设置翻译字符串，这很难自动化。出于所有其他原因，建议自动加载消息，我会详细解释下一步如何做。

#### 自动加载消息

传统上，大多数本地化框架将数据分组到每种语言的动态加载文件中。您可以将这些文件分发给翻译人员，并在他们准备好时将它们合并到您的应用程序中。

为了帮助这个过程，作者已经包含了一个叫做`gotext`的 helper CLI 工具，用于管理 Go 源代码中的文本。

让我们首先确保你有最新的版本:

```
$ go get -u golang.org/x/text/cmd/gotext 
```

Enter fullscreen mode Exit fullscreen mode

运行该工具将仅显示可用选项，而`help`开关不会显示任何其他信息:

```
$ gotext                        
gotext is a tool for managing text in Go source code.

Usage:

        gotext command [arguments]

The commands are:

        update      merge translations and generate catalog
        extract     extracts strings to be translated from code
        rewrite     rewrites fmt functions to use a message Printer
        generate    generates code to insert translated messages 
```

Enter fullscreen mode Exit fullscreen mode

出于本教程的目的，让我们使用 **update** 标志，它执行一个多步骤的过程，将翻译密钥提取到一个文件中，并更新代码，以便将它们加载到目录中，以便于使用。

创建一个文件`main.go`，添加几个`PrintF`调用，并确保包含对`go:generate`命令
的注释

```
$ touch main.go 
```

Enter fullscreen mode Exit fullscreen mode

*   **文件** : main.go

```
package main

//go:generate gotext -srclang=en update -out=catalog/catalog.go -lang=en,el

import (
 "golang.org/x/text/language"
 "golang.org/x/text/message"
)

func main() {
 p := message.NewPrinter(language.Greek)
 p.Printf("Hello world!")
 p.Println()

 p.Printf("Hello", "world!")
 p.Println()

 person := "Alex"
 place := "Utah"

 p.Printf("Hello ", person, " in ", place, "!")
 p.Println()

 // Greet everyone.
 p.Printf("Hello world!")
 p.Println()

 city := "Munich"
 p.Printf("Hello %s!", city)
 p.Println()

 // Person visiting a place.
 p.Printf("%s is visiting %s!",
 person,
 place)
 p.Println()

 // Double arguments.
 miles := 1.2345
 p.Printf("%.2[1]f miles traveled (%[1]f)", miles)
} 
```

Enter fullscreen mode Exit fullscreen mode

运行以下命令:

```
$ mkdir catalog
$ go generate 
```

Enter fullscreen mode Exit fullscreen mode

然后修改导入以包含 **catalog.go** 文件:

* *文件* : main.go

```
package main

//go:generate gotext -srclang=en update -out=catalog/catalog.go -lang=en,el

import (
 "golang.org/x/text/language"
 "golang.org/x/text/message"
      _ "golang.org/x/text/message/catalog"
)

... 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你看到项目结构，有一些文件被创建:

```
$ tree .
.
├── catalog
│   └── catalog.go
├── locales
│   ├── el
│   │   └── out.gotext.json
│   └── en
│       └── out.gotext.json
├── main.go 
```

Enter fullscreen mode Exit fullscreen mode

locales 文件夹包含库支持格式的翻译邮件。通常，您需要为此提供翻译。创建一个名为`messages.gotext.json`的新文件，并提供希腊语的翻译。

```
$ touch locales/el/messages.gotext.json 
```

Enter fullscreen mode Exit fullscreen mode

**文件**:locales/El/messages . gotext . JSON

```
{
  "language": "el",
  "messages": [
    {
      "id": "Hello world!",
      "message": "Hello world!",
      "translation": "Γιά σου Κόσμε!"
    },
    {
      "id": "Hello",
      "message": "Hello",
      "translation": "Γιά σας %[1]v",
      "placeholders": [
        {
          "id": "World",
          "string": "%[1]v",
          "type": "string",
          "underlyingType": "string",
          "argNum": 1,
          "expr": "\"world!\""
        }
      ]
    },
    {
      "id": "Hello {City}!",
      "message": "Hello {City}!",
      "translation": "Γιά σου %[1]s",
      "placeholders": [
        {
          "id": "City",
          "string": "%[1]s",
          "type": "string",
          "underlyingType": "string",
          "argNum": 1,
          "expr": "city"
        }
      ]
    },
    {
      "id": "{Person} is visiting {Place}!",
      "message": "{Person} is visiting {Place}!",
      "translation": "Ο %[1]s επισκέπτεται την %[2]s",
      "placeholders": [
        {
          "id": "Person",
          "string": "%[1]s",
          "type": "string",
          "underlyingType": "string",
          "argNum": 1,
          "expr": "person"
        },
        {
          "id": "Place",
          "string": "%[2]s",
          "type": "string",
          "underlyingType": "string",
          "argNum": 2,
          "expr": "place"
        }
      ]
    },
    {
      "id": "{Miles} miles traveled ({Miles_1})",
      "message": "{Miles} miles traveled ({Miles_1})",
      "translation": "%.2[1]f μίλια ταξίδεψε %[1]f",
      "placeholders": [
        {
          "id": "Miles",
          "string": "%.2[1]f",
          "type": "float64",
          "underlyingType": "float64",
          "argNum": 1,
          "expr": "miles"
        },
        {
          "id": "Miles_1",
          "string": "%[1]f",
          "type": "float64",
          "underlyingType": "float64",
          "argNum": 1,
          "expr": "miles"
        }
      ]
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

现在运行`go generate`命令和下一步的程序，看到翻译正在进行:

```
$ go generate
$ go run main.go
Γιά σου Κόσμε!
Γιά σας world!

Γιά σου Κόσμε!
Γιά σου Munich
Ο Alex επισκέπτεται την Utah
1,23 μίλια ταξίδεψε 1,234500% 
```

Enter fullscreen mode Exit fullscreen mode

如果您感兴趣的话，`rewrite`标志在源代码中搜索对`fmt`的引用，并用`p.Print`函数替换它们。例如，假设我们有以下程序:

**文件** : main.go

```
func main() {
   p := message.NewPrinter(language.German)
   fmt.Println("Hello world")
   fmt.Printf("Hello world!")
   p.Printf("Hello world!\n")
} 
```

Enter fullscreen mode Exit fullscreen mode

如果运行以下命令:

```
$ gotext rewrite -out main.go 
```

Enter fullscreen mode Exit fullscreen mode

然后 main.go 会变成:

```
func main() {
   p := message.NewPrinter(language.German)
   p.Printf("Hello world\n")
   p.Printf("Hello world!")
   p.Printf("Hello world!\n")
} 
```

Enter fullscreen mode Exit fullscreen mode

## 示例微服务

这是文章的第二部分，我们可以在实践中利用我们所学的关于`golang/x/text`包的知识。我们将构建一个简单的 HTTP 服务器，它将为接受用户语言参数的端点提供服务。然后，它会尝试将该参数与支持的语言列表进行匹配，然后根据最合适的语言环境提供翻译后的响应。

首先，确保您已经安装了所有的依赖项。

首先创建一个应用程序框架:

```
$ go get -u github.com/golang/dep/cmd/dep
$ dep init
$ touch main.go 
```

Enter fullscreen mode Exit fullscreen mode

**文件** : main.go

```
package main

import (
 "html"
 "log"
 "net/http"
        "fmt"
 "flag"
 "time"
)

const (
 httpPort  = "8090"
)

func PrintMessage(w http.ResponseWriter, r *http.Request) {
 fmt.Fprintf(w, "Hello, %s", html.EscapeString(r.Host))
}

func main() {
 var port string
 flag.StringVar(&port, "port", httpPort, "http port")
 flag.Parse()

 server := &http.Server{
 Addr:           ":" + port,
 ReadTimeout:    10 * time.Second,
 WriteTimeout:   10 * time.Second,
 MaxHeaderBytes: 1 << 16,
 Handler:        http.HandlerFunc(PrintMessage)}

 log.Fatal(server.ListenAndServe())
} 
```

Enter fullscreen mode Exit fullscreen mode

这个示例 HTTP 服务器还不处理翻译。我们可以通过将对`fmt.FprintF`的调用替换为对`p.FprintF`
的调用来做到这一点

```
func PrintMessage(w http.ResponseWriter, r *http.Request) {
   p := message.NewPrinter(language.English)
   p.Fprintf(w,"Hello, %v", html.EscapeString(r.Host)) 
```

Enter fullscreen mode Exit fullscreen mode

将下面一行添加到您的源代码中，并运行 go generate 命令:

```
//go:generate gotext -srclang=en update -out=catalog/catalog.go -lang=en,el 
```

Enter fullscreen mode Exit fullscreen mode

```
$ dep ensure -update
$ go generate        
el: Missing entry for "Hello, {Host}". 
```

Enter fullscreen mode Exit fullscreen mode

为缺少的条目提供翻译:

```
$ cp locales/el/out.gotext.json locales/el/messages.gotext.json 
```

Enter fullscreen mode Exit fullscreen mode

**文件**:locales/El/messages . gotext . JSON

```
{
    "language": "el",
    "messages": [
        {
            "id": "Hello, {Host}",
            "message": "Hello, {Host}",
            "translation": "Γιά σου %[1]v",
            "placeholders": [
                {
                    "id": "Host",
                    "string": "%[1]v",
                    "type": "string",
                    "underlyingType": "string",
                    "argNum": 1,
                    "expr": "html.EscapeString(r.Host)"
                }
            ]
        }
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

再次运行命令 go generate，并在`main.go` :
中添加对目录包的引用

```
$ go generate 
```

Enter fullscreen mode Exit fullscreen mode

**文件** : main.go

```
package main

import (
 "html"
 "log"
 "net/http"
 "flag"
 "time"
 "golang.org/x/text/message"
 "golang.org/x/text/language"

 _ "go-internationalization/catalog"
)
... 
```

Enter fullscreen mode Exit fullscreen mode

现在，为了确定当用户从 API 请求资源时我们需要切换哪种语言，我们需要添加一个[匹配器](https://godoc.org/golang.org/x/text/language#Matcher)对象，当提供了一个语言标签列表时，该对象将用于确定我们支持的地区的最佳匹配。
通过从`gotext`工具填充的`message.DefaultCatalog`中提供支持的地区列表，创建一个新的**匹配器**:

**文件** : main.go

```
var matcher = language.NewMatcher(message.DefaultCatalog.Languages()) 
```

Enter fullscreen mode Exit fullscreen mode

添加您的函数以根据请求参数匹配正确的语言:

**文件** : main.go

```
package main

import (
 "html"
 "log"
 "net/http"
 "flag"
 "time"
        "context"
 "golang.org/x/text/message"
 "golang.org/x/text/language"

 _ "go-internationalization/catalog"

)

//go:generate gotext -srclang=en update -out=catalog/catalog.go -lang=en,el

var matcher = language.NewMatcher(message.DefaultCatalog.Languages())

type contextKey int

const (
 httpPort  = "8090"
 messagePrinterKey contextKey = 1
)

func withMessagePrinter(next http.HandlerFunc) http.HandlerFunc {
 return func(w http.ResponseWriter, r *http.Request) {
     lang, ok := r.URL.Query()["lang"]

     if !ok || len(lang) < 1 {
         lang = append(lang, language.English.String())
     }
     tag,_, _ := matcher.Match(language.MustParse(lang[0]))
     p := message.NewPrinter(tag)
     ctx := context.WithValue(context.Background(), messagePrinterKey, p)

     next.ServeHTTP(w, r.WithContext(ctx))
 }
}
... 
```

Enter fullscreen mode Exit fullscreen mode

我只提供了一个从查询字符串解析的参数。您还可以混合和匹配从 cookie 或 Accept-Language 头解析的附加标记。

现在，您只需要用`withMessagePrinter`包装您的处理函数`PrintMessage`，并从上下文中获取打印机:

**文件** : main.go

```
...
func PrintMessage(w http.ResponseWriter, r *http.Request) {
 p := r.Context().Value(messagePrinterKey).(*message.Printer)
 p.Fprintf(w,"Hello, %v", html.EscapeString(r.Host))
}

func main() {
 var port string
 flag.StringVar(&port, "port", httpPort, "http port")
 flag.Parse()

 server := &http.Server{
 Addr:           ":" + port,
 ReadTimeout:    10 * time.Second,
 WriteTimeout:   10 * time.Second,
 MaxHeaderBytes: 1 << 16,
 Handler:        http.HandlerFunc(withMessagePrinter(PrintMessage))}

 log.Fatal(server.ListenAndServe())
} 
```

Enter fullscreen mode Exit fullscreen mode

启动服务器并发出一些请求来查看翻译的情况:

```
$ go run main.go 
```

Enter fullscreen mode Exit fullscreen mode

[![image](../Images/a724e4712b6853d0769f96514e04d40a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X5kuw2bx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j5lbcu7oezgpnyye1431.png)
[![image](../Images/f0bbeec3b3db6225b3d687e366fdb9f4.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--oAIm6ax4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/reauiczgico9wugi9dv9.png)

从现在起，世界是你的了…

## 使用短语

Phrase 支持许多不同的语言和框架，包括 Go。它允许轻松地导入和导出翻译数据，并搜索任何丢失的翻译，这真的很方便。最重要的是，你可以与翻译合作，因为让[专业人员为你的网站做本地化](https://phrase.com/blog/posts/translation-management-why-authentic-human-translation-is-still-essential-for-localizing-software/)会好得多。如果你想了解更多关于短语的知识，请参考[入门指南](https://phrase.com/docs/guides/setup/getting-started/)。你还可以获得 [14 天试用期](https://phrase.com/signup)。你还在等什么？

## 结论

在本文中，我们探索了 Go 如何使用`golang/x/text package`管理本地化，并实现了一个示例 web 服务器，该服务器提供翻译并解释所有部分如何组合在一起。由于官方文档缺乏实际例子，我希望这篇文章以简单的方式解释了将 i18n 添加到您的 Go 应用程序中的原则，并可以作为未来的参考。

这绝不是详尽的指南，因为每个应用程序都有不同的需求和范围要求。请继续关注关于这个主题的更详细的文章。