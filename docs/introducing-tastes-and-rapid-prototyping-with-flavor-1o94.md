# 引入口味和快速原型制作

> 原文：<https://dev.to/awfulaxolotl/introducing-tastes-and-rapid-prototyping-with-flavor-1o94>

[![face boxes](../Images/0699933ad2805f53bc9b0df739908ae5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Rqqzh0X_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://axolotl.industries/static/face-boxes-d153b623f4faf77f7cf365268f9c6078-fefd9.png)

我制作了一个类型脚本库，用于在创意原型制作过程中智能生成样本。这叫品味。

在 GitHub 上看看吧！

# 快速咬一口

假设我们正在创建一个最小的海报。不——一张巨大的极简海报，因为我们有话要说。

让我们用品味来快速检验字体大小和颜色的不同组合。

下面的代码也可用于 Runkit 上的交互性[。](https://runkit.com/awfulaxolotl/a-quick-bite-of-tastes) 

```
// 1\. Define the variables we want to play with
import { integer, record } from 'tastes'

const hue = integer({ min: 0, max: 360 })
const poster = record({
  fgHue: hue,
  bgHue: hue,
  headerPt: integer({ min: 20, max: 32 }),
  bodyPt: integer({ min: 12, max: 20 }),
})

// 2\. We can checkout the specific poster design at
// `(0.4, 0.2, 0.75, 0)` in the sample space.
console.log(poster([0.4, 0.2, 0.75, 0]))

// 3\. But that's too manual. Let's just ask for 30 random
// sample poster designs.
import { sampleRandom, take } from 'tastes'

for (const s of take(30, sampleRandom(poster))) {
  console.log(s)
}

// 4\. But random designs may not be the best examples.
// Let's check out carefully selected "representative"
// samples instead.
import { sampleBatch } from 'tastes'

// Use detail of order 3
for (const s of sampleBatch(poster, 3)) {
  // `console.log` is used in abscence of
  // a proper rendering function
  console.log(s)
} 
```

Enter fullscreen mode Exit fullscreen mode

# 谦逊的假设

已经存在一批令人高兴的 Javascript 随机数据生成器。如 [faker](https://github.com/marak/Faker.js/) 、 [casual](https://github.com/boo1ean/casual) 、 [chance](http://chancejs.com/) —加上 [jsverify](https://github.com/jsverify/jsverify) 进行基于属性的测试。

那么，为什么还要有另一个数据生成库呢？

嗯，我想要一些东西来促进创造性的原型制作。我厌倦了调整不同的变量和设置来得到我想要的。所以让我们向我们的电脑求助吧！

随机数据生成器不适合这个。

您可以试验您的代码如何对随机数据做出反应，但这将是一个漫无目的的追求。这相当于视频游戏控制器上的按钮敲击。你会赢吗？也许吧。但是你知道为什么吗？不要！

这就是品味的来源。这是创造性原型制作中新方法的更有意的基础。假设是，如果最大限度地使用示例驱动的原型，将会带来巨大的生产力收益。

[![faces conversation](../Images/883116af2c5cc9d8ae3f1a49ed455ac8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i5T1g7my--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://axolotl.industries/static/faces-conversation-b44579f3d34c4f2d282330f5e3ff71dc-fefd9.png)

# 计划

Tastes 目前只不过是数据生成功能的分类。不完全是无用的，但也不完全是令人兴奋的。

这个想法是为了给一些漂亮的 rad 开发人员工具提供一个完整的基础。想象一个可组合的工具包，la [D3](https://d3js.org) ，但是用于生成原型。

下面是管道中的大致情况:

*   样本空间交互探索组件。
*   即插即用，使组件与正确类型反应。
*   从 Typescript 函数自动生成单元测试用例。
*   自动生成 React 故事书的无限示例。
*   更丰富的预制样本库。这可以包括地理空间类型、颜色、lorem ipsum、形状、数据结构、时间序列等等。
*   挑选样本空间探索。

# 一个生财的新世界

不要犯错误；品味不是凭空产生的。

这是不断发展的高级软件接口领域的一部分。“高层次”是抽象思维，“低层次”是关注细节和本质的思维。

随着时间的推移，人机界面已经逐步提升到更高的水平。我们从大型机开始，用户需要在穿孔卡中输入低级代码。现在我们可以用语音索要 gif 了。

进步！

 <video src="https://zippy.gfycat.com/ActualBelatedAmbushbug.mp4" loop="">等等，但是等一下。事实证明，进步并不是普遍的。

自从 1968 年[所有演示之母](https://www.youtube.com/watch?v=M5PgQS3ZBWA)以来，创意工具一直没有根本改变。当然，我们现在使用鼠标和图形而不是命令行，但是我们的表达水平没有改变。我们仍然在 Photoshop 中操作像素，在 Word 中操作字母，在 Ableton 中操作笔记。

好吧，像素之类的东西不会消失。当然不是——它们是各自媒体的组成部分。没有字母的小说不是小说，没有像素的图片不是图片。

但是我们人类不会用字母和像素来思考。我们用叙述、隐喻、情感、瞬间和信息来表达自己。

软件应该帮助我们在那个层面上工作。如果我们可以在个人资料图片中简单地要求一条龙，谁会关心像素调整？在一个你可以要求你的文章有无限种有效替代版本的世界里，谁会担心拼写呢？

[![dragon love](../Images/6043fa366c91ab46a000da5e80636f47.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4ZhdAq1S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://axolotl.industries/static/dragon-love-b4a5a88b041493f0f062b4c695c7536d-3dd2a.jpg)

我们就要到了。下面是几个例子:

*   [**项目追梦人。**](https://autodeskresearch.com/projects/dreamcatcher) 从约束条件中生成工业品外观设计零件。
*   [**洋红色。**](https://magenta.tensorflow.org/) 从样品和风格中创造音乐和艺术。
*   [**网格。**](https://thegrid.io/) 从内容中衍生网站。
*   [**雷尼。**](https://jon.gold/2016/06/declarative-design-tools/) 交互地探索可能的设计组合。

事实上，口味可以被认为是 René的进化。它概括了更多数据类型的概念，并将其集成到软件开发工作流中。

# 好吧，那么

一有机会就去品尝一下吧！

这只是一个 NPM 安装了:

```
npm install tastes --save-dev
# or `yarn add tastes --dev` 
```

Enter fullscreen mode Exit fullscreen mode</video>