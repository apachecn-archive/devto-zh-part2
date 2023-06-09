# 您在云计算领域的第一份工作/ 3

> 原文：<https://dev.to/yzvyagelskaya/your-first-job-in-the-cloud--3-28ka>

## 第一章。云中的第一份工作

[![yzvyagelskaya image](img/ea80fd4e9cc0f45ad4861b2dfc4912b7.png)](/yzvyagelskaya) [## 你在云中的第一份工作

### Yulia Zvyagelskaya Aug 18 ' 185min read

#machinelearning #tutorial #cloudcomputing](/yzvyagelskaya/your-first-job-in-the-cloud-4lcj)

## 第二章。完成工作

[![yzvyagelskaya image](img/ea80fd4e9cc0f45ad4861b2dfc4912b7.png)](/yzvyagelskaya) [## 您在云计算领域的第一份工作/2

### Yulia Zvyagelskaya 8 月 19 日 1815 分钟阅读

#machinelearning #tutorial #cloudcomputing](/yzvyagelskaya/your-first-job-in-the-cloud-2-4ob8)

## 第三章。让我的云发挥作用

> 我写下了我的快乐歌曲，每个孩子都会喜欢听

### 将作业分割成小块

在前一章中，我们准备了一个环境来启动我们可爱的工作。在我们让这艘船自由航行之前，剩下的唯一一件事就是把整个工作分成几个有逻辑的部分。为什么？

嗯，事情可能会出错。人们可能希望在最后一步进行一些调整。云计算节省了我们笔记本电脑的正常运行时间，但它需要钱。在大多数情况下，当使用我们的模型参数时，我们不需要从头开始重做整个准备步骤，如清理文本、将输入分成训练和测试数据集等。

我建议将文本处理的以下步骤分开(对于图像或其他可能不同的东西，但整个方法仍然有效。)

*   丢弃所有不必要的数据，只留下我们要处理的
*   用排序正则表达式清理输入
*   将输入拆分为训练和测试数据集
*   在训练数据集上训练模型
*   检查测试数据集上的模型

### 一一

我的建议是将脚本分成几个类，每个类扮演自己的角色。这些文件还将包含`__main__`部分，允许将它们作为独立的脚本执行，接受与特定步骤相关的参数。

#### 越小越好

第一步应该在本地*完成*以减小我们将要上传的文件的大小。这节省了我们上传*和*我们篮子中输入数据的大小的时间。

为此，只需加载 CSV，去掉所有不必要的列，然后保存回来。就是这样。结果应该被复制到 bucket 中，所有步骤都将在云中完成。

#### 看门人进行营救

在大多数关于这个主题的文章、书籍和演讲中，人们都建议去掉所有的标点符号，并增加句法糖(“我本来可以”我本来可以”)来帮助训练者识别相同的单词。大多数(如果不是全部的话)建议用过时的正则表达式来做这件事。

互联网上的文本不同于打字机产生的文本。现在都在用 *unicode* 。如今，[撇号](https://en.wikipedia.org/wiki/Apostrophe)可能是由懒惰的帖子作者*打出来的“’”和*“’”，如果文本的创建者有某种印刷美感的话。引号 *' " ' "* 、破折号*--*、数字 *1 2* 甚至ʟᴇᴛᴛᴇʀs 𝒾𝓃 𝓉𝒽𝑒 𝖆𝖗𝖙𝖎𝖈𝖑𝖊.也是如此遗留正则表达式将无法识别所有的动物园。

幸运的是，现代正则表达式有匹配器，可以精确匹配我们需要的语义。Tose 被称为 [*字符类*](https://en.wikipedia.org/wiki/Regular_expression#Character_classes) ，可以用来匹配所有的标点符号，或者所有的字母*和*数字。

对于文本处理，我建议只保留字母数字(字母和数字)和标点符号。后者应该统一(所有的双引号应该转换成打字机双引号，单引号和破折号也一样。)

在这之后，可以使用缩短形式的列表来扩展所有的*“我是”_s 到 _“我是”_s 等等。基本上就是这样。_Dump* 将此步骤的结果转储到铲斗。在输入数据改变之前，可以在模型训练过程的后续执行中避免该步骤。我个人用的是*泡菜*，但是格式并不重要。

#### 兄弟集:训练和测试

常见的方法是将数据集分成几部分，一部分用于训练模型，另一部分用于测试模型。Split *和*将结果保存到购物篮。这个过程通常需要几个参数，比如*填充*和*最大字数*，这些参数很少改变。你可能不会经常调整它们。

保存结果。

#### 训练模型

训练模型。确保你将所有的*记录到记录器*中，而不是记录到标准输出中，以保存进程的日志。我通常使用`logging.info`来调试消息，使用`logging.warn`来报告我想要报告的重要消息。谷歌 ML 日志查看器允许按严重程度过滤掉日志信息，以后人们可能只浏览一下*重要的*内容。

保存模型。

#### 检查一下

我也在云中签入。稍后，人们可能会将模型下载到本地，并使用步骤 3 中保存的测试集来测试它，但是我可以在云端检查日志。

### 总结归纳

如果您像我一样，将这些步骤放入类中，那么将在云中运行的包的结果`__main__`应该是:

```
if __name__ == "__main__":
  # parse all the arguments 
  if arguments.pop('janitize'):
    Janitor(**arguments).cleanup(remote_dir)
  if arguments.pop('split'):
    Splitter(keep_n=arguments.pop('keep_n')).split(remote_dir)
  if arguments.pop('train'):
    model = Modeller(**arguments).train_model(remote_dir)
    if arguments.pop('check'):
      Modeller(**arguments).check_model(remote_dir, model=model) 
```

Enter fullscreen mode Exit fullscreen mode

这就是我现在想分享的全部内容。快乐云！