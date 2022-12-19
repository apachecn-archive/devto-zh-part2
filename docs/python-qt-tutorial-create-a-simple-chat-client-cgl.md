# Python Qt 教程:创建简单的聊天客户端

> 原文：<https://dev.to/m_herrmann/python-qt-tutorial-create-a-simple-chat-client-cgl>

这个 Python Qt 教程向您展示了如何创建一个(工作！)适用于 Windows、Mac 或 Linux 的聊天客户端。我们将为 Python 安装 Qt，编写客户端并为它构建一个安装程序。

# 使用来自 Python 的 Qt

[Qt](https://www.qt.io/) 是一个 GUI 框架。是用 C++开发的。但是在 2018 年，Qt 公司为 Python 发布了 [Qt。这提高了这种动态语言的生产率，同时保留了 C++的大部分速度。](https://www.qt.io/qt-for-python)

在 Qt for Python 问世之前，大多数人使用的是一个名为 [PyQt](https://riverbankcomputing.com/software/pyqt/intro) 的库。它更加成熟，而且非常稳定。它的缺点是需要你购买商业项目的许可证。这与 Python 的 Qt 不同，后者在 LGPL 下获得许可，因此通常可以免费使用。

从代码的角度来看，使用哪一个绑定并没有太大的区别。它们的 API 几乎一模一样。我们在这里使用 Qt for Python，因为它比较新。如果你想用 PyQt 来代替，查看一下这个 PyQt5 教程。

# 为 Python 安装 Qt

由于 venv 和 pip 模块，在 Python 3 中安装 Qt 非常容易。打开终端并导航到一个空目录。假设 Python 的安装目录在您的路径上，那么您可以输入以下内容:

```
python -m venv virtualenv 
```

Enter fullscreen mode Exit fullscreen mode

这将在`virtualenv`目录中创建一个虚拟环境。它将存储项目的依赖项。

要激活虚拟环境，请使用以下两个命令之一:

```
call virtualenv\scripts\activate.bat  # on Windows
source virtualenv/bin/activate        # On Mac / Linux 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过 shell 中的前缀`(virtualenv)`来判断激活是否成功:

[![Screenshot of an activate Python 3 virtual environment on Windows](../Images/68840acde49b19a34c78e4b8251dcea9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rR_FzC8d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://build-system.fman.io/static/public/img/python3-virtual-environment-active-screenshot-windows.png)

对于本教程的剩余部分，我们将假设虚拟环境是活动的。

要安装 Qt for Python，请输入以下命令:

```
pip install PySide2 
```

Enter fullscreen mode Exit fullscreen mode

它被称为`PySide2`是有历史原因的:PySide 是第一个官方 Python 绑定，由当时的 Qt 诺基亚所有者在 2009 年发布。2011 年诺基亚出售 Qt 后，PySide 的开发陷入停滞。PySide2 是社区维护它的努力。最终，在 2016 年，Qt 公司[承诺](https://groups.google.com/d/msg/pyside-dev/pqwzngAGLWE/kXUpXBhILAAJ)正式支持这个项目。

# 一个聊天客户端

我们现在将使用 Qt for Python 来创建一个(工作！)聊天客户端。它看起来是这样的:

[![Qt sample application](../Images/b4ba53da28cc2233071fcec629318b44.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EWM8a9rR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://build-system.fman.io/static/public/img/python-qt-tutorial/qt-example-application.png)

我们将自上而下地建造它。首先，显示每个人消息的文本区域:

[![QTextEdit screenshot](../Images/ad9f1a9ef67b1c6d34c7ffac13263c6d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1Td4S193--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://build-system.fman.io/static/public/img/python-qt-tutorial/qtextedit-screenshot.png)

要显示它，在您的终端中启动`python`并输入以下命令:

[![QTextEdit sample code on Windows](../Images/dbf3d4de106bda9f220174d53393ef9d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AVHuh5qc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://build-system.fman.io/static/public/img/python-qt-tutorial/qtextedit-sample-code.png)

第一行告诉 Python 加载 PySide:

```
from PySide2.QtWidgets import * 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们创建一个`QApplication`。这在任何 Qt 应用中都是必需的。我们传递空括号`[]`来表示没有命令行参数:

```
app = QApplication([]) 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们通过以下代码行创建文本区域:

```
text_area = QTextEdit() 
```

Enter fullscreen mode Exit fullscreen mode

我们告诉 Qt 显示文本区域:

```
text_area.show() 
```

Enter fullscreen mode Exit fullscreen mode

这打开了一个窗口。为了让它响应用户输入，我们最后需要以下命令:

```
app.exec_() 
```

Enter fullscreen mode Exit fullscreen mode

现在 Qt 处理击键、鼠标事件等。直到您关闭包含文本字段的小窗口。

如果上面的截图有效，那么恭喜你！您刚刚用 Qt for Python 创建了一个非常简单的 GUI 应用程序。

# 定位 GUI 元素

我们现在想要在文本区域下面添加用于输入消息的文本字段:

[![Qt sample application: A QLineEdit below a QTextEdit](../Images/a337e6a9ad2733ed37ae0a658c1d7000.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JYvhtrpc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://build-system.fman.io/static/public/img/python-qt-tutorial/qt-example-application-empty.png)

但是我们如何告诉 Qt 把它放在文本区域的下面(而不是右边)？答案是通过一个*布局*。布局告诉 Qt 如何定位 GUI 元素。

为了将文本字段放在文本区域下面，我们使用如下的`QVBoxLayout`:

```
from PySide2.QtWidgets import *
app = QApplication([])
layout = QVBoxLayout()
layout.addWidget(QTextEdit())
layout.addWidget(QLineEdit())
window = QWidget()
window.setLayout(layout)
window.show()
app.exec_() 
```

Enter fullscreen mode Exit fullscreen mode

我们再次导入`PySide2`并创建一个`QApplication`。我们继续构建布局:首先是一个文本区域，就像我们之前看到的那样。然后是一个类型为`QLineEdit`的文本字段。接下来，我们创建一个窗口来包含我们的布局。我们通过调用上面的`.show()`并使用`app.exec_()`将控制权交给 Qt 来结束。

# 插曲:小工具

上面我们看到的`QWidget`，是 Qt 中最基本的 GUI 元素。所有其他控件都是它的专门化:按钮、标签、窗口、文本字段等。小部件类似于 HTML 元素，它们封装了外观和行为，并且可以嵌套。

下面是最重要的 Qt 部件的截图:

[![Screenshot of the most common Qt widgets](../Images/b5688db5e184adba83e171bb69f33376.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AXZUEeZ8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://build-system.fman.io/static/public/img/python-qt-tutorial/qt-widgets-screenshot.png)

例如，它显示:

*   [QLabel](https://doc.qt.io/qt-5/qlabel.html)
*   [QComboBox](https://doc.qt.io/qt-5/qcombobox.html)
*   [QCheckBox](https://doc.qt.io/qt-5/qcheckbox.html)
*   [qushbutton](https://doc.qt.io/qt-5/qpushbutton.html)
*   [单选按钮](https://doc.qt.io/qt-5/qradiobutton.html)
*   [QTextEdit](https://doc.qt.io/qt-5/qtextedit.html)
*   [QLineEdit](https://doc.qt.io/qt-5/qlineedit.html)
*   [QSlider](https://doc.qt.io/qt-5/qslider.html)
*   [QProgressBar](https://doc.qt.io/qt-5/qprogressbar.html)

(截图代码可在[此处](https://build-system.fman.io/static/public/files/widgets_example_pyside.py)获得，供您参考。)

# 与服务器对话

准备好 GUI 后，我们需要将它连接到服务器。最简单的方法是通过`requests`库。您可以通过以下命令安装它:

```
pip install requests 
```

Enter fullscreen mode Exit fullscreen mode

完成此操作后，再次启动 python 并输入以下命令:

```
import requests
chat_url = 'https://build-system.fman.io/chat'
requests.get(chat_url).text 
```

Enter fullscreen mode Exit fullscreen mode

这应该给你最后聊天信息的 HTML。

我们也可以发送消息。这里有一个例子，但是一定要用你自己的名字和文字:-)

```
requests.post(chat_url, {'name': 'Matt', 'message': 'Nice tutorial'}) 
```

Enter fullscreen mode Exit fullscreen mode

当您再次调用`requests.get(chat_url).text`时，您的消息应该在输出的末尾。

# 信号

我们的聊天客户端需要处理某些事件:

*   当用户按下`Enter`时，当前消息应该被发送到服务器。
*   我们希望每秒钟从服务器获取并显示一次新消息。

Qt 使用一种称为*信号*的机制来对用户输入或定时器等事件做出反应。这里有一个例子:

```
from PySide2.QtWidgets import *
from PySide2.QtCore import QTimer
app = QApplication([])
timer = QTimer()
timer.timeout.connect(lambda: print('hi!'))
timer.start(1000)
app.exec_() 
```

Enter fullscreen mode Exit fullscreen mode

当您运行这段代码时，消息`hi!`每秒钟在您的终端中出现一次:

[![QTimer example](../Images/56998597f66de32c8bb737a1250c889c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HLVezO_U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://build-system.fman.io/static/public/img/python-qt-tutorial/qtimer-example.png)

上面代码中的信号是`timer.timeout`。我们使用它的`.connect(...)`方法来指定当信号出现时被调用的函数。在这个例子中，我们使用了内嵌函数`lambda: print('hi!')`。我们的另一个调用`timer.start(1000)`确保 Qt 每 1000 毫秒运行一次这个函数。

# 把所有的东西放在一起

我们现在可以完全实现聊天客户端了。将下面的代码复制到你的`virtualenv`目录下的一个文件中，比如说`main.py`。一定要在最上面填上你的名字，不然你就不能发消息了。然后，您可以使用以下命令运行聊天:

```
python main.py 
```

Enter fullscreen mode Exit fullscreen mode

您将能够看到其他人写了什么，并发送您自己的消息。聊天愉快！:-)

```
from PySide2.QtCore import *
from PySide2.QtWidgets import *

import requests

name = '' # Enter your name here!
chat_url = 'https://build-system.fman.io/chat'

# GUI:
app = QApplication([])
text_area = QTextEdit()
text_area.setFocusPolicy(Qt.NoFocus)
message = QLineEdit()
layout = QVBoxLayout()
layout.addWidget(text_area)
layout.addWidget(message)
window = QWidget()
window.setLayout(layout)
window.show()

# Event handlers:
def refresh_messages():
    text_area.setHtml(requests.get(chat_url).text)

def send_message():
    requests.post(chat_url, {'name': name, 'message': message.text()})
    message.clear()

# Signals:
message.returnPressed.connect(send_message)
timer = QTimer()
timer.timeout.connect(refresh_messages)
timer.start(1000)

app.exec_() 
```

Enter fullscreen mode Exit fullscreen mode

# 在其他电脑上运行你的应用

我们现在可以用命令`python main.py`启动我们的应用程序。但是我们如何在别人的电脑上运行它呢？尤其是他们没有安装 Python 的情况下？

我们想要的是一个独立的可执行文件，不需要 Python(或其他任何东西)来运行。创建这样一个二进制的过程在 Python 中被称为*冻结*。

PyInstaller 等特殊的 Python 库允许您冻结应用程序。不幸的是，他们只能让你走 80%的路。通常，您仍然需要手动添加或删除 dll 或共享库。它们不能解决基本的任务，比如访问数据文件、创建安装程序或自动更新。

因此，我们将使用一个更现代的库，名为 [fbs](https://build-system.fman.io) 。它基于 PyInstaller，但是增加了缺少的 20%。您可以通过以下命令安装它:

```
pip install fbs PyInstaller==3.4 
```

Enter fullscreen mode Exit fullscreen mode

要启动新的 fbs 项目，请输入:

```
python -m fbs startproject 
```

Enter fullscreen mode Exit fullscreen mode

这会问你几个问题。可以用 *Chat* 作为应用名，你的名字作为作者。

完成后，请[下载该文件](https://build-system.fman.io/static/public/files/python-qt-tutorial/main.py)，并将其复制到`src/main/python/main.py`。用文本编辑器打开文件，用您的名字替换 name。下面的命令像以前一样开始聊天:

```
python -m fbs run 
```

Enter fullscreen mode Exit fullscreen mode

更重要的是，您现在可以通过以下命令创建独立的可执行文件:

```
python -m fbs freeze 
```

Enter fullscreen mode Exit fullscreen mode

这会将您的 Python 代码冻结在文件夹 target/Chat 中。您可以将此目录复制到任何一台与您的操作系统相同的电脑上，以便在那里运行聊天。是不是很牛逼？

# 创建安装程序

您也可以使用 fbs 来生成安装程序。这是通过命令`python -m fbs installer`完成的:

(在 Windows 上，此步骤要求 [NSIS](http://nsis.sourceforge.net/Main_Page) 在路径上。在 Linux 上，你需要 [fpm](https://fpm.readthedocs.io/en/latest/installing.html) 。)

如果您有一个希望转换成 fbs 的现有应用程序，[这篇文章](https://build-system.fman.io/pyqt-exe-creation/)可能会帮助您。它是为 PyQt 编写的，但同样适用于 Qt for Python / PySide。另一个很好的信息来源是 [fbs 的教程](https://github.com/mherrmann/fbs-tutorial)。

# 结论

我们在上面看到了如何使用 Qt for Python 创建一个简单的桌面应用程序。我们使用了基本的小部件，如`QTextEdit`和`QLineEdit`，以及`QVBoxLayout`来排列它们。Qt 的信号让我们可以让这个 GUI 动态化:首先使用一个定时器每 1000 毫秒从服务器获取最新的消息。第二，让我们响应用户按下 Enter 按钮提交消息。最后，我们使用 fbs 轻松创建独立的可执行文件和安装程序。

如果您有任何问题或意见，请随时通过 Twitter [@m_herrmann](https://dev.to/m_herrmann) 联系我！

*本帖最初发表于[这里](https://build-system.fman.io/python-qt-tutorial)。*