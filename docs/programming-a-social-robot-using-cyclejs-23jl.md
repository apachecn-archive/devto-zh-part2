# 使用 Cycle.js 为社交机器人编程

> 原文：<https://dev.to/mjyc/programming-a-social-robot-using-cyclejs-23jl>

*原载于[媒体](https://medium.com/@chungjy9/programming-a-social-robot-using-cycle-js-95f30a0128ce)T3】*

> ***注:**查看其他关于使用 Cycle.js 编写社交机器人的帖子太:*
> T5】1 .[使用 Cycle.js](https://dev.to/mjyc/programming-a-social-robot-using-cyclejs-23jl)
> *2 编程一个社交机器人。[在 Cycle.js 中实现有限状态机](https://dev.to/mjyc/implementing-a-finite-state-machine-in-cyclejs-1e63)*

在这篇文章中，我将向你展示如何使用 [Cycle.js](https://cycle.js.org/) 编写一个社交机器人。我假设您熟悉反应式编程。如果你不是，看看[你错过的反应式编程介绍](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)。如果你急于动手，跳到[实施“旅行个性测试”](#implementing-travel-personality-test)部分。

## 什么是社交机器人？

[维基百科](https://en.wikipedia.org/wiki/Social_robot)将其介绍为:

> 社交机器人是一种自主机器人，它通过遵循社会行为和附属于其角色的规则，与人类或其他自主物理代理进行交互和通信。

社交机器人之母辛西娅·布雷泽尔曾经说过:

> 简而言之，可社交机器人以类似人类的方式具有社交智能，与它互动就像与另一个人互动。在成就的顶峰，他们可以和我们交朋友，就像我们可以和他们交朋友一样。

我将社交机器人视为具体化的代理，其主要任务是与人类交流以帮助人类。所以，用于[教育](http://robotic.media.mit.edu/portfolio/storytelling-companion/)或[老年人护理](http://www.cataliahealth.com/)的互动机器人最符合我的定义。

编写社交机器人类似于编写 web 应用程序。在这两种情况下，程序员编写代码来处理输入，例如按钮点击或传感器读数，并相应地输出数据，例如在屏幕上显示信息或向电机发送控制信号。主要的区别在于，编程社交机器人涉及到多模态输入和输出，例如语音和动作，以与人类互动，而不是仅仅使用屏幕界面。

在这篇文章中，我将使用一个[平板脸机器人](https://github.com/mjyc/tablet-robot-face)进行演示。平板电脑脸机器人只是一个在平板电脑上运行的网络应用程序，但我们将让它说话，听你说话，看你，使它更像一个“社交机器人”。

## 什么是 Cycle.js？

[Cycle.js](http://cycle.js.org) 是一个功能性和反应性的 JavaScript 框架。它是一种抽象，将所有产生副作用的代码分离成驱动程序，所以核心应用程序逻辑代码在一个“主”函数中保持纯粹。Cycle.js 的作者将 web 应用程序描述为人类和计算机之间的对话。如果我们假设两者都是函数，人是`y = driver(x)`而计算机是`x = main(y)`，其中`x`和`y`是[反应式编程](https://cycle.js.org/streams.html#streams-reactive-programming)上下文中的流，那么对话就是两个函数通过它们的输入流相互反应，输入流是另一个函数的输出。

## 为什么社交机器人要用 Cycle.js？

对我来说，Cycle.js 本质上加强了功能反应式编程，例如，使用流和[端口和适配器架构](http://wiki.c2.com/?PortsAndAdaptersArchitecture)，例如，分离副作用，以使创建和理解复杂和并发的交互式程序变得容易——超越 web 应用。这也是我选择 Cycle.js 为社交机器人编程的原因。我相信 Cycle.js 实施的模式将帮助程序员解决源于支持多模态交互的并发问题，并在所需机器人行为的复杂性增加时保持控制。事实上，如果您可以自己实施这些模式，就不需要使用 Cycle.js。例如，你可以使用带有 reactimate 、 [Flapjax](http://www.flapjax-lang.org/) 或[react vex](http://reactivex.io/)流库之一的 [Yampa，用你的机器人的 API 可用的语言来做这件事。](https://wiki.haskell.org/Yampa/reactimate)

## 实施“旅行个性测试”

足够的背景，我们现在将创建一个机器人程序来测试你的旅行个性。具体来说，我们将制造机器人

1.  当你和机器人互动时，看着你
2.  如流程图中的[所示提问。](http://www.nomadwallet.com/afford-travel-quiz-personality/)

如果你好奇，可以在 Stackblitz 查看完整的代码和演示。

**重要！！**目前，我们在这篇文章和 Stackblitz 演示中使用的[自行车-机器人-驾驶员/跑步](https://github.com/mjyc/cycle-robot-drivers/blob/master/run)包只能在 Chrome 浏览器上运行(> = 65.0.3325.181)。

本文中的代码示例假设您熟悉 JavaScript ES6 。为了构建代码，我在这里使用了 [browserify](http://browserify.org/) 和 [Babel](https://babeljs.io/) ，但是你也可以随意使用你喜欢的构建工具和编译器。如果您不熟悉它们，只需分叉[stack blitz 演示代码](https://stackblitz.com/edit/cycle-robot-drivers-tutorials-01-personality-quiz)并开始编码！

让我们建立一个 Cycle.js 应用程序。创建文件夹:

```
mkdir my-robot-program
cd my-robot-program 
```

Enter fullscreen mode Exit fullscreen mode

然后下载 [`package.json`](https://github.com/mjyc/cycle-robot-drivers/blob/master/examples/tutorials/01_personality_quiz/package.json) 、 [`.babelrc`](https://github.com/mjyc/cycle-robot-drivers/blob/master/examples/tutorials/01_personality_quiz/.babelrc) 、 [`index.html`](https://github.com/mjyc/cycle-robot-drivers/blob/master/examples/tutorials/01_personality_quiz/index.html) 并在文件夹中创建一个空的`index.js`文件。运行`npm install`来安装所需的 npm 软件包。安装后，您可以运行`npm start`来构建和启动 web 应用程序，它什么也不做。

现在在`index.js` :
中添加以下代码

```
import xs from 'xstream';
import {runRobotProgram} from '@cycle-robot-drivers/run';

function main(sources) { }

runRobotProgram(main); 
```

Enter fullscreen mode Exit fullscreen mode

然后运行该应用程序，例如通过运行`npm start`。它会在你的浏览器上加载一张机器人的脸。

我们刚刚成功设置并运行了一个 Cycle.js 应用程序！

### 机器人，看脸！

我们现在将关注于实现第一个特性——看脸。

让我们通过在`main` :
中添加以下代码，让机器人移动它的眼睛

```
// ...

// "sources" is a Cycle.js term for the input of "main" / the output of "drivers"
function main(sources) {
  // "const" (and "let") is a javascript ES6 feature
  const sinks = {
    TabletFace: xs.periodic(1000).map(i => ({
        x: i % 2 === 0 ? 0 : 1,  // horizontal left or right
        y: 0.5  // vertical center
      })).map(position => ({
        type: 'SET_STATE',
        value: {
          leftEye: position,
          rightEye: position
        }
      }))
  };
  // "sinks" is a Cycle.js term for the output of "main" / the input of "drivers"
  return sinks;
}

// ... 
```

Enter fullscreen mode Exit fullscreen mode

这里我们通过从`main`返回`sink.TabletFace`流向`TabletFace`驱动程序发送命令。 [`periodic`](https://github.com/staltz/xstream#periodic) xstream 工厂创建每秒发出一个递增数字的流，而 [`map`](https://github.com/staltz/xstream#map) xstream 操作符创建一个将发出的数字转换成位置的新流和另一个将发出的位置转换成控制命令的新流。如果运行更新后的应用程序，机器人应该会反复左顾右盼。

现在让我们通过在`main` :
中添加更多代码来检测人脸

```
// ...

function main(sources) {
  sources.PoseDetection.poses.addListener({
    next: (poses) => console.log('poses', poses)
  });

  // ...
}

// ... 
```

Enter fullscreen mode Exit fullscreen mode

这里我们使用 [addListener](https://github.com/staltz/xstream#addListener) xstream 操作符来添加一个回调函数，该函数将检测到的姿态数据打印到`poses`流，即从`PoseDetection`驱动程序返回的流。

当您运行应用程序时，您应该看到打印到浏览器控制台的对象数组。如果你看不到他们，请确保你对摄像机可见，并通过位于机器人面部下方的姿态可视化器被检测到(尝试向下滚动)。每个数组代表当前时刻检测到的姿态，其格式如下:

```
const poses = [
  // the first detected person
  {
    "score": 0.32371445304906,
    "keypoints": [
      {
        "part": "nose",
        "position": {
          "x": 253.36747741699,
          "y": 76.291801452637
        },
        "score": 0.99539834260941
      },
      {
        "part": "leftEye",
        "position": {
          "x": 253.54365539551,
          "y": 71.10383605957
        },
        "score": 0.98781454563141
      },
      // ...
  },
  // the second detected person if there is one
  {
    "score": 0.22838506316132706,
    "keypoints": [
      {
        "part": "nose",
        "position": {
          "x": 236.58547523373466,
          "y": 360.03672892252604
        },
        "score": 0.9979155659675598
      },
      // ...
    ]
  },
  // ...
] 
```

Enter fullscreen mode Exit fullscreen mode

当应用程序运行时，尝试从摄像机中消失。
你应该会看到`poses`数组中少了一个对象。也可以试着通过向左或向右转头来隐藏你的一只耳朵。您应该不会看到在`keypoints`数组中的`part`字段中有一个字符串`nose`的对象。

现在我们知道了如何移动机器人的眼睛和检索检测到的面部数据，让我们把它们放在一起，让机器人看一张脸。具体来说，我们将使机器人的眼睛跟随被检测人的鼻子。更新`main`如下:

```
// ...

function main(sources) {
  const sinks = {
    TabletFace: sources.PoseDetection.poses
      .filter(poses =>
        // must see one person
        poses.length === 1
        // must see the nose
        && poses[0].keypoints.filter(kpt => kpt.part === 'nose').length === 1
      ).map(poses => {
        const nose = poses[0].keypoints.filter(kpt => kpt.part === 'nose')[0];
        return {
          x: nose.position.x / 640,  // max value of position.x is 640
          y: nose.position.y / 480  // max value of position.y is 480
        };
      }).map(position => ({
        type: 'SET_STATE',
        value: {
          leftEye: position,
          rightEye: position
        }
      }))
  };
  return sinks;
}

// ... 
```

Enter fullscreen mode Exit fullscreen mode

这里我们通过使用从`PoseDetection`驱动程序(`sources.PoseDetection.poses`)的输出流创建的流向`TabletDriver`发送命令。
为了将姿态数据转换成控制命令，我们使用 [`filter`](https://github.com/staltz/xstream#filter) xstream 操作符将姿态数据过滤成只包含一个鼻子可见的人的姿态数据。然后我们使用 [`map`](https://github.com/staltz/xstream#map) xstream 运算符两次将检测到的鼻子位置转换为眼睛位置，并将眼睛位置转换为控制命令。

我们已经让机器人看脸了！

*锻炼思路:*

*   让机器人看你的一只手而不是你的鼻子？
*   当你把目光从摄像机移开时，让机器人微笑( [`happy`表情](https://github.com/mjyc/cycle-robot-drivers/blob/master/screen))？

#### 仔细查看`runRobotProgram`

在阅读上面的代码示例时，您可能想知道:

1.  何时何地创建了`TabletFace`驱动程序
2.  驱动程序如何以及何时产生副作用

下面是第一个问题的答案:我们在示例代码中使用的两个驱动程序`TabletFace`和`PoseDetection`，是在`runRobotProgram`中创建的。
通常当你编写一个 Cycle.js app 时，你需要[显式地](https://cycle.js.org/getting-started.html#getting-started-coding-create-main-and-drivers)创建驱动，并把它们传递给 [Cycle.js `run`](https://cycle.js.org/api/run.html) 函数。我们跳过了这一步，因为我们使用了`runRobotProgram`,它创建了为平板电脑机器人编程所需的驱动程序，并为我们调用了 Cycle.js `run`。`runRobotProgram`函数是[cycle . js`run`](https://github.com/mjyc/cycle-robot-drivers/blob/master/run/src/index.tsx)的包装函数，它

1.  创建五个驾驶员，`AudioPlayer`、`SpeechSynthesis`、`SpeechRecognition`、`TabletFace`、`PoseDetection`
2.  创建并设置五个动作组件`FacialExpressionAction`、`AudioPlayerAction`、`TwoSpeechbubblesAction`、`SpeechSynthesisAction`、`SpeechRecognitionAction`，以允许程序员将它们用作驱动程序
3.  调用 Cycle.js 运行创建的驱动程序和操作。

事实上，如果你习惯使用 Cycle.js，你可以使用 Cycle.js `run`而不是`runRobotProgram`来对驱动和动作进行更多的控制。你也可以创建一个新的`runRobotProgram`功能，为你自己的机器人提供驱动程序，而不是平板电脑机器人！

关于第二个问题，请查看 Cycle.js 网站的[这一页](https://cycle.js.org/drivers.html)。

### 机器人，提问！

我们现在将重点放在实现第二个功能上——询问旅行个性测验问题。

首先，为了方便起见，我们将测验流程图表示为字典中的字典。添加以下代码:

```
// ...
import {runRobotProgram} from '@cycle-robot-drivers/run';

const Question = {
  CAREER: 'Is reaching your full career potential important to you?',
  ONLINE: 'Can you see yourself working online?',
  FAMILY: 'Do you have to be near my family/friends/pets?',
  TRIPS: 'Do you think short trips are awesome?',
  HOME: 'Do you want to have a home and nice things?',
  ROUTINE: 'Do you think a routine gives your life structure?',
  JOB: 'Do you need a secure job and a stable income?',
  VACATIONER: 'You are a vacationer!',
  EXPAT: 'You are an expat!',
  NOMAD: 'You are a nomad!'
};

const Response = {
  YES: 'yes',
  NO: 'no'
};

const transitionTable = {
  [Question.CAREER]: {
    [Response.YES]: Question.ONLINE,
    [Response.NO]: Question.FAMILY,
  },
  [Question.ONLINE]: {
    [Response.YES]: Question.NOMAD,
    [Response.NO]: Question.VACATIONER,
  },
  [Question.FAMILY]: {
    [Response.YES]: Question.VACATIONER,
    [Response.NO]: Question.TRIPS,
  },
  [Question.TRIPS]: {
    [Response.YES]: Question.VACATIONER,
    [Response.NO]: Question.HOME,
  },
  [Question.HOME]: {
    [Response.YES]: Question.EXPAT,
    [Response.NO]: Question.ROUTINE,
  },
  [Question.ROUTINE]: {
    [Response.YES]: Question.EXPAT,
    [Response.NO]: Question.JOB,
  },
  [Question.JOB]: {
    [Response.YES]: Question.ONLINE,
    [Response.NO]: Question.NOMAD,
  }
};

function main(sources) {
// ... 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我修改了测验问题，将所有回答选项更改为“是”和“否”。

现在让机器人提问并记录你的口头回答。
首先，我们将让机器人在开始时只说第一个问题，即加载机器人的面部，并在说了一些话后开始听:

```
// ...
function main(sources) {
  sources.SpeechRecognitionAction.result.addListener({
    next: (result) => console.log('result', result)
  });
  // ...
  const sinks = {
    TabletFace: sources.PoseDetection.poses
      .filter(poses =>
      // ...
    SpeechSynthesisAction: sources.TabletFace.load.mapTo(Question.CAREER),
    SpeechRecognitionAction: sources.SpeechSynthesisAction.result.mapTo({})
  };
  return sinks;
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们通过从`main`经由`sink.SpeechSynthesisAction`和`sink.SpeechRecognitionAction`返回创建的流，向`SpeechSynthesisAction`驱动程序和`SpeechRecognitionAction`驱动程序发送命令。
驱动程序`SpeechSynthesisAction`的输入流在`sources.TabletFace.load`流中发出的 tablet-face-loaded 事件上发出`Question.Career`。
在完成`sources.SpeechSynthesisAction.result`流中发出的语音合成动作事件时，`SpeechRecognitionAction`驱动程序的输入流发出一个空对象(`{}`)。
两个流都是使用 [`mapTo`](https://github.com/staltz/xstream#mapTo) xstream 操作符创建的。
我们还使用 [addListener](https://github.com/staltz/xstream#addListener) xstream 操作符打印出在`sources.SpeechRecognitionAction.result`流中发出的事件。

当你运行这个应用程序时，你应该会听到机器人说“充分发挥你的职业潜力对你来说重要吗？”并查看打印到浏览器控制台的`SpeechRecognitionAction`的输出。输出具有以下格式:

```
const result = {
  "result": "yes",  // transcribed texts
  "status": {
    "goal_id": {  // a unique id for the executed action
      "stamp": "Mon Oct 01 2018 21:49:00 GMT-0700 (PDT)",  // "Date" object
      "id": "h0fogq2x0zo-1538455335646"
    },
    "status": "SUCCEEDED"  // "SUCCEEDED", "PREEMPTED", or "ABORTED"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

试着说点什么，看看它听得有多清楚。

现在我们想改进程序，让机器人问不止一个问题。例如，每当机器人听到一个合适的答案，即“是”或“否”，我们可以尝试将问题作为命令发送给`SpeechSynthesisAction`司机。让我们通过更新上面的代码来表达这一点，如下:

```
// ...
function main(sources) {
  // ...
  const sinks = {
    TabletFace: sources.PoseDetection.poses
      .filter(poses =>
      // ...
    SpeechSynthesisAction: xs.merge(
      sources.TabletFace.load.mapTo(Question.CAREER),
      sources.SpeechRecognitionAction.result.filter(result =>
        result.status.status === 'SUCCEEDED'  // must succeed
        && (result.result === 'yes' || result.result === 'no') // only yes or no
      ).map(result => result.result).map(result => {
        // Hmm...
      })
    ),
    SpeechRecognitionAction: sources.SpeechSynthesisAction.result.mapTo({})
  };
  return sinks;
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们使用 [`merge`](https://github.com/staltz/xstream#merge) xstream 工厂，合并发出第一个问题(`sources.TabletFace.load.mapTo(Question.CAREER)`)的流中的命令和听到“是”或“否”(`sources.SpeechRecognitionAction.result.filter(// ...`)时发出后续问题的流中的命令。

这种方法有一个问题。我们无法确定在第二个流中返回哪个问题，因为这个问题依赖于机器人问的最后一个问题，而机器人问的最后一个问题又依赖于最后一个问题，依此类推。换句话说，我们需要我们正在创建的当前流的先前输出，作为当前流的输入。

为了解决这个循环依赖问题，我们采用代理模式，更新`main`函数如下:

```
// ...
function main(sources) {
  // ...
  const lastQuestion$ = xs.create();
  const question$ = xs.merge(
    sources.TabletFace.load.mapTo(Question.CAREER),
    sources.SpeechRecognitionAction.result.filter(result =>
      result.status.status === 'SUCCEEDED'  // must succeed
      && (result.result === 'yes' || result.result === 'no') // only yes or no
    ).map(result => result.result)
    .startWith('')
    .compose(sampleCombine(
      lastQuestion$
    )).map(([response, question]) => {
      return transitionTable[question][response];
    })
  );
  lastQuestion$.imitate(question$);

  const sinks = {
    TabletFace: sources.PoseDetection.poses
      .filter(poses =>
      // ...
    SpeechSynthesisAction: question$,
    SpeechRecognitionAction: sources.SpeechSynthesisAction.result.mapTo({})
  };
  return sinks;
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们已经将为`sink.SpeechSynthesisAction`创建流的代码移到了`sink`对象定义之外。我们使用 [`create`](https://github.com/staltz/xstream#create) xstream 工厂创建一个空的代理流`lastQuestion$`，并在创建`question$`流时使用它。
然后使用 [`imitate`](https://github.com/staltz/xstream#imitate) xstream 操作符将代理流`lastQuestion$`连接到其源流`question$`。我们还使用 [`compose`](https://github.com/staltz/xstream#compose) 和 [`sampleCombine`](https://github.com/staltz/xstream/blob/master/EXTRA_DOCS.md#sampleCombine) xstream 操作符来组合来自`sources.SpeechRecognitionAction.result`流和`lastQuestion$`流的事件。请注意，我在流变量名的末尾添加了`$`，以便与 Cycle.js 作者所做的其他变量区分开来。尝试更新的应用程序，看看机器人是否会问一个以上的问题，如果你用“是”或“否”来回答它。

你可能想知道我们什么时候更新了代码，在*所有*问题之后发送“开始聆听”命令({})。我们没有更新代码；我们之前的代码已经按预期工作了，因为`sources.SpeechSynthesisAction.result`流在完成*每个*合成语音时发出数据。

你可能遇到的一个问题是，当机器人听到一个不是“是”或“不是”的答案时，例如，由于失误，它没有提出下一个问题。在这种情况下，机器人应该重新开始听，给这个人一个改正答案的机会。让我们更新代码来修复这个问题:

```
// ...
    SpeechSynthesisAction: question$,
    SpeechRecognitionAction: xs.merge(
      sources.SpeechSynthesisAction.result,
      sources.SpeechRecognitionAction.result.filter(result =>
        result.status.status !== 'SUCCEEDED'
        || (result.result !== 'yes' && result.result !== 'no')
      )
    ).mapTo({})
  };
  return sinks;
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

运行更新的应用程序。你应该看到机器人会继续监听并把它听到的内容打印到控制台上，直到它听到“是”或“否”，然后再问下一个问题。

我们到此为止。尝试参加旅行个性测验，找出你的旅行个性，尽情享受吧！

*锻炼思路:*

*   实现[“有史以来最重要的 24 个流程图”](https://www.buzzfeed.com/lukelewis/the-most-important-flowcharts-of-all-time)中的一个，让机器人回答人生中最大的问题之一？
*   让你的机器人在某个 Twitter 用户发布推文时阅读该用户的推文，例如使用 Twitter API ？
*   让你的机器人在股票价格低于或高于某个阈值时提醒你？

如果有什么不清楚的地方，请告诉我，我很乐意和你谈谈你的顾虑。感谢您的阅读！

## 杂项

*   有趣的事实:[现在很多社交机器人都用屏幕当脸](https://spectrum.ieee.org/automaton/robotics/humanoids/what-people-see-in-157-robot-faces)。
*   查看 [RxJS Marbles](http://rxmarbles.com/#mergeMap) 用 Marbles 图可视化流操作符，例如[间隔](http://rxmarbles.com/#interval)(xstream 中的周期)[映射](http://rxmarbles.com/#map)、[过滤](http://rxmarbles.com/#filter)、[映射到](http://rxmarbles.com/#mapTo)、[合并](http://rxmarbles.com/#merge)。
*   如果你是一个 [ROS](http://www.ros.org/) 用户，可以看看我的 [experimental Cycle.js 驱动](https://github.com/mjyc/cycle-ros-example)使用 [roslibjs](https://github.com/RobotWebTools/roslibjs) 与 ROS 通信。
*   通过参加[这个简短的调查](https://goo.gl/forms/rdnvgk8rWrUmbtrt1)，帮助我改进[自行车机器人驾驶员](//./)库！

我的名字是麦克·钟。我是一名对人机交互和机器学习领域感兴趣的研究生。你可以通过[推特](https://twitter.com/mjyc_)和 [GitHub](https://github.com/mjyc) 联系我。