# 用代码编写你的编码标准。编辑器配置

> 原文：<https://dev.to/davidwengier/codify-your-coding-standards-with-editorconfig-1knd>

每个开发团队都有编码标准。有时它们是通过惯例、传统、例子建立起来的，有时甚至有一个正式的文件来概述它们(希望是可以更新的活格式！).无论如何做，没有人想成为代码审查或拉请求中的坏人，因为通常是轻微的违规而拉人上来，然而同时也没有人想看到代码库被忽视，让不一致性蔓延，或者可读性减弱。

Visual Studio 有许多优秀的规则和格式选项，使其能够完全配置以匹配您的编码标准和约定，但在团队环境中，保持一切同步可能是一件痛苦的事情。“团队设置文件”选项在大多数情况下有效，但它并不完美，仍然需要每个人配置 Visual Studio，以便在加入团队或重新安装计算机时使用该共享文件。

幸运的是，有一种方法可以在工具级别强制执行一些编码标准，而没有这些问题，特别是在 Visual Studio 2017 中，它现在支持. editorconfig 文件中的配置，该文件覆盖了单个开发人员的设置，并告诉 IDE 如何基于每个存储库进行操作。的。editorconfig 文件被简单地提交到存储库的根，从那时起，它就规定了诸如缩进、格式、样式和命名规则等内容。不是所有的 ide 都支持所有相同的特性，但是官方网站上的列表肯定令人印象深刻。

在这篇文章中，我将谈论如何编纂一些具体的。Visual Studio 相关规则。对于更详细的信息，[官方文档](https://docs.microsoft.com/en-us/visualstudio/ide/create-portable-custom-editor-options)很棒，虽然我可能会有偏见，因为我在那里提交了我的第一份 PR 到 docs 项目。

## 命名规则

命名规则允许你围绕字段、属性、常量等的命名和大小写制定标准。在您的代码库中。每个命名规则都需要一个名称，用下划线、严重性和要应用的样式以小写形式指定。例如:

```
dotnet_naming_rule.public_members_must_be_pascal.severity = error
dotnet_naming_rule.public_members_must_be_pascal.symbols = public_symbols
dotnet_naming_rule.public_members_must_be_pascal.style = pascal_style 
```

在这个例子中，`dotnet_name_rule`表示我们正在定义一个规则的一部分，`public_members_must_be_pascal`是我们规则的名称，我们将把它应用到与我们稍后将定义的`public_symbols`命名符号相匹配的符号上。我们希望在任何时候都执行这条规则，所以`severity`是`error`，这意味着 Visual Studio 将像处理编译器错误一样处理违规。最后，我们说过符合这个规则的东西应该使用在`pascal_style`中定义的样式，这是我们给我们的样式起的名字。

## 命名风格

命名样式定义了开发人员应该如何格式化与任何应用的规则相匹配的符号。像命名规则一样，它们有一个名称，然后它们可以指定前缀、后缀、单词分隔符和大写规则。在这种情况下，我们只需要像这样定义`pascal_case`的大写规则:

```
dotnet_naming_style.pascal_style.capitalization = pascal_case 
```

同样，`dotnet_naming_style`意味着我们正在定义一个样式，而`pascal_style`是我们在规则中使用的样式的名称。

## 命名符号

拼图的最后一块告诉 Visual Studio 该规则应该应用于哪些符号。对于我们的`public_symbols`,我们需要指定可访问性为 public，并且我们希望规则应用于属性、方法、字段、事件和委托。我们或许还可以在其中添加类、结构和枚举。

```
dotnet_naming_symbols.public_symbols.applicable_kinds = property,method,field,event,delegate
dotnet_naming_symbols.public_symbols.applicable_accessibilities = public 
```

命名符号也允许你指定`required_modifiers`,这样你就可以不同地定位静态、只读、异步或常量符号。

## 把所有的东西放在一起

这三个元素结合在一起，使得一个规则完全编纂，并意味着 Visual Studio 可以是一个坏人，当谈到执行编码标准。不需要再争论常量是对你大喊大叫还是更微妙了，你可以结束`_fields`和`m_fields`之间的古老战争。

此外，命名符号和样式可以由多个命名规则使用，因此您只需要定义一次类似于`pascal_style`的东西，就可以将 pascal 大小写约定应用于一些不同的东西。

但是要注意的是，如果你将它引入一个遗留代码库，你需要小心行事，并且可能只是在同一次提交中处理它引发的所有问题。即使您将严重性设置为`warning`或`suggestion`，至少您可能会用问题填充错误窗口，并且给任何人一个忽略错误窗口中的事情的理由都不是一个好主意。

那个。editorconfig 文件还可以用于指定缩进样式、大括号用法和样式、`var`用法，甚至是是否需要使用`this.`，或者系统使用语句应该去哪里。如果你能花时间去填写所有的可能性，那么在团队中就容易多了，因为你的代码库不受单个开发机器配置的影响，或者在开源项目中，确保贡献者总是与他们贡献的项目风格相匹配。

的完整示例。我目前用于个人项目的 editorconfig 文件可以在 DbUpgrader 项目[这里](https://github.com/davidwengier/dbupgrader/blob/master/.editorconfig)中找到。

### 明白了

到目前为止，我发现了一些关于设置编辑器配置文件的问题:

*   如果你指定常量应该是 pascal 大小写，那么当常量全是大写字母时，VS 不会出错，因为这仍然是有效的 pascal 大小写。
*   文件中规则的顺序似乎不一致，所以私有字段和私有常量的规则有时会重叠，VS 会认为你这样做是错误的。

如果我找到其他人，我会更新帖子。