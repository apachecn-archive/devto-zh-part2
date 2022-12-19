# 用马尔可夫链生成你自己的毕业演讲

> 原文：<https://dev.to/whatrocks/generating-your-own-graduation-speeches-with-markov-chains-55o7>

想象一下。你是一家成立仅三个月的电动滑板车初创公司的创始人兼首席执行官兼产品幻想家。在转变全球运输市场和涉足你的第一笔天使投资(你知道，只是为了让你的脚沾湿)之间，你心爱的母校邀请你向毕业班发表今年的毕业典礼演讲。

没错，你不幸没有从大学辍学，所以你已经失去了那条方便的叙述线索。你同意发表演讲，并向自己保证你会没事的。你对当地城市的摩托车政治或二次充电市场的单位经济毫无疑问。那会给你五分钟左右的时间。也许你甚至可以在舞台上骑着滑板车走向领奖台大笑一场？把它写下来，不要对任何人嘀咕。

但是随着毕业日期的临近，压力越来越大。你勇敢的幕僚长兼旅行社代理人兼唯一真正的朋友问你演讲稿写得怎么样了，你只是微笑着点头，“很好，山姆。而是把这些点连接起来。反过来。”

“你是说像史蒂夫·乔布斯那样的？斯坦福，2005 年。我已经看了无数遍 YouTube 视频了。”

“哦，不，不是这样的。它更多的是关于失败的好处，你知道，想象力的重要性。”

“J.K .罗琳，哈佛，2008。拜托，你不会也开那个世界上最大的格兰芬多聚会玩笑吧。是吗？请说不。现在就说不。”

“不，当然不是。不管怎样，是不是到了我每天写超然感恩日志的时候了？你差点让我又错过了。给我一支你的钢笔。不是那个，是另一个。”

你坐在公共午餐桌旁，一杯姜味绿茶康普茶和一张可怕的白纸放在你面前。

就在——你发誓——你正要开始写有史以来最伟大的毕业典礼演讲时，一个新奇的数据科学家走过来，坐在你的正对面。他们看不出你在状态吗？如果他们只是一直无所事事，你为什么要付他们那么多钱？

“我想我可以帮你。”

你抬头看了他们一眼，勉强。

“我偷听了你和萨姆的谈话。别那样看着我，这是一个开放式的办公室布局——可能也是你的主意。不管怎样，我想我可以帮你。你需要一次演讲。一个好的。而且要快。”

你把(山姆的)笔放在桌子上，双臂交叉，身体后倾。

“我在听。”

“你听说过马尔可夫链吗？”

## 生活中的一点点马氏

