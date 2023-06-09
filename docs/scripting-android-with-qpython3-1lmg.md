# 用 QPython3 编写 Android 脚本

> 原文：<https://dev.to/matthewodle/scripting-android-with-qpython3-1lmg>

这是我(初出茅庐，由于普遍缺乏效率而被放弃)的纯移动脚本开发工作流程[(全文在此)](http://blog.matthewodle.com/roll-your-own-name-generator-with-qpython3-on-an-android-device/)。

[![](img/3ae8943350c4d2333fa516c1cc376488.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4SJTF62m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.matthewodle.com/wp-content/uploads/2018/02/generated-names-300x97.png)

* * *

# 移动开发

去年夏天，我进行了一次自驾游，想尝试一种仅限移动设备的工作流程。我需要的主要组件是一个解释器、一个 IDE 和源代码控制。

#### 我发现并使用的应用套件如下:

*   qpython 3(python IDE 和脚本运行程序)
*   SGit(源代码管理)
*   LabCoat (GitLab 资源库查看器)
*   Dropbox(即席、易于访问的文件存储)
*   Dropsync(移动设备上目录之间的单向或双向同步)
*   Epsilon Notes(降价编辑器和渲染器)

在本教程中，我将讲述我使用 QPython3 的经历。

* * *

## 这次努力的收获:

#### 加:

*   都在你的口袋里
*   测试代码的短反馈循环

#### △:

*   难以跨多个应用程序查看信息
*   编辑代码可能是一件苦差事
*   在我去过的那个商场里，没有足够的有舒适座位的充电站

#### 推荐:

*   买一个蓝牙键盘
*   带上便携式充电器
*   坐得离陌生人很近，这样你就可以分享充电站，也许他们会离开(他们没有离开，但提出重新安排座位，这样我们就可以舒适地坐在一起，同时静静地盯着我们的手机)

## 本帖涵盖:

1.  如何经营一个“Hello World！”QPython3 中的脚本
2.  如何将文件加载到 QPython3 脚本中
3.  我如何为我的宠物取名字

### 安装列表

*   [qython 3(v 1 . 0 . 3)](https://play.google.com/store/apps/details?id=org.qpython.qpy3)

## 运行 Qpython3，打开 hello_world.py

[![QPython3 Home](img/499a7fc1fff62109c0955c8b1c28a142.png "QPython3 Home")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FcA1UlUS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.matthewodle.com/wp-content/uploads/2018/02/1-qpython-main.png)

选择`Programs` > `hello_world.py` > `Open`查看脚本内容。

[![QPython3 Open](img/15704ed70cd03293549edd20826114fe.png "QPython3 Open")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--f-S7pbbd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.matthewodle.com/wp-content/uploads/2018/02/3-qpython-open.png)

[![QPython3 Hello World Contents](img/87f590fe8809d35ed50f2a6a8c375ac8.png "QPython3 Hello World Contents")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8ZaDRqN6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.matthewodle.com/wp-content/uploads/2018/02/4-qpython-hello-world-contents.png)

这里超标准的 python3。该脚本使用 [SL4A 库](http://pythoncentral.io/python-for-android-the-scripting-layer-sl4a/)(Android 的脚本层)来显示 Android toast，然后将`Hello world!`打印到控制台。

单击脚本编辑器屏幕底部工具栏上的右箭头运行脚本。你也可以退出编辑器，再次点击`hello_world.py`，选择`Run`。

[![QPython3 Hello World Execution](img/41e31c3fd40dadc29436af09873cf333.png "QPython3 Hello World Execution")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G2m5wIGw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.matthewodle.com/wp-content/uploads/2018/02/5-qpython-hello-world-execution.png)

就是这样！

如果您愿意，可以尝试其他一些默认脚本。要查看 SL4A 库的一些功能，运行`test.py`在各种传感器和 UI 元素上运行一系列测试。它将演示一些东西，如文本到语音，各种形式的输入元素，和进度条。

* * *

## 演示 Qpython3 App -物品名称生成器！

既然您已经能够在您的 Android 设备上执行 python 脚本，那么让我们做唯一合乎逻辑的下一件事:编写一个简短的幻想设备名称生成器脚本。

### 加载文件

首先，我们需要加载文件内容。我编写了一个快速 loadfile 函数，它接受一个文件名并返回文件中包含的字符串列表。

[![QPython3 Namegen Assets](img/39760b83d0d668af87b14b4ce6279c80.png "QPython3 Namegen Assets")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jJ0qXzrA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.matthewodle.com/wp-content/uploads/2018/02/10-qpython-assets.png)

`weapons.txt`

```
bow
axe
mace
sword
spear
flail
etc

```

加载的文件中的每一行都将通过换行或者`\n`作为一个单独的字符串存储在列表中。

`fileutils.py`

```
def loadfile(filename):
    root_path = '/storage/emulated/0/qpython/scrdipts3/modules/namegen/assets/'
    file_path = '{}{}'.format(root_path, filename)
    loaded_file = open (file_path, 'r')
    return loaded_file.read().split('\n')

```

[![QPython3 fileutils.py](img/62eca08f6500f538c40b9531bfd33fa0.png "QPython3 fileutils.py")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GKbrwFWH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.matthewodle.com/wp-content/uploads/2018/02/30-qpython-fileutils.png)

这个`root_path`很难看，但是 QPython3 不支持相对路径。可以通过使用`os`库来改进:

```
import os

def loadfile(filename):
    root_path = os.path.dirname(os.path.abspath(__file__))
    ...

```

这将允许您在其他设备上运行脚本，比如 Linux VM。

### 名称生成器脚本

#### 这个脚本将:

*   从我们的`fileutils.py`模块导入 loadfile 函数
*   将 3 个文本文件的内容读入列表:前缀、武器和后缀
*   对于武器列表中的每种武器:
    *   打印一个由当前武器名称和一个随机前缀和后缀组成的字符串

**第一步**:加载文件。从我们的 fileutils 模块导入 loadfile 函数。

```
from fileutils import loadfile

```

**第二步**:调用 loadfile 创建武器列表，然后打印出来验证。

```
from fileutils import loadfile

weapons = loadfile('weapons.txt')
print (weapons)

```

如果您运行这个脚本，您应该会看到一个列表，每个武器都有一个条目打印到控制台:

```
['bow', 'axe', ...]

```

**步骤 3** :为前缀和后缀调用 loadfile。

```
from fileutils import loadfile

weapons = loadfile('weapons.txt')
prefixes = loadfile('prefixes.txt')
suffixes = loadfile('suffixes.txt')

```

**第四步**:最后，循环遍历武器列表，选择一个随机的前缀和后缀，打印出组合。不要忘记导入`random`模块。

```
import random
from fileutils import loadfile

weapons = loadfile('weapons.txt')
prefixes = loadfile('prefixes.txt')
suffixes = loadfile('suffixes.txt')

for weapon in weapons:
    prefix = random.choice(prefixes)
    suffix = random.choice(suffixes)
    print ("{} {} of {}".format(prefix, weapon, suffix))

```

[![QPython3 Namegen Code](img/8f0ba171f21aa2583c5d7b9446d0d377.png "QPython3 Namegen Code")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UCMsnW0N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.matthewodle.com/wp-content/uploads/2018/02/20-namegen-code.png)

在您的控制台中，您应该会看到类似这样的内容:

[![QPython3 Generated Names](img/01292ac903655fbe94286c33accba6b5.png "QPython3 Generated Names")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sIzLQppE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.matthewodle.com/wp-content/uploads/2018/02/40-qpython-generated-names.png)

# 结论

现在，您应该熟悉在 Android 设备上编写和执行 python 模块了！添加您自己的前缀、后缀和武器，将您同样令人敬畏的对手加入到诸如`satisfactory cuisses of pain`、`repressed flail of efficacy`和`gesticulating bow of obscurity`这样的传奇名字中！

感谢阅读！