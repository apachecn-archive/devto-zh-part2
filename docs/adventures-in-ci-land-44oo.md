# 词地奇遇

> 原文：<https://dev.to/dmerejkowsky/adventures-in-ci-land-44oo>

*最初发表于[我的博客](https://dmerej.info/blog/post/adventures-in-ci-land/)。*

今天在工作中，我编写了一个 CI 脚本来测试 react 应用程序，结果比预期的要复杂一些。

让我们试着重现我遇到的有趣问题以及我是如何解决它们的。

# 设置舞台

如果你想试着复制我所做的，这是你需要的。

*   Node.js，yarn
*   Python3 和`pipenv`
*   `chromedriver`二进制。

让我们从创建一个简单的 React 应用程序开始:

```
$ yarn global add create-react-app
$ create-react-app hello
$ cd hello
$ yarn 
```

Enter fullscreen mode Exit fullscreen mode

我们现在有一个漂亮的 React 应用程序运行在我们最喜欢的浏览器中。

让我们编辑`App.js`文件来显示`Hello, world`而不是

```
import React, { Component } from 'react';

class App extends Component {
  render() {
    return (
      <p>Hello, world!</p>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

# 增加一些端到端的测试

让我们用`pipenv`来创建一个我们需要的 virtualenv:

```
$ pipenv install pytest
$ pipenv install selenium
$ pipenv shell 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们使用 selenium 和 pytest <sup id="fnref1">[1](#fn1)</sup> 添加一些端到端的功能。

```
# in test_hello.py import selenium.webdriver

def test_home():
    driver = selenium.webdriver.Chrome()
    driver.get("http://127.0.0.1:3000")
    assert "Hello, world!" in driver.page_source 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以像往常一样用 pytest 运行测试:

```
$ pytest
collected 1 item

test_hello.py . [100%]
1 passed in 4.77 seconds 
```

Enter fullscreen mode Exit fullscreen mode

好了，成功了！

现在，让我们假设您有一个开发应用程序的团队，您希望每当有人在这个 repo 上创建一个合并请求时，就运行这些测试。

这被称为*持续集成*(简称 CI ),相信我，这比告诉你的队友在提交他们的变更以供审查之前记得运行测试要好得多！

# 写词

我们在工作中使用`GitLab`,非常喜欢它的 CI 特性。

如果你完全不了解 GitLab CI，下面是它的工作原理:

*   你在一些机器上安装和配置`gitlab-runner`程序(称为*转轮*
*   然后编写一个包含工作描述的`.gitlab-ci.yml`文件。

在我的工作中，我们喜欢保持`.gitlab-ci.yml`简单，并保持 CI 脚本的代码独立，就像这样:

(注意我们如何使用`python3 -m pipenv`而不仅仅是`pipenv`。这是为了确保`pipenv`可以运行预期版本的 Python)

```
# in .gitlab-ci.yml

stages:
 - check

check:
  stage: check
  script:
    - python3 -m pipenv install
    - python3 -m pipenv run python ci.py 
```

Enter fullscreen mode Exit fullscreen mode

```
# in ci.py 
def main():
    # Call yarn here 
if __name__ == " __main__":
    main() 
```

Enter fullscreen mode Exit fullscreen mode

我们这样做是因为这样可以很容易地在本地重现 CI 期间发现的构建失败。团队中的任何开发人员都可以直接在他们的机器上运行`python ci/ci.py`,而不是试图从 yaml 文件中复制/粘贴代码。

# 不明不白

目前，selenium 测试使用成熟的 Chrome 来运行测试。这对开发人员来说很好，但对 GitLab 运行者来说就不那么好了。

让这些程序在一个无头的 Chrome 上运行会好得多，也就是说，没有任何 GUI。

让我们通过添加一个`--headless`选项来解决这个问题:

```
# in conftest.py 
import pytest

def pytest_addoption(parser):
    parser.addoption("--headless", action="store_true")

@pytest.fixture
def headless(request):
    return request.config.getoption("--headless") 
```

Enter fullscreen mode Exit fullscreen mode

```
# in test_hello.py 
from selenium.webdriver.chrome.options import Options as ChromeOptions

def test_home(headless):
    options = ChromeOptions()
    options.headless = headless
    driver = selenium.webdriver.Chrome(chrome_options=options)
    ... 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们使用`--headless`选项运行`pytest`，pytest 会将`test_home`函数的`headless`参数设置为`True`。这就是 pytest *夹具*的工作原理。

无论如何，我们现在可以通过运行:
来检查它是否工作

```
$ pytest --headless 
```

Enter fullscreen mode Exit fullscreen mode

# 写词

所以现在我们面临一个新的挑战:我们需要在运行`pytest`之前运行`yarn start` *，并在 selenium 测试完成后终止 React 脚本。*

在 Python 中做这件事的一个好方法是使用`with`语句，所以让我们这么做:

```
class BackgroundProcess:
    """ Run `yarn start` in the background. Ensure the yarn process
    is killed when exiting the `with` block

    """
    def __init__ (self):
        self.process = None

    def __enter__ (self):
        self.process = subprocess.Popen(["yarn", "start"])

    def __exit__ (self, type, value, tb):
        self.process.terminate()

def main():
    with BackgroundProcess("yarn", "start"):
        subprocess.run(["pytest", "--headless"], check=True)

if __name__ == " __main__":
    main() 
```

Enter fullscreen mode Exit fullscreen mode

在`with`块的内容之前，也就是在`pytest`开始之前，`__enter__`方法将被调用。然后在`pytest`完成后*调用`__exit__`方法，即使发生了异常*，将关于异常的数据作为参数传递给`__exit__ ()`方法。如果发生这种情况，除了再加注之外，我们什么都不想做，所以我们忽略它们。

反正这比用`try/except/finally`可读性强多了，你说呢？

我们仍然需要一个小小的修正:默认情况下，`yarn start`会在我们的浏览器上打开一个新标签。这在我们处理 JavaScript 代码时很棒，但在这里我们处理的是 CI 脚本，所以我们倾向于禁用这种行为。

幸运的是，我们所要做的就是将`BROWSER`环境变量设置为`NONE` :

```
class BackgroundProcess:
    ...

    def __enter__ (self):
        env = os.environ.copy()
        env["BROWSER"] = "NONE"
        self.process = subprocess.Popen(self.cmd, env=env) 
```

Enter fullscreen mode Exit fullscreen mode

注意:您可能想知道为什么我们不直接在`.gitlab-ci.yml`文件中设置`BROWSER`环境变量。这本来是可行的，但是这里我们创建了当前环境变量的一个特殊的*副本*，并且我们只为`yarn`进程设置了`BROWSER`环境变量*。为什么？*

好吧，如果你认为环境变量是讨厌的全局变量(你应该这样认为:进程的环境只是一个大的可变共享状态)，那么这样限制它们的范围是有意义的。

不管怎样，回到主要话题:

# 臭虫

注意:本文的其余部分假设您使用的是 Linux。在其他操作系统上，情况可能会有所不同(或者根本没有)。

看看 CI 剧本行不行。

```
$ python ci.py
yarn run v1.7.0
$ react-scripts start
Starting the development server...
...
1 passed in 4.77 seconds 
```

Enter fullscreen mode Exit fullscreen mode

让我们再运行一次，检查一下`yarn`进程确实被终止了:

```
$ python ci.py
? Something is already running on port 3000\. Probably:
  hello (pid 16508)

Would you like to run the app on another port instead? (Y/n) 
```

Enter fullscreen mode Exit fullscreen mode

啊哦。

让我们运行`pgrep`来检查`yarn`进程是否死了:

```
$ pgrep yarn
[err 1] 
```

Enter fullscreen mode Exit fullscreen mode

纱线流程*被*终止。怎么回事？

如果我们看一下`.terminate()`的实现，我们会发现:

```
# in /usr/lib/python3.6/subprocess.py 
class Popen:

      def send_signal(self, sig):
          """Send a signal to the process."""
          # Skip signalling a process that we know has already died.
          if self.returncode is None:
              os.kill(self.pid, sig)

      def terminate(self):
          """Terminate the process with SIGTERM
          """
          self.send_signal(signal.SIGTERM) 
```

Enter fullscreen mode Exit fullscreen mode

因此，`terminate()`只是使用进程 ID ( `pid`)发送`SIGTERM`信号。窃听器不在那里。

# 赤裸裸的真相

事实是我们刚刚创造了一个孤儿(我们是怪物！)

当我们运行`yarn start`时，`yarn`进程查看了`package.json`中一个名为`start`的部分，发现是这样的:

```
{
...
  "scripts": {
    "start": "react-scripts start",
    ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后它创建了一个*子*进程，即`react-scripts start`，有一个*不同的 PID* 。

所以当我们杀死父进程时，`node`进程就变成了孤儿，因为它的父进程已经死了(可怜的小进程)。

至少在 Linux 上，所有的孤立进程都会自动重新连接到机器启动后创建的第一个进程。(`systemd`在我的机器上)。该进程的 PID 始终等于 1，通常称为`init`。

我们可以通过运行`pstree` :
来检查

```
$ pstree
systemd─┬ <- PID 1
...
        ├─node── <- our poor orphan
...
        ├─plasmashell─┬
                      ├─konsole─┬─zsh─ <- our shell 
```

Enter fullscreen mode Exit fullscreen mode

那么，我们如何确保`node`子进程也被杀死呢？

有一些奇特的方法可以解决这类问题(例如，我们可以使用`cgroups`),但是我们可以用 Python stdlib 来解决。

原来我们可以在`subprocess.Popen()`调用中使用`start_new_session`参数。这将创建一个*会话*，并将`yarn`进程(及其所有子进程)附加到它上面。

然后我们可以发送`SIGTERM`信号给父进程的 PID，会话中的所有进程都会收到:

```
import os
import signal

def __enter__ (self):
  ...
  self.process = subprocess.Popen(self.cmd, start_new_session=True)

def __exit__ (self):
    os.killpg(self.process.pid, signal.SIGTERM) 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们重新运行我们的脚本，我们可以看到当 CI 脚本终止时，`yarn`和`node`都不存在:

```
$ python ci.py
$ pgrep yarn
[err 1]
$ pgrep node
[err 1] 
```

Enter fullscreen mode Exit fullscreen mode

今天到此为止。干杯！

* * *

*感谢您阅读到目前为止:)*

我很想听听你的想法，所以请在下面留下你的评论，或者阅读[反馈页面](https://dmerej.info/blog/pages/feedback/)，了解更多与我联系的方式。

* * *

1.  这不是我第一次使用这些工具为 Web 应用程序编写端到端测试。例如，参见[移植到 pytest](https://dmerej.info/blog/post/porting-to-pytest-a-practical-example/) 。 [↩](#fnref1)