在这篇文章中，我将向你展示如何使用 Python 中的一个简单的[马尔可夫链库](https://github.com/jsvine/markovify)和 FloydHub 上的一个开放的毕业典礼演讲数据集，轻松生成你自己的过度紧张和高度感性的毕业典礼陈词滥调。

几分钟后，你会像这样滔滔不绝地说:

> 初创企业或任何其他合作的成功秘诀是，把一个老脑袋贴在一辆摩托车上。他穿过大厅，经过一扇门——门是空的。

或者这个智慧的金块:

> 想想你的祖先:在他们中间，为这里的每一个人，在你的祖先中间，我甚至想过逃离旧的祖先。

### 现在就试试吧

[![Button](img/72130b7743a6586c3ed870384980df27.png)T2】](https://www.floydhub.com/run?template=https://github.com/whatrocks/markov-commencement-speech)

点击此按钮在 FloydHub 上打开一个[工作区](https://docs.floydhub.com/guides/workspace/)，在这里你可以训练一个马尔可夫链模型，在一个真实的 JupyterLab 环境中生成“毕业典礼演讲风格”的句子。大约 300 篇著名演讲的[毕业典礼演讲数据集将被自动附加并在工作区中可用。跟着 Jupyter 笔记本走就行了。就这么简单，伙计们。](https://floydhub.com/whatrocks/datasets/commencement)

### 但是什么是马尔可夫链呢？

以这个长胡子的魔鬼命名的马尔可夫链是一个描述一系列状态(这些状态可能是一些情况，或者一组值，或者，在我们的例子中，是一个句子中的一个词)以及从一个状态移动到任何其他状态的概率的模型。

我发现最好的马尔可夫链解释器是由维克多·鲍威尔和刘易斯·乐和制作的视觉解释器。停下你正在做的事情，看看他们的帖子。

很好，你回来了。

正如您刚刚了解到的，马尔可夫链是各种行业中流行的建模工具，在这些行业中，人们需要对不可能的大型现实世界系统进行建模，如金融、环境科学、计算机科学。鲍威尔和乐和指出，谷歌的大坏 PageRank 算法是一种马尔可夫链。所以这是件大事。

但是马尔可夫链也在自然语言处理(NLP)的文本生成领域找到了一个很好的平衡点。或者，换句话说，它们非常适合创建 Picard 船长推特机器人。

[![Picard](img/af8d724ada7d9dd20659ac2abfd3cba7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--caJSPEdn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4a3q7g8chscl0m9rhtt2.png)

## 产生有史以来最好的演讲

在我们的例子中，我们希望使用马尔可夫链来基于著名的毕业典礼演讲语料库生成随机句子。

幸运的是，第一部分有一个简单的 Python 库。它叫做[马克维奇](https://github.com/jsvine/markovify)。我马上会告诉你如何使用它。

首先，我们需要一些演讲记录。啊，数据——深度学习所有问题的原因和解决方案。

我经常去的网站“NPR 有史以来最好的毕业典礼演讲”是一个很好的起点。从那里，一些[漂亮的声音抓取](https://www.crummy.com/software/BeautifulSoup/)——伴随着从流行的毕业典礼演讲网站上找到的大量人工垃圾邮件删除(唉，不要问)——导致了包含大约 300 个纯文本毕业典礼演讲脚本的数据集的集合。

[毕业典礼演讲数据集](https://www.floydhub.com/whatrocks/datasets/commencement)在 FloydHub 上公开，这样你就可以在自己的项目中使用它。我还整理了一个[简单的 Gatsby.js 静态网站，如果你想在闲暇时随意阅读这些演讲](https://whatrocks.github.io/commencement-db/)。

好了，言归正传。如前所述，markovify 库非常容易使用。让我再次提醒您，只需点击上面的“在 FloydHub 上运行”按钮，就可以在`speech_maker`笔记本中跟随。实际上，这又是:

[![Button](img/72130b7743a6586c3ed870384980df27.png)T2】](https://www.floydhub.com/run?template=https://github.com/whatrocks/markov-commencement-speech)

为了生成我们的句子，我们将遍历我们的数据集中的所有演讲(可在`/floyd/input/speeches`路径获得),并为每个演讲创建一个马尔可夫模型。

```
import os
import markovify

SPEECH_PATH = '/floyd/input/speeches/'

speech_dict = {}
for speech_file in os.listdir(SPEECH_PATH):
    with open(f'{SPEECH_PATH}{speech_file}') as speech:
        contents = speech.read()

        # Create a Markov model for each speech in our dataset
        model = markovify.Text(contents)
        speech_dict[speech_file] = model 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将使用 markovify 的`combine`方法将它们组合成一个大的马尔可夫链。

```
models = list(speech_dict.values())

# Combine the Markov models model_combination = markovify.combine(models) 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将生成我们的随机句子:

```
print(model_combination.make_sentence()) 
```

Enter fullscreen mode Exit fullscreen mode

> 当然，在联邦政府资助的高速公路项目中，我可以做出最优雅、最非凡的产品。

您可能已经注意到，我已经用语音的文件名作为关键字将各个语音模型组织到了一个字典中。你为什么这么做，笨蛋？嗯，如果你继续跟着`speech_maker`笔记本走，你会发现这有助于你在不断尝试的过程中更容易地过滤演讲。

例如，也许你只想从斯坦福大学的演讲中生成句子？还是在麻省理工？还是仅仅来自于 20 世纪 80 年代的演讲？

工作区包含一个 CSV，其中包含每个演讲的元数据(演讲者`name`、`school`和`year`)。这是你在毕业典礼演讲中崭露头角的机会。哦，这里有一个想法:你为什么不刷掉你的辛普森的电视剧本递归神经网络(RNN)代码，并在这个数据集上试一试？

[![simpsons](img/35874e50c6282c58657d1fbff49e4611.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h0VNyweo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a6e2fhj4re3decsdzmlv.jpg)

## 将“在 FloydHub 上运行”添加到您自己的项目中

组织自己的机器学习实验可能是一种真正的痛苦，更不用说试图与他人分享你的工作了。许多人比我聪明(或者是我？)已经承认了数据科学中的再现性危机。

“在 FloydHub 上运行”按钮是有帮助的。通过这个按钮，我们可以让复制和共享您的数据科学项目变得更加容易。

现在，你可以简单地将这个按钮添加到你在 GitHub 上的 repos 中，任何人都可以在 FloydHub 上旋转工作空间，以及你的代码、数据集、深度学习框架和任何其他环境配置。

你需要做的是:

**在你的 repo 中创建一个 floyd.yml 配置文件**

```
machine: gpu
env: pytorch-1.4 
```

Enter fullscreen mode Exit fullscreen mode

**将此片段添加到您的自述文件**

```
<a href="https://floydhub.com/run">
    <img src="https://static.floydhub.com/button/button.svg" alt="Run">
</a> 
```

Enter fullscreen mode Exit fullscreen mode

你完成了！
认真地。现在就用我们的[对象分类](https://github.com/floydhub/image-classification-template)回购来试试吧。甚至这个[岗位的回购](https://github.com/whatrocks/markov-commencement-speech)。

真正神奇的是，您还可以在配置文件中包含所需的数据集。例如，情感分析项目的`floyd.yml`配置文件如下:

```
env: tensorflow-1.7
machine: cpu
data:
  - source: floydhub/datasets/imdb-preprocessed/1
    destination: imdb 
```

Enter fullscreen mode Exit fullscreen mode

这将使用 Tensorflow 1.7 在 CPU 驱动的工作空间中旋转您的代码，并附加 [IMDB 数据集](https://www.floydhub.com/floydhub/datasets/imdb-preprocessed/1)。这使得任何人都可以从 GitHub 自述文件中轻松复制您的实验。

你可以[在我们的文档中阅读更多](https://docs.floydhub.com/guides/run_on_floydhub_button/)关于 FloydHub 上的 Run 按钮。我们期待看到你与世界分享的东西！祝你们好运，毕业生们！