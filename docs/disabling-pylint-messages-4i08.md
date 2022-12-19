# 禁用 Pylint 消息

> 原文：<https://dev.to/pzelnip/disabling-pylint-messages-4i08>

*本帖原帖于 2018 年 8 月 12 日发至[https://www . codependentcodr . com/disabiling-pylint-messages . html # disabiling-pylint-messages](https://www.codependentcodr.com/disabling-pylint-messages.html#disabling-pylint-messages)T3】*

今天的小提示，因为我一直在找这个。如果您使用
[Pylint](https://pylint.org/) 对您的代码进行静态分析，您可能会发现您想要禁用特定行或文件的特定规则。也就是说，您不想永久禁用该规则，但只是针对该规则没有意义的一个特殊位置。

我发现这在单元测试代码中特别常见，因为我的测试方法名称往往很长，违反了规则 C0103“名称不符合命名规则”。例如，一个测试名称可能看起来像:

```
def test_message_builder_generates_correct_bytestring_when_no_argument_supplied(): 
```

Enter fullscreen mode Exit fullscreen mode

这是非常自我描述的，当测试失败时，它使找出可能出错的地方变得更加容易。问题是 Pylint 将标记该行，因为它的长度超过 30 个字符，违反了样式
准则。我们可以在整个代码库中禁用这个规则，但是在测试的
之外，这个规则是有意义的。

这就是本地禁用的用武之地，它采用注释的形式:

```
# pylint disable=C0103 def test_message_builder_generates_correct_bytestring_when_no_argument_supplied(): 
```

Enter fullscreen mode Exit fullscreen mode

这将抑制作用域(模块或块)剩余部分的代码 C0103，或者直到它被重新启用:

```
# pylint disable=C0103 def test_message_builder_generates_correct_bytestring_when_no_argument_supplied():

# still disabled here... 
# pylint enable=C0103 def but_not_disabled_here_so_this_name_will_get_flagged_by_pylint(): 
```

Enter fullscreen mode Exit fullscreen mode

您也可以(通常是更好的做法)对某个特定代码使用“详细名称”,而不是简写代码。比如这是
等价:

```
# pylint disable=invalid-name def test_message_builder_generates_correct_bytestring_when_no_argument_supplied(): 
```

Enter fullscreen mode Exit fullscreen mode

一个问题变成了“我如何知道代码的详细名称是什么？”答案是在命令行上使用`--list-msgs`参数给 Pylint，它会把它们都吐出来:

```
$ pylint --list-msgs
:blacklisted-name (C0102): *Black listed name "%s"*
  Used when the name is listed in the black list (unauthorized names).
:invalid-name (C0103): *%s name "%s" doesn't conform to %s*
  Used when the name doesn't conform to naming rules associated to its type
  (constant, variable, class...).
:missing-docstring (C0111): *Missing %s docstring*
  Used when a module, function, class or method has no docstring.Some special
  methods like __init__ doesn't necessary require a docstring.
:empty-docstring (C0112): *Empty %s docstring*
  Used when a module, function, class or method has an empty docstring (it would
  be too easy ;).

... more lines ... 
```

Enter fullscreen mode Exit fullscreen mode

关于 Pylint 的一个额外的提示:如果你使用 Visual Studio 代码，你可以打开 Pylint 作为你的 linter，警告将被放到 problems 视图中。要打开它，在你的 VS 代码设置中设置以下内容(假设你已经安装了微软的 [Python 扩展):](https://marketplace.visualstudio.com/items?itemName=ms-python.python) 

```
"python.linting.enabled": true,
"python.linting.pylintEnabled": true, 
```

Enter fullscreen mode Exit fullscreen mode

注意，默认情况下这两个选项都是打开的(至少在当前版本中是这样)。保存 Python 文件后，问题视图中将显示 Pylint 警告:

[![Pylint Warnings in VS Code Problems View](../Images/1e336e57dd695c6a13e1a5867ad2fa08.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NQRUyPA8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codependentcodr.com/static/imgs/pylint_warnings_in_vscode-crunch.png)

请注意，该屏幕截图还说明了如何通过在搜索框中输入“pylint”来过滤问题视图，只显示 Pylint 警告。还要注意，单击其中任何一个都会在 VS 代码中打开该文件，并导航到有问题的那一行。非常方便。