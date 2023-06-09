# 文件结构如何通知编程模型

> 原文：<https://dev.to/hdennen/how-file-structure-informs-the-programming-model-29p0>

我们最近对我们的应用程序进行了一次重组，试图在下一个主要特性出现时缓解一些棘手的问题。

从这一过程中得出两个观察结果:

1)应用程序的文件结构决定了开发人员如何组织他们的逻辑。

2)如果结构反映了糟糕的组织，或者没有意图的分类，那么在该结构中编写的代码将是糟糕的组织和没有意图的分类。

#### 没有目的的分类

考虑逻辑和模型的分离。应该在哪里做出决定？在应用程序级别还是在功能级别？到底该不该造？

也许我们在应用程序级别将它们分开。构建特征时，我们最终在逻辑目录和模型目录中都有一个目录 featureName。我们有重复的命名，但是目录之间是一一对应的，我们可以独立于模型进行逻辑修改。

好的。

但是那些我们没有考虑到的事情呢？系统 api、业务领域、框架和表示之间的界限？如果这些行不存在于文件结构中，它们也不会存在于代码中。

这就是逻辑如何发展到耦合不同的系统问题。这是一个痛苦的地方。

#### 绘制线条

文件结构应该规定，至少在高层次上，向一个方向移动的功能的分离。

```
Things that run the app 
 → things that run the business domain 
 → things that present the business domain 
```

Enter fullscreen mode Exit fullscreen mode

在这个组织中，处理领域的东西不知道呈现它的东西，反之亦然，它们都使用运行应用程序的东西。

让我们考虑两个场景，一个是类型分类文件结构，另一个是分离规范文件结构。

#### 类型分类文件结构

开发人员的任务是根据设备规格构建一个精简版和完整版的特性。开发人员(因为他们擅长自己的工作)抽象出设备检测的系统关注点，然后着手构建这个双重类型的特性。

```
device detection
 → feature model 
 → feature logic(arg: deviceType) 
```

Enter fullscreen mode Exit fullscreen mode

一个月后，业务人员来了，说“我们想要这个功能的中等规格版本”。开发商现在可以:

将功能版本选择的逻辑与运行功能的逻辑分开

**b)** 添加更多 if 语句

如果他们选择 A，系统会更健壮，但企业会问为什么要花这么长时间。如果他们选择 B，他们将增加更多的技术债务，当该领域的任何其他事情发生变化时，他们就会焦头烂额，但企业会对周转时间感到满意。

你可能会说，如果开发者真的是一个好的开发者，他们应该已经看到了这一点。好的。如果他们开发了 100 个功能呢？可接受的误差率是多少？当你可以把这个问题融入到结构中时，为什么还要增加更多问题预测的认知开销呢？

在这种情况下，如果没有规定文件结构，就无法防止将来的更改。

#### 关注规范性文件结构

同样，开发人员的任务是根据设备规格构建一个精简和完整版本的特性。开发人员查看系统->域|表示的文件结构，并可以相应地组织逻辑:

```
device detection
 → feature version functionalities - type[full, slim]
 → feature presentation(type[index]) 
```

Enter fullscreen mode Exit fullscreen mode

这里，该结构已经强制区分了运行选定的逻辑和定义要运行的可选业务逻辑。

然后，当企业要求中等版本的功能时，解决方案很容易实现。

```
device detection
 → feature version functionalities - type[full, slim, medium]
 → feature presentation(type[index]) 
```

Enter fullscreen mode Exit fullscreen mode

#### 这对我有什么帮助？

我们很容易忽略环境对我们工作效率的所有影响，我说的不是开放式办公室😒我说的是我们工作的代码库。

想想你花了太多时间构建一个特性的心理环境，只是为了添加一个小的调整或修复一个 bug。或者当企业问为什么事情要花这么长时间，你用量子力学的论文来回答，只是为了建立一个描述一切有多糟糕的起点。

一个组织良好的代码库可以减轻这种痛苦。

设计和构建软件不仅仅是以一种有意义的方式构建事物，它也是以一种将开发者置于更强的位置来处理接下来的事情的方式来构建事物。