# 最常见的编程案例类型

> 原文：<https://dev.to/chaseadamsio/most-common-programming-case-types-30h9>

当使用计算机时——特别是在编程时——你会不可避免地发现自己在给事物命名([计算机科学中两大难题之一](https://twitter.com/codinghorror/status/506010907021828096?lang=en))。

成功命名的一个主要因素是知道您想要使用的案例类型，这样您就可以对每个项目/工作空间有一个一致的约定。如果你正在写软件，你会在一个语言规范中遇到至少一个这样的例子。有些语言(尤其是 Go)很大程度上依赖于你对这两种语言之间的区别的了解以及对它们的正确使用。

## 你会学到什么

*   最常见的案例类型:

    *   骆驼箱
    *   蛇案
    *   烤肉串盒
    *   帕斯卡案例
    *   大写字母(带蛇形字母)
*   如何在以下情况下使用它们:

    *   为计算机命名文件(我推荐的最佳实践)
    *   用 Go 编写代码
    *   用 Ruby 编写代码
    *   用 JavaScript 编写代码
    *   用 Python 编写代码

# 茶包

`camelCase`必须 **(1)** 以小写字母开头 **(2)** 每一个新的后续单词的第一个字母大写，并与前一个单词复合。

变量`camel case var`的骆驼情况的一个例子是`camelCaseVar`。

# 蛇 _ 案

`snake_case`非常简单，用“_”替换所有空格，并使所有单词小写。这是可能的，但在我看来，这最终违背了初衷。

变量`snake case var`的一个蛇行例子是`snake_case_var`。

# 烤肉串-案例

`kebab-case`非常简单，用“-”替换所有空格，并将所有单词小写。有可能烤肉串案件和混合骆驼案件和帕斯卡案件，但最终挫败的目的。

变量`kebab case var`的烤肉串例子是`kebab-case-var`。

# 帕斯卡塞

`PascalCase` has 的每个单词都以大写字母开头(不像 camelCase 的第一个单词以小写字母开头)。

变量`pascal case var`的 pascal 情况的一个例子是`PascalCaseVar`。

**注意:这种情况很容易与 camel 案例混淆，但它完全是一种独立的案例类型。**

# 上层 _ 案例 _ 蛇案

将所有空格替换为下划线，并将所有字母转换为大写。

变量`upper case snake case var`的大写蛇形的一个例子是`UPPER_CASE_SNAKE_CASE_VAR`。

# 我应该使用哪种案例类型？

现在你已经知道了各种大小写类型，让我们来看一个我推荐的文件名最佳实践的例子，以及什么时候使用 Go、JavaScript、Python 和 Ruby 的大小写。

## 命名文件时应该用什么约定？

**推荐:永远的蛇案**

给文件命名时，问“最小公分母是什么？”很重要如果你不是固执己见，我发现我在 snake case 上取得了最大的成功，因为它最不可能在文件系统之间产生问题，并且保持文件名对“my_awesome_file”可读。

如果你是 Mac 用户或者和 Mac 用户一起工作，总是使用小写是一个好习惯。Mac 有一个 HFS+文件系统，因为 HFS+不区分大小写，它可以把“我的文件”或“我的文件”读成“我的文件”。

我对此的主要论点源于我在运行 CI/CD(持续集成/持续交付)集群时看到的一个特别阴险的“bug”。在 React 项目的生成过程中，CI 作业失败，出现“file not found: mycomponent.js”。开发人员发誓该文件在项目的源代码中，当我仔细查看时，我注意到他们有一个“MyComponent.js”的导入，但该文件被命名为“my component . js”(对于 React 项目，PascalCase 是命名组件文件的约定)。由于 HFS+处理文件大小写的方式，在开发人员(使用 Mac)编写代码时，它愉快地接受了“MyComponent.js”是“mycomponent.js ”,但在基于 Unix 的 CI 服务器构建它时，它会失败，因为它期望精确的大小写来找到文件。

## 围棋约定

Go 是最需要注意大小写类型约定的语言。该语言通过名称是以大写字母还是小写字母开头来决定变量、字段或方法是否应该对包调用者可用。

*   **Pascal case** 是*在 Go 中导出字段和方法所必需的*
*   **Camel case** 是 Go 中内部字段和方法的*必需的*

```
package casetypes

type ExportedStruct {
    unexportedField string
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，`ExportedStruct`可用于打包`casetypes`的调用者，而`unexportedField`仅可用于`ExportedStruct`上的方法。

## Javascript 约定

*   **骆驼案**为变量和方法。
*   **JavaScript 中类型和类的 Pascal case** 。
*   **大写蛇盒**为常量。

### 做出反应约定

我写了足够多的 React，它足够独特，值得在这里作为一个小节来调用约定:

*   **Pascal 大小写**用于 React 中的组件名和文件名。

## 红宝石约定

*   **Pascal case** 用于 Ruby 中的类和模块。
*   **蛇案**为符号、方法和变量。
*   **大写蛇盒**为常量。

## Python 约定

*   **Snake case** 为[方法名和实例变量](https://www.python.org/dev/peps/pep-0008/#method-names-and-instance-variables) (PEP8)。
*   **大写蛇盒**为常量。

## 其他约定

*   在 **Lisp** 中的烤肉串案例。
*   烤肉串案在 **HTTP 网址** ( `most-common-programming-case-types/`)。
*   在 **JSON** 属性键中的蛇案。

# 快速对照表

| 案例类型 | 例子 |
| --- | --- |
| 字符串形式的原始变量 | `some awesome var` |
| 骆驼箱 | `someAwesomeVar` |
| 蛇案 | `some_awesome_var` |
| 烤肉串盒 | `some-awesome-var` |
| 帕斯卡案例 | `SomeAwesomeVar` |
| 大写蛇盒 | `SOME_AWESOME_VAR` |

现在您已经了解了最常见的 case 类型，您已经准备好学习大多数流行的语言，并且知道在编写自己的代码时应该遵守哪些约定！