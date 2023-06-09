# 给我的小宝贝一个机会

> 原文：<https://dev.to/dmerejkowsky/giving-mypy-a-go-1be0>

*原载于[我的博客](https://dmerej.info/blog/post/giving-mypy-a-go/)T3。*

# 简介

这是对文章[我不需要类型](https://dmerej.info/blog/post/trying-mypy/)的跟进。

我留下了一个提示，解释我将使用 Python 项目给出具体的例子，所以开始吧。

正如你可能已经猜到的，我将讨论类型注释和静态类型检查器。

# mypy 如何工作

如果您还不熟悉 mypy，这里有一个 mypy 工作方式的快速概述。

让我们用一个相当做作的例子:

```
def is_odd(num):
    return num % 2 == 0

if is_odd("this sentence contains %s"):
    print("ok") 
```

Enter fullscreen mode Exit fullscreen mode

我们有一个函数`is_odd`，显然只对数字起作用，我们用一个字符串调用它。

但是由于字符串包含`%s`，Python 会很高兴地假设我们正在尝试格式化它，而这个错误将不会被注意到。`is_odd`将简单地返回 False，因为我们*也被*允许用 Python 中的`==`比较字符串和数字。

如果没有类型注释，mypy 什么也检测不到:

```
$ mypy foo.py
<nothing> 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们添加类型注释并重新运行 mypy，我们会得到一个错误消息:

```
def is_odd(num: int) -> bool:
    return num % 2 == 0

if is_odd("this sentence contains %s"):
    print("ok") 
```

Enter fullscreen mode Exit fullscreen mode

```
$ mypy foo.py
error: Argument 1 to "is_odd" has incompatible type "str"; expected "int" 
```

Enter fullscreen mode Exit fullscreen mode

因此，您可以在循环中使用 mypy:

*   从注释几个函数开始。
*   在源代码上运行 mypy。
*   如果它检测到一些错误，要么修复注释，要么修复代码。
*   回到第一步

这就是所谓的“渐进打字”。

# 设计一个实验

这个实验的目标是检验添加类型注释的麻烦是否值得。

## 一些定义

让我来定义一下我所说的“值得”是什么意思。

关于类型注释有很多说法，比如:

*   这使得外部贡献者更容易理解代码
*   它有助于重构
*   它有助于大型项目的维护

这些可能是真的，但很难衡量。

所以我问了自己一些别的问题:

*   第一:即使有其他的东西(linters，测试，评论…)，有没有只有类型注释才能发现的错误？
*   第二:让 mypy 无错运行所需的更改是否提高了代码的质量？

我知道第二个问题有点主观。我仍将使用这一指标，因为它对我来说非常重要。

## 选择项目

为了测试这个假设，我需要一个现有的项目。我使用了 [tsrc](https://github.com/SuperTanker/tsrc) ，这是我们在工作中[使用的命令行工具](https://tanker.io)来管理多个 git 库，并自动化 GitHub 和 GitLab 审查自动化<sup id="fnref1">T5 1</sup>。

我选择它是因为:

*   它不太大，所以实验不会花太长时间。
*   不算太小，这样我们就有足够的实验数据了。

## 协议

这是我使用的协议:

*   通过逐步输入循环，直到所有内容都有了类型注释。
*   记下每个重要的补丁。(就是说任何东西都是*而不是*只是添加注释)
*   当循环结束时，看一看每个补丁，并询问是否发现了 bug，以及它是否提高了代码的质量。

在继续之前，我应该告诉您我使用 mypy 时有三个重要选项:

*   `--strict`:这意味着 mypy 为每一个缺失类型注释的*发出一个错误。*
*   tsrc 依赖于不存在 stub 类型的库。
*   `--strict-optional`:如果你有一个字符串，你必须使用`Optional[str]`而不仅仅是`str`。我选择这个是因为:
    *   相对于没有错误是相当频繁的
    *   在未来的 mypy 版本中,`--strict-optional`将成为默认设置。

# 看着补丁

让我们先来看看琐碎的补丁:

## 真弦

```
def find_workspace_path() -> Path:
    head = os.getcwd()
     tail = True
     while tail:
         tsrc_path = os.path.join(head, ".tsrc")
         if os.path.isdir(tsrc_path):
             return Path(head)
         else:
            head, tail = os.path.split(head)
     raise tsrc.Error("Could not find current workspace") 
```

Enter fullscreen mode Exit fullscreen mode

该功能首先检查工作路径中是否有一个`.tsrc`隐藏目录。如果没有，它会遍历每个父目录并检查`.tsrc`目录是否在这里。如果它到达文件系统的根目录(T2 的第二个值是 None)，它会引发一个异常。

mypy 不喜欢`tail`以一个布尔值开始，然后被赋值给一个字符串。

我们可以通过使用一个非空字符串来解决这个问题，该字符串的值揭示了意图:

```
- tail = True + tail = "a truthy string"
     while tail: 
```

Enter fullscreen mode Exit fullscreen mode

另一种方法是使用类似于`Union[bool,str]`的类型，但我认为这样会更令人困惑。

无论如何，我不确定代码的质量在那里得到了提高。我的 py 没有得分。

## 保存配置

```
class Options:
    def __init__ (self, url, shallow=False) -> None:
        self.shallow: bool = shallow
        self.url: str = url

def save_config(options: Options):
    config = dict()
    config["url"] = options.url
    config["shallow"] = options.shallow

    with self.cfg_path.open("w") as fp:
        ruamel.yaml.dump(config, fp) 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`save_config`将一个“值对象”(`Options`类)序列化为一个 YAML 文件。

mypy 看到了前两行，`config = dict()`，`config["url"] = options.ul`，错误地推断出`config`是一个从字符串到字符串的字典。

然后它抱怨分配给一个布尔值`config["shallow"]`。

我们可以通过将`config`类型强制为从字符串到`Any`的字典来解决这个问题:`Any`是一个“神奇”的类型，我们可以精确地将[用于这种情况](http://mypy.readthedocs.io/en/latest/kinds_of_types.html#the-any-type)。

```
- config = dict() + config: Dict[str, Any] = dict() 
```

Enter fullscreen mode Exit fullscreen mode

这有点烦人，但是类型注释使得`config`更清楚了。mypy 得 1 分。

## 编码项目名称

当你使用 GitLab API 的时候，你经常要用到‘项目 id’。文档说如果是“url 编码”的，你可以使用`<namespace>/<project_name>`字符串，就像这样:

```
Get info about the Foo project in the FooFighters namespace:
GET /api/v4/projects/FooFighters%2Foo 
```

Enter fullscreen mode Exit fullscreen mode

在 python 中，天真的方法不起作用:

```
import urllib.parse

>>> urllib.parse.quote("FooFighters/Foo")
FooFighters/Foo 
```

Enter fullscreen mode Exit fullscreen mode

相反，你必须指定一个“安全”字符列表，也就是那些你不需要编码的字符。

文档说默认值是“/”。

因此，这里是我们在`tsrc.gitlab` :
中使用的

```
project_id = "%s/%s" % (namespace, project)
encoded_project_id = urllib.parse.quote(project_name, safe=list()) 
```

Enter fullscreen mode Exit fullscreen mode

mypy 发现默认值是一个字符串，所以他抱怨我们使用了一个列表。我们通过
修复了它

```
- encoded_project_name = urllib.parse.quote(project_name, safe=list()) + encoded_project_name = urllib.parse.quote(project_name, safe="") 
```

Enter fullscreen mode Exit fullscreen mode

在这里，mypy 强迫我们遵循一个隐含的约定。在 Python 中有两种方法来表示字符列表。一个实列表:`['a', 'b', 'c']`，或者一个字符串:`"abc"`。`urllib.quote()`函数的作者决定使用第二种形式，所以我们也遵循这个惯例是件好事。

mypy 的另一个胜利。

## 发现 bug

我们已经使用了很多工具来捕捉漏洞:

*   每个拉取请求都由其他人审查
*   为每个拉取请求运行两个静态分析器(pylint 和 pyflakes)
*   McCabe 复杂度是针对代码库的每一个函数和方法进行测量的，不允许超过 10。
*   TDD 被用于整个项目的开发
*   测试覆盖率已经达到了 80%

尽管如此，mypy 还是找到了测试的 bug，所有的 linters 和所有的评论者都没有找到。

以下是其中的几个。你可以试着自己找到错误，答案会在⁂符号后给出。

### 处理 _ 流 _ 错误

```
class GitLabAPIError(GitLabError):
    def __init__ (self, url: str, status_code: int, message: str) -> None:
        ...

def handle_stream_errors(response: requests.models.Response) -> None:
     if response.status_code >= 400:
        raise GitLabAPIError(
            response.url, "Incorrect status code:", response.status_code) 
```

Enter fullscreen mode Exit fullscreen mode

这段代码包装了对 GitLab API 的调用，并安排在我们发出“流”请求时引发特定的`GitLabAPIError`。(例如，下载一个 GitLab CI 工件):

<center>⁂</center>

这里的问题是我们颠倒了`status_code`和`message`参数。容易搞定:

```
- raise GitLabAPIError(
- response.url, "Incorrect status code:", response.status_code) + raise GitLabAPIError(
+ response.url, response.status_code, "Incorrect status code") 
```

Enter fullscreen mode Exit fullscreen mode

这个 bug 没有被发现，因为有问题的代码实际上是从 CI 脚本中复制/粘贴的(并且您通常不为 CI 脚本编写测试)。

在 tsrc 中，我们实际上不需要流响应，所以这实际上是死代码(现在它已经从代码库中消失了)

### 处理 _ json _ 错误

```
def handle_json_errors(response: requests.models.Response):

    try:
         json_details = response.json()
     except ValueError:
     json_details["error"] = (
         "Expecting json result, got %s" % response.text
     )

    ...

    url = response.url
    if 400 <= status_code < 500:
        for key in ["error", "message"]:
            if key in json_details:
                raise GitLabAPIError(url, status_code, json_details[key])
         raise GitLabAPIError(url, status_code, json_details)
    if status_code >= 500:
        raise GitLabAPIError(url, status_code, response.text) 
```

Enter fullscreen mode Exit fullscreen mode

这个稍微有趣一点。它位于前一个的附近，处理调用返回 JSON 对象的 GitLab API 的错误。

我们当然要捕捉 500 个错误，希望这不会经常发生。

如果状态代码在 400 和 499 之间，我们知道我们发出的请求有问题，但是我们需要告诉用户请求被拒绝的原因。

大多数情况下，GitLab API 会返回一个包含`error`或`message`键的 JSON 对象，但有时在返回的对象中两个键都找不到，有时响应的文本甚至不是有效的 JSON 代码。

所以我们必须检查 JSON 对象中的两个键，如果没有找到(如果我们退出 for 循环)，就将整个 JSON 响应存储在异常中。

<center>⁂</center>

bug 在第二个`raise GitLabAPIError`。我们正在传递一个完整的对象，而`GitLabAPIError`需要一个字符串。

修复方法是:

```
- raise GitLabAPIError(url, status_code, json_details) + raise GitLabAPIError(url, status_code, json.dumps(json_details, indent=2)) 
```

Enter fullscreen mode Exit fullscreen mode

同样，这很难通过测试来发现。GitLab 返回的 JSON 没有*而没有*包含`error`或`message`键的情况在项目的生命周期中只发生过一次(这解释了为什么要编写代码)，所以手动 QA 和单元测试不需要检查这个代码路径。

无论如何，注意我们没有盲目地编写类似于`str(json_details)`的东西来将 JSON 对象转换成字符串。我们发现它被用在显示给最终用户的消息中，因此我们使用`json.dumps(json_details), indent=2)`来确保消息包含整齐缩进的 JSON 并且易于阅读。

#### LocalManifest

```
class LocalManifest:
    def __init__ (self, workspace_path: Path) -> None:
        hidden_path = workspace_path.joinpath(".tsrc")
        self.clone_path = hidden_path.joinpath("manifest")
        self.cfg_path = hidden_path.joinpath("manifest.yml")
        self.manifest: Optional[tsrc.manifest.Manifest] = None

    def update(self) -> None:
        ui.info_2("Updating manifest")
        tsrc.git.run_git(self.clone_path, "fetch")
        tsrc.git.run_git(self.clone_path, "reset", "--hard", branch)

    def load(self) -> None:
        yml_path = self.clone_path.joinpath("manifest.yml")
        if not yml_path.exists():
            message = "No manifest found in {}. Did you run `tsrc init` ?"
            raise tsrc.Error(message.format(yml_path))
        self.manifest = tsrc.manifest.load(yml_path)

    @property
    def copyfiles(self) -> List[Tuple[str, str]]:
        return self.manifest.copyfiles

    def get_repos(self) -> List[tsrc.Repo]:
        assert self.manifest, "manifest is empty. Did you call load()?"
        return self.manifest.get_repos(groups=self.active_groups) 
```

Enter fullscreen mode Exit fullscreen mode

运行`tsrc init git@example.com/manifest.git`之后，清单被克隆到`<workspace>/.tsrc/manifest`中。

因此，`manifest.yml`的内容在`<workspace>/.tsrc/manifest/manifest.yml>`中。

`LocalManifest`类表示这个清单存储库。

以下是运行`tsrc sync`时发生的情况:

*   调用`local_manifest.update()`:通过运行`git fetch; git reset --hard origin/master`更新`<workspace>/.tsrc/manifest>`中的库
*   调用`local_manifest.load()`:解析`manifest.yml`文件，其内容存储在`self.manifest`属性中。
*   然后`Syncer`类调用`local_manifest.get_repos()`来找出要克隆或同步的存储库列表。
*   最后，`FileCopier`类使用`local_manifest.copyfiles`来执行文件复制。

<center>⁂</center>

这不是一个真正的错误，但是 mypy 强迫我们承认`LocalManifest.manifest`开始时没有，只有在`.load()`被调用后才会得到它的真实值。

在`get_repos()`中我们已经有了一个`assert`，但是 mypy 强迫我们在`copyfiles` getter:
中添加一个类似的检查

```
 @property
    def copyfiles(self) -> List[Tuple[str, str]]:
+       assert self.manifest, "manifest is empty. Did you call load()?"
        return self.manifest.copyfiles 
```

Enter fullscreen mode Exit fullscreen mode

### GitServer

tsrc 的一些测试我们称之为端到端测试:

它们是这样工作的:

*   我们为每个测试创建一个全新的临时目录
*   在其中，我们创建了一个包含裸 git 存储库的`srv`目录
*   然后，我们创建一个`work`目录，并从那里运行 tsrc 命令。

因此，我们不必模仿文件系统或 git 命令(这是可行的，但相当困难)，并且事情很容易调试，因为在出现问题的情况下，我们可以直接`cd`到测试目录并手动检查 git 库的状态。

无论如何，这些测试是用一个叫做`GitServer`的测试助手编写的。

您可以使用这个类来创建 git 存储库，在一些分支上推送文件，等等:

下面是助手的样子:

```
 def add_repo(self, name: str, default_branch="master") -> str:
        ...
        url = self.get_url(name)
        return url

    def push_file(self, name: str, file_path: str, *,
                  contents="", message="") -> None:
        ...
        full_path = ...
        full_path.parent.makedirs_p()
        full_path.touch()
        if contents:
            full_path.write_text(contents)
        commit_message = message or ("Create/Update %s" % file_path)
        run_git("add", file_path)
        run_git("commit", "--message", commit_message)
        run_git("push", "origin", "--set-upstream", branch)

    def tag(self, name: str, tag_name: str) -> None:
        run_git("tag", tag_name)
        run_git("push", "origin", tag_name)

    def get_tags(self, name: str) -> List[str]:
         src_path = self.get_path(name)
         rc, out = tsrc.git.run_git_captured(src_path, "tag", "--list")
         return out 
```

Enter fullscreen mode Exit fullscreen mode

可以这样用:

```
def test_tsrc_sync(tsrc_cli: CLI, git_server: GitServer) -> None:
    git_server.add_repo("foo/bar")
    git_server.add_repo("spam/eggs")
    manifest_url = git_server.manifest_url
    tsrc_cli.run("init", manifest_url)
    git_server.push_file("foo/bar", "bar.txt", contents="this is bar")

    tsrc_cli.run("sync")

    bar_txt_path = workspace_path.joinpath("foo", "bar", "bar.txt")
    assert bar_txt_path.text() == "this is bar" 
```

Enter fullscreen mode Exit fullscreen mode

<center>⁂</center>

bug 在`get_tags` :

```
 def get_tags(self):
         rc, out = tsrc.git.run_git(src_path, "tag", raises=False)
-        return out +        return out.splitlines() 
```

Enter fullscreen mode Exit fullscreen mode

`get_tags`方法也是死代码。它有一个有趣的故事。

当我开始为 tsrc 编写端到端测试并发现我需要一个测试框架来编写这些端到端测试时，就出现了对`GitServer`助手的需求。

至关重要的是,`GitServer`实现使用干净的代码。

*   第一，我们必须确定`GitServer`没有错误。否则，当测试失败时，我们可能会在产品代码中寻找错误。
*   同理，`GitServer`应该是容易改的。几乎可以肯定的是，tsrc 中会添加一些特性，这些特性需要修改`GitServer`代码来正确地测试它。

所以为了有一个干净的`GitServer`实现，我当然使用了我所知道的最好的技术:TDD。

[![yo dawg tests](img/515ff91d5e560a3f1df995609f6e2a16.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6vcQplEN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dmerej.info/blog/pics/yo-dawg-test.jpg)

您可以在 [test_test_helpers.py](https://github.com/SuperTanker/tsrc/blob/master/tsrc/test/test_test_helpers.py) 文件中找到它们。

无论如何，我正在为 tsrc 编写一个包含标签的端到端测试。

我想:“好吧，我需要一个`GitServer`中的`.tag()`方法。所以我还需要一个`get_tags()`方法来检查标签是否被真正地推送了”。因此我写了`get_tags`方法，忘记了先为`GitServer`写一个失败的测试(这在 TDD 5 年后仍然会发生，所以如果它也发生在你身上，不要担心。).在那一点上，我只有我的端到端测试失败了，所以我让它通过了，并且完全忘记了`get_tags()`方法。哦好吧。

### 执行者和任务

在 tsrc 的实现中，我们经常需要遍历一个项目列表，对每个项目执行操作并记录哪个失败了，当循环完成时，向用户显示错误消息。

例如:

```
tsrc sync
* (1/2) foo/bar
remote: Counting objects: 3, done.
...
Updating 62a5d28..39eb3bd
error: The following untracked files would be overwritten by merge:
    bar.txt
Please move or remove them before you merge.
Aborting
* (2/2) spam/eggs
Already up to date.
Error: Synchronize workspace failed
* foo/bar: updating branch failed 
```

Enter fullscreen mode Exit fullscreen mode

或者:

```
$tsrc foreach
:: Running `ls foo` on every repo
* (1/2) foo
$ ls foo
bar.txt
* (2/2) spam
$ ls foo
ls: cannot access 'foo': No such file or directory
Error: Running `ls foo` on every repo failed
* spam 
```

Enter fullscreen mode Exit fullscreen mode

所以为了保持事情顺利 <sup id="fnref2">[2](#fn2)</sup> ，我们有一些只处理循环和错误处理的高级代码:

首先，一个通用的`Task`方法。

(注意，您可以用 mypy 让您的[拥有泛型类型](https://mypy.readthedocs.io/en/latest/generics.html)。这是*牛逼*因为没有它你会被 C++程序员嘲笑)

```
class Task(Generic[T], metaclass=abc.ABCMeta):

    @abc.abstractmethod
    def description(self) -> str:
        pass

    @abc.abstractmethod
    def display_item(self, item: T) -> str:
        pass

    @abc.abstractmethod
    def process(self, item: T) -> None:
        pass 
```

Enter fullscreen mode Exit fullscreen mode

然后，一个普通的`SequentialExecutor`知道如何在一个项目列表上执行一个给定的任务并显示结果:

```
class SequentialExecutor(Generic[T]):
    def __init__ (self, task: Task[T]) -> None:
        self.task = task
        self.errors: List[Tuple[Any, tsrc.Error]] = list()

    def process(self, items: List[T]) -> None:
        if not items:
            return True
        ui.info_1(self.task.description())

        self.errors = list()
        for item in enumerate(items):
            ...
            self.process_one(item)

        if self.errors:
            self.handle_errors()

    def process_one(self, item: T) -> None:
        try:
            self.task.process(item)
        except tsrc.Error as error:
            self.errors.append((item, error))

    def handle_errors(self) -> None:
        # Display nice error message
        ...
        raise ExecutorFailed() 
```

Enter fullscreen mode Exit fullscreen mode

因此我们可以从`Task`继承来实现`tsrc sync` :

```
class Syncer(tsrc.executor.Task[Repo]):

    def process(self, repo: Repo) -> None:
        ui.info(repo.src)
        self.fetch(repo)
        self.check_branch(repo)
        self.sync_repo_to_branch(repo)

    def check_branch(self, repo: Repo):
        current_branch = tsrc.git.get_current_branch(repo_path)
        if not current_branch:
            raise tsrc.Error("Not on any branch") 
```

Enter fullscreen mode Exit fullscreen mode

`tsrc foreach`同上:

```
class CmdRunner(tsrc.executor.Task[Repo]):

    def process(self, repo: Repo) -> None:
        full_path = self.workspace.joinpath(repo.src)
        rc = subprocess.call(cmd, ...)
        if rc != 0:
            raise CommandFailed 
```

Enter fullscreen mode Exit fullscreen mode

你发现窃听器了吗？

<center>⁂</center>

bug 在这里:

```
 def process(self, items: List[T]) -> None:
         if not items:
             return True 
```

Enter fullscreen mode Exit fullscreen mode

这是糟糕的重构的结果。`Executor`用于通过查看`process()`方法的返回值来跟踪任务的成功。

过了一段时间，我发现使用异常更清楚，主要是在我实现`Syncer`类的时候。(你可以只引发一个异常，而不是添加很多`ifs`)。

但是早期的`return True`被留下了。在这里`mypy`发现了一些会让外部读者困惑的东西。在 tsrc 中，几乎所有处理执行器和任务的函数或方法要么不返回任何值，要么抛出异常。那个布尔人在这里做什么？

当然，已经没有检查`process`返回值的测试了(它们与其余代码同时被重构)，所以这个 bug 没有被注意到。

### [跑啊](#rungit)

mypy 要求的最后一项更改也非常有趣。是这样的。

在 tsrc 中，我们经常需要运行`git`命令，但是我们可以用两种非常不同的方式运行它们:

*   我们可以只运行命令，让它的输出直接显示给用户。这对于像`git fetch`这样需要花费一些时间并且输出包含用户进度指示的事情很有用。
*   或者我们只需要运行命令，捕获它的输出，并把它作为一个值来处理。例如，如果我们想获得“origin”遥控器的 url，我们可以调用`git remote get-url origin`。
*   在这两种情况下，我们都必须处理命令失败的可能性。

下面是实现的样子:

```
def run_git(working_path, *cmd, raises=True):
    """ Run git `cmd` in given `working_path`

    If `raises` is True and git return code is non zero, raise
    an exception. Otherwise, return a tuple (returncode, out)

    """
    git_cmd = list(cmd)
    git_cmd.insert(0, "git")
    options = dict()
    if not raises:
        options["stdout"] = subprocess.PIPE
        options["stderr"] = subprocess.STDOUT

    process = subprocess.Popen(git_cmd, cwd=working_path, **options)

    if raises:
        process.wait()
    else:
        out, _ = process.communicate()
        out = out.decode("utf-8")

    returncode = process.returncode
    if raises:
        if returncode != 0:
            raise GitCommandError(working_path, cmd)
    else:
        return returncode, out 
```

Enter fullscreen mode Exit fullscreen mode

下面是它的使用方法:

```
# run `git fetch`, leaving the output as-is run_git(foo_path, "fetch")

# run git remote get-url origin rc, out = run_git(foo_path, "remote", "get-url", "origin", raises=False):
if rc == 0:
    # Handle the case where there is no remote called 'origin'
    ...
else:
    # Do something with out 
```

Enter fullscreen mode Exit fullscreen mode

这里是我们使用`run_git`的另一个地方:我们有一个名为`tsrc status`的命令，它显示整个工作区的概要。这里它的输出是什么样子的:

```
tsrc status
* foo master ↓1 commit
* bar devel ↑1 commit 
```

Enter fullscreen mode Exit fullscreen mode

下面是实现:

```
class GitStatus:

    def update_remote_status(self):
        _, ahead_rev = run_git(
            self.working_path,
            "rev-list", "@{upstream}..HEAD",
            raises=False
        )
        self.ahead = len(ahead_rev.splitlines())

        _, behind_rev = run_git(
            self.working_path,
            "rev-list", "HEAD..@{upstream}",
            raises=False
        )
        self.behind = len(behind_rev.splitlines()) 
```

Enter fullscreen mode Exit fullscreen mode

找到窃听器了吗？

<center>⁂</center>

`update_remote_status()`中的代码假设`git rev-list`输出一个提交列表，然后统计输出中的行数。

如果*配置了*一个远程分支:
，那么这种方法就很有效

```
$ git revlist HEAD..@{upstream}
30f729a6a0ec3926cf063f5f8a3953b89d7b252e
ef564f0ef38a163beb3db52474ac4e256a6c2cd4 
```

Enter fullscreen mode Exit fullscreen mode

但是如果没有配置 remote，git 命令将会失败，并显示如下消息:

```
$ git revlist HEAD..@{upstream}
fatal: no upstream configured for branch 'dm/foo' 
```

Enter fullscreen mode Exit fullscreen mode

由于`update_remote_status()`不检查返回码，`self.ahead_rev`和`self.behind_rev`都被设置为`1`，输出看起来像:

```
* foo other-branch ↑1↓1 commit 
```

Enter fullscreen mode Exit fullscreen mode

哎呀。

<center>⁂</center>

但是不仅仅是这个 bug。

注意，`run_git`返回值的类型依赖于`raises`参数的值*。*

起初，我试图用一个`Union`类型来注释这个函数，就像这样:

```
def run_git(working_path: Path, *cmd: str, raises=True) ->
      Union[Tuple[int, str], None]:
    pass 
```

Enter fullscreen mode Exit fullscreen mode

但是后来 mypy 抱怨每一行使用了`run_git`和`raises=False` :

```
rc, out = run_git(..., raises=False)

foo.py:39: error: 'builtins.None' object is not iterable 
```

Enter fullscreen mode Exit fullscreen mode

我想了想，发现把`run_git`分成`run_git`和`run_git_captured` :
会更干净

```
def run_git(working_path: Path, *cmd: str) -> None:
    """ Run git `cmd` in given `working_path`

    Raise GitCommandError if return code is non-zero.
    """
    git_cmd = list(cmd)
    git_cmd.insert(0, "git")

    returncode = subprocess.call(git_cmd, cwd=working_path)
    if returncode != 0:
        raise GitCommandError(working_path, cmd)

def run_git_captured(working_path: Path, *cmd: str,
                     check: bool = True) -> Tuple[int, str]:
    """ Run git `cmd` in given `working_path`, capturing the output

    Return a tuple (returncode, output).

    Raise GitCommandError if return code is non-zero and check is True
    """
    git_cmd = list(cmd)
    git_cmd.insert(0, "git")
    options: Dict[str, Any] = dict()
    options["stdout"] = subprocess.PIPE
    options["stderr"] = subprocess.STDOUT

    returncode = process.returncode
    if check and returncode != 0:
        raise GitCommandError(working_path, cmd, output=out)
    return returncode, out 
```

Enter fullscreen mode Exit fullscreen mode

的确，代码多了一点，也有一点重复，但是:

*   原来实现中的倍数`if raises`不见了。函数中的`if`越少越好。
*   `raises`参数做了非常不同的事情:
    *   第一，它改变了函数的返回类型
    *   第二，它迫使调用者显式地处理命令失败的情况。

现在，关于您是否想要捕获输出的意图被编码在函数名称 ( `run_git_captured`而不是`run_git`)中的*。*

还要注意，你不能忘记检查返回代码了。

你可以写:

```
_, out = run_git_captured(repo_path, "some-cmd")
# We know `out` is *not* an error message, because if `some-cmd` failed, an
# exception would have been raised before `out` was set. 
```

Enter fullscreen mode Exit fullscreen mode

或者你可以明确地说明你的错误处理:

```
rc, out = run_git_captured(repo_path, "some-cmd", check=False)
if rc == 0:
    # Ditto, out can't be an error message else:
    # Need to handle error here 
```

Enter fullscreen mode Exit fullscreen mode

我认为这本身就是使用 mypy 的一个很好的理由:)

# 接下来是什么

嗯，`mypy`变更已经被[合并](https://github.com/SuperTanker/tsrc/pull/110)，CI 现在在严格模式下对每个拉请求运行`mypy`。

我仍然对类型注释的其他好处感到好奇，我无法检查这些好处(可维护性、代码理解、易于重构……)。

我想我们会看到 tsrc 的下一次重构进展如何。

# 结论

我们看到了 mypy 如何在保持相对较少的误报的同时，仍然发现了不一致、一些 bug，甚至是设计问题。

所以，我的建议是你有机会就用。干杯！

* * *

*感谢您阅读到目前为止:)*

我很想听听你的想法，所以请在下面留下你的评论，或者阅读[反馈页面](https://dmerej.info/blog/pages/feedback/)了解更多与我联系的方式。

* * *

1.  如果你想了解更多，可以随意浏览[文档](https://supertanker.github.io/tsrc/)，或者阅读[介绍帖](https://dmerej.info/blog/post/introducing-tsrc/)。 [↩](#fnref1)

2.  干代表“不要重复自己” [↩](#fnref2)