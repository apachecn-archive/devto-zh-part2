# 您在云计算领域的第一份工作/2

> 原文：<https://dev.to/yzvyagelskaya/your-first-job-in-the-cloud-2-4ob8>

## 第一章。云中的第一份工作

[![yzvyagelskaya image](img/ea80fd4e9cc0f45ad4861b2dfc4912b7.png)](/yzvyagelskaya) [## 你在云中的第一份工作

### Yulia Zvyagelskaya Aug 18 ' 185min read

#machinelearning #tutorial #cloudcomputing](/yzvyagelskaya/your-first-job-in-the-cloud-4lcj)

## 第二章。完成工作

> 听听吟游诗人的声音！
> —威廉·布莱克

### 设置套餐

在第 1 章的最后，我们能够在云中运行作业。成功完成了(如果没有，请怪谷歌，不要怪我。)我们见过它旁边这个迷人的绿灯图标。现在让我们试试真正的 job‽

还没有。我们还没有成熟到可以进入一个有狮子的笼子。让我们一步一步来。

我假设你手头有代码——显然是训练模型的代码。与其他教程不同，本教程不会为您提供代码培训模型。我们正在讨论部署到 *ML 引擎*。很抱歉。

这段代码可能有一个`import`包。它可能需要`Tensorflow`、`Keras`和其他一些没有包含在 python 标准库中的包。如果你导入的只是`Tensorflow`，其余的都是你自己写的，那么你几乎不需要这个教程。这样勇敢的人应该自己闯过一切。

所以，是的。包裹。将你所有文件中的所有`import`行复制到我们在*第一章*(“我们的第一份工作”)中创建的工作支架的顶部，我努力找到如何在这里锚定帖子内的副标题。)

向云提交作业。大约 8 分钟后检查日志，查看任务是否失败。如果没有，我羡慕你，你似乎只使用默认包含在 *ML 引擎*设置中的标准包。我没那么幸运。Google 包含了非常有限的一组包(可能是为了减少 docker 的启动时间。)

因此，我们需要明确地要求我们需要的附加包。不幸的是，AFAICT，没有办法检索出 *ML 引擎*提供的开箱即用的东西和我们需要的东西之间的差异，所以我们不得不一个接一个地添加它们，提交作业，检查日志，哭泣，对着天空大喊，重复。要添加包，打开我们在第 1 章的*中创建的文件`setup.py`，添加以下几行。* 

```
 from setuptools import find_packages
 from setuptools import setup

 setup(
     name='test1',
     version='0.1',
+    install_requires=['scikit-learn>=0.18','annoy>=1.12','nltk>=3.2'],
+    packages=find_packages(),
+    include_package_data=True,
     description='My First Job'
 ) 
```

Enter fullscreen mode Exit fullscreen mode

继续在`install_requires`中添加条目，直到 *ML 引擎*满足要求，作业返回成功处理。可能会发生这样的情况，你需要的一些包是为比`3.5`更年轻的 python 设计的(在我的例子中，这个包使用了在`3.6`中引入的奇特的新字符串格式`f'Hi, {name}'`)。我没有找到更好的解决方案，而不是在本地下载这个包，移植到`3.5`然后自己重新打包。构建这个包，并把它放入你的 bucket(为此我在那里创建了一个子文件夹`packages`)*和*同一个带有`setup.py`脚本的文件夹。现在我们必须告诉 ML 引擎使用我们的版本。使用
更新您的 shell 脚本

```
 --module-name test1.test1 \
     --package-path ./test1 \
+    --packages my_package-0.1.2.tar.gz \
     --config=test1/test1.yaml \ 
```

Enter fullscreen mode Exit fullscreen mode

对于运行一个作业需要包含到发行版中的所有您自己的包，也应该这样做。对`setup.py`中`setup`的调用中的`install_packages`参数必须相应更新。另外，用`packageUris`参数:
更新你的云配置

```
trainingInput:
  scaleTier: CUSTOM
  masterType: complex_model_m_gpu
  pythonVersion: "3.5"
  runtimeVersion: "1.9"
  packageUris:
    - 'gs://foo-bar-baz-your-bucket-name/packages/my_package-0.1.2.tar.gz' 
```

Enter fullscreen mode Exit fullscreen mode

提交作业，并检查作业现在是否为绿色。

### 各司其职

并非所有第三方包都可以在云中使用。它和我们自己的笔记本电脑一模一样，但是是虚拟的，所以一切都应该是一样的，对吗？嗯，是也不是。一切都是一样的。但是该虚拟机在作业完成时会关闭。这意味着如果我们需要日志之外的一些结果(比如一个训练过的模型文件，你知道的)，我们必须将它存储在运行这个任务的容器之外的某个地方，否则它将和容器一起死亡。

Python 标准`open(file_name, mode)`不支持桶(`gs://...../file_name`)。需要`from tensorflow.python.lib.io import file_io`并将所有对`open(file_name, mode)`的调用改为`file_io.FileIO(file_name, mode=mode)`(注意命名模式参数。)打开的手柄界面是一样的。

一些第三方包有一个显式的`save`方法，接受文件名而不是`FileIO`，唯一的可能是*之后将*复制到桶中。我没有使用 *Google SDK* 来做这件事，因为它不在容器中，所以这里有一小段代码将文件从容器复制到桶中。

下面的代码效率很低，因为它一次加载整个模型，然后将它转储到桶中，但是对于相对较小的模型，它对我有效:

```
model.save(file_path)

with file_io.FileIO(file_path, mode='rb') as i_f:
    with file_io.FileIO(os.path.join(model_dir, file_path), mode='wb+') as o_f:
        o_f.write(i_f.read()) 
```

Enter fullscreen mode Exit fullscreen mode

读写模式都必须设置为二进制。当文件相对较大时，分块读写文件以减少内存消耗是有意义的，但是在二进制 IO 读操作中分块文件似乎超出了本教程的范围。我建议把这个包装到函数中，根据需要调用这个函数。

现在，当我们安装了所有需要的包，并且手头有了在 bucket 中存储文件的功能时，没有什么可以阻止我们在云中尝试我们的模型，在云中加载全部数据。对吗？

没有。我强烈建议将工作分成更小的步骤，比如“清理”、“分成训练和测试集”、“预处理”、“训练”等，并将所有中间结果转储到桶中。因为你需要一个在云中运行的培训过程，所以这应该非常耗时。手头有中间结果可能会在未来为您节省大量时间。例如，调整给予训练过程本身的参数不需要重新运行所有准备步骤。在这种情况下，我们可以从第 N 步的*开始，使用之前转储的上一步的输出作为输入。*

如何做，我将在下一章说明。快乐云！

[![yzvyagelskaya image](img/ea80fd4e9cc0f45ad4861b2dfc4912b7.png)](/yzvyagelskaya) [## 您在云计算领域的第一份工作/ 3

### Yulia Zvyagelskaya 8 月 25 日 184 分钟阅读

#machinelearning #tutorial #cloudcomputing](/yzvyagelskaya/your-first-job-in-the-cloud--3-28ka)