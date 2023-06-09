# Omnipytent 1.3.0:异步任务和选择 ui

> 原文：<https://dev.to/idanarye/omnipytent-130-async-tasks-and-selection-uis-2e0o>

如果你不知道什么是 [Omnipytent](https://github.com/idanarye/vim-omnipytent) ,先看看这个:

[![idanarye image](img/456007709804c1392e9a743b81ebcafc.png)](/idanarye) [## Omnipytent 解释说

### idan Arye 12 月 19 日 17 时 15 分阅读

#vim #workflow #automation](/idanarye/omnipytent-5g5l)

[Omnipytent](https://github.com/idanarye/vim-omnipytent) 的 1.3.0 版本引入了一个新概念——异步任务。在这篇文章中，我将试着解释什么是异步任务，它们有什么好处。

# 问题:单线程、基于事件循环的 UI

(如果您已经理解我们为什么需要异步任务，您可以跳过这一部分)

Vim 有一个单线程架构。当你执行一个操作时(通过键入一个键，运行一个运行模式命令，使用`:autocmd`等。)该操作接管线程，其他任何东西都不能被更新。该操作可能接收来自用户的输入(例如使用`input()`或`getchar()`)或更新 TUI(例如使用`:echo`或通过使用`:!`运行 shell 命令),但 Vim 的事件循环本身会被阻塞，直到操作完成。您不能运行其他命令，这意味着没有任何东西可以更新 TUI，但是当操作正在运行时，即使是作业和终端也不能从它们的流中读取任何东西。

您的日常 Vim 操作非常快速，并且足够“原子化”,因此这不成问题。当你键入`w` twe 时，Vim 不能做任何其他事情，我们需要跳到下一个单词，但是这发生得如此之快以至于没有关系，并且你不需要 Vim 做任何其他事情。另一方面，如果您正在运行 shell 命令，它们可能需要很长时间，您必须耐心等待它们完成。幸运的是，Neovim 和 Vim8 都有作业和终端，所以你只需启动它，它就会在后台运行。

但是，如果您需要终端中运行的命令的“结果”呢？例如， [fzf](https://github.com/junegunn/fzf) 正在一个终端中运行`fzf`，并使用结果打开所选择的文件。这不能在单个 Vim 函数调用中完成——它需要在一个函数中启动终端，然后执行回事件循环，一旦`fzf`存在，就运行另一个函数来处理结果。如果在启动终端的同一个函数中完成，Vim 将无法更新终端缓冲区，用户也无法使用它。

不仅仅是 shell 命令——有时你只需要用户使用 Vim 的 UI 本身。一个很好的例子就是[逃犯.维姆](https://github.com/tpope/vim-fugitive)的`:Gcommit`命令。它为用户打开一个新的缓冲区来输入提交消息，当用户保存并关闭这个缓冲区时，插件用这个提交消息创建一个新的 Git 提交。在`:Gcommit`调用的函数结束之前，它占据了事件循环，用户不能写提交消息，也不能保存和关闭窗口，所以`:Gcommit`必须在此之前终止，当窗口关闭时，必须调用另一个函数来完成这个过程。

像逃亡者或与 fzf 捆绑在一起的插件正在注册回调来实现这种行为。做起来没那么难，但是相当繁琐(甚至比在 JS 里还多！)并且需要对 Vimscript 和 Vim 的架构有所了解。Omnipytent 任务应该是简单的和流线型的，所以这个回调注册太多了——我们需要更简单的。这就是异步任务的用武之地。

# 思想:基于异步任务的生成器

(如果您不关心它们是如何工作的，可以跳过这一节，直接进入下一节查看如何使用它们的示例)

Python 中的生成器函数是至少有一个`yield`表达式的函数。如果你想了解更多关于它的信息[，请阅读 Python 文档](https://docs.python.org/3/reference/expressions.html#yield-expressions)——为了我们的目的，提到`yield`产生一个值和执行本身就足够了——所以如果被调用者`yield`失败了，执行返回调用者，调用者可以在以后选择从它`yield`到达的地方恢复被调用者。

在 Python 3.5 中引入关键字`async`和`await`之前，这是用于异步 IO 的，由于 Omnipytent 希望支持旧版本，所以它使用`yield`来执行异步任务。

异步任务看起来像这样:

```
@task
def my_async_task(ctx):
    do_something()
    result = yield ASYNC_COMMAND()
    followup(result) 
```

当任务`yield` s 时，`ASYNC_COMMAND()`做三件事:

*   它准备命令(例如，打开一个窗口或启动一个终端)
*   它将自己注册在 Omnipytent 中。
*   它在 Vim 中注册一个回调/ `:autocmd`,以便在完成后恢复自身。

此后- `:OP my_async_task`终止，控制返回到 Vim 的事件循环。但是`my_async_task`本身还没有被终止——在某个时候它将被恢复，任务将继续执行并做后续工作。

当然，您可以在任务的后面`yield`使用另一个异步命令。

Omnipytent 附带了一些有用的异步命令。如果您需要创建自己的-请参考`:help omnipytent-creating-AsyncCommand`。

# `INPUT_BUFFER` -基本异步用户输入

例如，我将使用第一个 post - [Spring 的示例宠物诊所 web 应用程序](https://github.com/spring-projects/spring-petclinic)中的相同示例项目。假设我们想要一个添加动物主人的任务。API 很简单——一个带有细节的 POST 请求——但是我们如何提示用户(也就是我们)输入字段呢？

到目前为止，我们必须使用`input()`来允许用户输入数据。有了异步任务，我们可以做得更好:

```
import requests, yaml

OWNER_FIELDS = ('firstName', 'lastName', 'address', 'city', 'telephone')

@task
def add_owner(ctx):
    empty_form = '\n'.join('%s: ' % field for field in OWNER_FIELDS)
    filled_form = yield INPUT_BUFFER(text=empty_form, filetype='yaml')
    parsed_form = yaml.load('\n'.join(filled_form))
    requests.post('http://localhost:8080/owners/new', data=parsed_form) 
```

重要的一行是任务函数的第二行——我们执行`yield`的那一行。`INPUT_BUFFER`是与 Omnipytent 捆绑在一起的异步命令之一，它打开一个带有缓冲区的新窗口，供用户编辑。我们设置了原始文本——一个带有空白字段供用户填写的 YAMLish 表单——还将文件类型设置为 YAML 以获得一些漂亮的颜色。然后我们`yield`这个命令对象——执行控制返回到 Vim 的事件循环。现在用户可以填充表单了，当他们关闭表单时，Omnipytent 返回，任务继续执行——将`filled_form`设置为用户填充的缓冲区的行。

这是它实际运行的样子:

[![input buffer example](img/73e0b47cb78ad3deebb366c27960abca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_ftBunph--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v45lk1uespbz8g9dg1a2.gif)

# `CHOOSE` -选择 ui 的力量

异步任务背后的主要动机是支持选择 ui。这些通常是模糊匹配器，如 fzf 或 Unite，但我使用术语*Selection ui*,因为这种机制并不局限于允许模糊搜索的工具——任何提供从列表中选择的 TUI 的工具都适用。

测试可以接受参数，但是输入参数并不总是很方便。当参数很长、或很复杂、或很难记住时，以及当有一种简单的方法以编程方式生成可能性列表时，对于用户来说，使用选择 UI 过滤和挑选他们想要的东西要方便得多。

在我的下一个技巧中，我需要在示例项目中添加一个缺失的方法:

```
// src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java

    @GetMapping("/owners.json")
    public @ResponseBody List<Map<String, Object>> showResourcesVetList() {
        return this.owners.findByLastName("").stream().map(owner -> {
            Map<String, Object> entry = new HashMap<>();
            entry.put("id", owner.getId());
            entry.put("firstName", owner.getFirstName());
            entry.put("lastName", owner.getLastName());
            entry.put("address", owner.getAddress());
            entry.put("city", owner.getCity());
            entry.put("telephone", owner.getTelephone());
            return entry;
        }).collect(Collectors.toList());
    } 
```

它所做的只是添加一个新路径- `/owners.json` -生成所有所有者的 JSON。没什么特别的，但是 orig example 项目只支持以 HTML 页面的形式获取它们，这很难解析(除非使用正则表达式)

我们想编写一个 Omnipytent 任务，它用 GET 请求读取列表，让用户选择一个所有者，编辑它，并用 POST 请求更新它。为了进行选择，我们将使用另一个异步命令- `CHOOSE` :

```
import json

@task
def edit_owner(ctx):
    entries = json.loads(requests.get('http://localhost:8080/owners.json').text)
    entry = yield CHOOSE(
        entries,
        fmt='{firstName} {lastName}'.format_map,
        preview=lambda entry: yaml.dump(entry, default_flow_style=False))

    owner_id = entry.pop('id')
    orig_form = yaml.dump(entry, default_flow_style=False)
    edited_form = yield INPUT_BUFFER(text=orig_form, filetype='yaml')
    parsed_form = yaml.load('\n'.join(edited_form))

    edit_url = 'http://localhost:8080/owners/%s/edit' % owner_id
    requests.post(edit_url, data=parsed_form) 
```

`CHOOSE`运行 Vim 中安装的任何选择 UI。它首先检查 [fzf](https://github.com/junegunn/fzf) ，然后是 [Denite](https://github.com/Shougo/denite.nvim) ， [Unite](https://github.com/Shougo/unite.vim) ， [CtrlP](https://github.com/ctrlpvim/ctrlp.vim) ，最后——如果以上都不可用，它使用基于`inputlist()`的选择 UI。或者——如果你有其他偏好——你可以用`g:omnipytent_selectionUI`设置选择 UI。

除了选项列表，我们还向`CHOOSE`传递了两个参数:

*   `fmt`:选项为`dict` s，但选择 ui 从行中选择。该参数是一个将每个选项格式化为一行的函数。
*   `preview` : fzf，Denite 和 Unite 支持项目的预览，这个参数是一个用于渲染预览的函数。

在我们放弃`CHOOSE`之后，我们取回被选中的选项，并用它来显示一个`INPUT_BUFFER`——我之前已经解释过了。

让我们看看它是如何工作的:

[![selection UI example](img/155624bd2a94ec0388966d6f69448737.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_M4__EQf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0qgrv68jaryf4h7f8wvw.gif)

# 多选和生成器选项任务——一个实际的激励性例子

前面的例子很好地展示了异步任务的强大功能，但是它们并不适合您编写 Omnipytent 任务。如果您想要实际添加和编辑所有者，那么您将使用 web 应用程序，并且如果您需要一种快速的方法来运行它们，同时开发以测试您的代码，那么每次编辑文本缓冲区都有点麻烦。

那么，你实际上想要为之创建任务的东西呢？怎么样...运行测试？

运行测试是`CHOOSE`的一个很好的用例。当我处理一段代码时，我经常想运行一个测试来检查它。我不想运行**所有的**测试，因为这会花费太长时间，并且会产生太多的输出。我只想运行这个特定的测试，并查看它的输出——这将反映我对代码所做的更改。

测试名通常很长——它们需要编码包含测试的类/模块的名称，测试函数本身的名称，有时还需要参数化。每次写全名都很麻烦。将这个名字写在测试中使它更容易运行，但是现在当我们需要另一个测试时，我们必须编辑 tasks 文件——并确定那个测试的名字。没那么难，但现在可以做得更好——我们现在有`CHOOSE`！

好的项目管理工具通常有一个列出所有测试的命令，但是这是 Maven，所以我们必须自己解析文件:

```
import re

@task
def run_tests(ctx):
    pattern = re.compile(r'@Test\n\s*public void (\w+)\(')
    test_names = []
    for path in local.path('src/test').walk(lambda p: p.basename.endswith('.java')):
        for match in pattern.finditer(path.read('utf8')):
            test_names.append('#'.join((path.stem, match.group(1))))

    chosen_tests = yield CHOOSE(test_names, multi=True)

    cmd = local['mvn']['test']
    cmd = cmd['-Dtest=' + ','.join(chosen_tests), 'test']
    cmd & TERMINAL_PANEL 
```

请注意，`CHOOSE` : `multi=True`有一个新的参数。正如您可能已经从名称中猜到的那样，它允许用户选择多个选项。只有 fzf，Denite 和 Unite 支持这个，但是即使有 CtrlP 和`inputlist()`它仍然会返回一个列表来保持任务的一致性。

然后我们加入选定的测试，瞧！

[![multi selection example](img/8958aca226775d4b6bf9a59bca7aa7ea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MiQNo5Qi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wo28my7fd82ddtgp4fsw.gif)

但是...我们仍然需要选择每次想要运行的测试。用 fzf 选择它肯定比键入它好，但是由于我们通常希望在相同的代码区域工作时多次运行相同的测试，如果 Omnipytent 能够记住我们最后的选择就好了。

嗯-可以的。Omnipytent 已经有了能够记住用户选择的`@task.options`，但是你只能选择一个选项，而且选项键必须硬编码为局部变量。Omnipytent 1.3.0 解决了这两个问题:

1.  `@task.options`现在基于`CHOOSE`——所以它可以使用更精细的选择 ui。一个新的变种- `@task.options_multi` -允许你选择多个选项。如果你正在使用 CtrlP 或`inputlist()`并且仍然想要多个选择，你必须将它们作为参数传递。或者直接升级到 fzf/Denite/Unite。
2.  如果任务函数是一个生成器，它不使用局部变量作为选项，而是使用`yield` ed 值作为选项。

这意味着我们可以将我们的`run_tests`分成两个任务:`pick_tests`和`run_tests`。`pick_tests`会一直提示我们选择测试，但是`run_tests`会记住我们最后的选择:

```
@task.options_multi
def pick_tests(ctx):
    ctx.key(str)
    pattern = re.compile(r'@Test\n\s*public void (\w+)\(')
    for path in local.path('src/test').walk(lambda p: p.basename.endswith('.java')):
        for match in pattern.finditer(path.read('utf8')):
            yield '#'.join((path.stem, match.group(1)))

@task(pick_tests)
def run_tests(ctx):
    cmd = local['mvn']['test']
    cmd = cmd['-Dtest=' + ','.join(ctx.dep.pick_tests), 'test']
    cmd & TERMINAL_PANEL 
```

注意`pick_tests`的第一行:`ctx.key(str)`。因为`yield` ed 选项可以是对象，我们需要它们的一串键，`ctx.key`设置了挑选这些键的函数。该键必须是确定性的，因为这些键将用于缓存选择。还有`ctx.preview`用于设置预览功能，但我们在这里不需要。

它是这样工作的:

[![multi_options example](img/5d4dbd344916d5056bb0d942afdb6be7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DhmOmT41--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4mwtx6kzlaffrors1a22.gif)

# 结论

Omnipytent 的目标是允许简单项目任务的微自动化。异步任务允许您将更好的 UI 添加到自动化中，进一步增强触手可及的能力。