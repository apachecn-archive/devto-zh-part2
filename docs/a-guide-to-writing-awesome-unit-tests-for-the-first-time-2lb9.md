# 首次编写令人敬畏的单元测试指南

> 原文：<https://dev.to/napoleon039/a-guide-to-writing-awesome-unit-tests-for-the-first-time-2lb9>

### 简介:

这是一个旨在让你开始测试的教程，尤其是单元测试。在本教程中，我们将学习如何为 Vue.js 应用程序设置单元测试。有许多可用的测试框架，我相信您一定听说过其中的许多。我们将使用 Jest 框架进行测试。我们将使用 vue-test-utils，这将使设置测试更加容易。还将使用其他一些实用程序，但是在这个阶段不必太担心它们。

### 你会学到什么:

在本教程结束时，您将会学到为 Vue 应用程序设置单元测试的实用知识。虽然我们将设置的测试是基本的，但是我还将告诉您如何查找文档和设置更复杂的测试。您将能够立即利用在本教程中学到的这些技能。让我们开始吧。

### 先决条件:

因为您正在为 Vue 应用程序设置测试，所以希望您对 Vue.js 框架有所了解。除了测试本身，我们还将对 package.json 和。babelrc 文件。你不需要知道关于这些文件的一切，以及我们将要在其中写入的内容。仅仅知道它做什么就足够了。

### 创建 Vue 项目:

首先，我们将使用 webpack 模板创建一个简单的 Vue 项目。虽然 Vue 3.0 版引入了另一种创建项目的方法，而不是使用模板，但是因为我经常使用模板，所以在本教程中我也会这样做。用这两种方式创建的项目没有太大的区别。
[![Options selected during project creation](../Images/eb31a13e33a2f5ee091e9f85145fa29c.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--F_He1zSp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f5awilwecnwmml6622xv.PNG)

相应地随意选择这些值——项目名称、描述、作者。我更喜欢为我的项目使用独立(运行时+编译器)版本，你可以选择任何一个。这是一个示例项目，是用来教你测试的，所以不需要路由器。如果需要，您可以选择一个 linter，但是不要设置单元测试和端到端测试选项。

如果不清楚选择哪个选项，你可以参考上面的图片，随意做一些你认为合适的必要修改。但是，不要设置测试，因为我们将设置我们自己的测试。由于这不是一个 Vue 教程，您需要自己执行项目安装的后续步骤。

现在，这是一个我们将要测试的简单模板。如果需要，继续对文本进行修改，但是保持大纲不变(两个按钮——一个改变数据属性，另一个调用函数)。

app . view:[![App.vue component](../Images/ba7013faa89e2b899fe75a4a62478441.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MOC1_ilC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/svpvlv4aknrh9ee8bgpz.PNG)

测试.视图: [![Test.vue component](../Images/c5171d43b63333dee81c54aaeebdbd53.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6ZkG3w-B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jiz8uppcciitpxrb3dm6.PNG)

### 安装测试依赖项:

既然我们已经启动并运行了我们的项目，那么是时候安装我们设置单元测试所需的所有实用程序和软件包了。

[![Installing packages using npm on command line](../Images/c6d45fb182d5be5015c669e4e53bb36d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XHJsDN9Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3qtvz1ffnm2dwwypctwm.PNG)

我们正在安装的这些新软件包的简单说明:

1.  vue-jest:我们将在一个 JavaScript 文件中设置我们的测试。这个包将把我们的 Vue 文件解析/编译成简单的 JavaScript，使之易于使用。
2.  babel-jest:这个包帮助我们处理一些前沿语法。如果我们使用的语法和特性没有得到广泛的支持，需要编译，这个包可以让 Jest 和 Babel 互相兼容。
3.  jest:这是用于实际 Jest 设置的包
4.  @vue/test-utils:这个包是创建组件实例所必需的。

不要忘记使用- save-dev 标志。这很关键。这是因为所有这些包应该只处于开发模式，而不是生产模式，因为用户不需要它们。如果我们从生产中排除这些包，我们可以使项目更轻松。

现在对我前面提到的配置文件进行修改。这些更改将对 package.json 和。Vue.js 在我们创建项目时自动为我们创建的 babelrc 文件。json 包含了我们的项目和。babelrc 是 Babel 所需的配置文件。Babel 是一个了不起的东西，它可以翻译我们新的 JavaScript 代码，以便旧的浏览器能够理解它。

package . JSON:
[![image of all changes made to package.json file](../Images/42b2d618455b8fadbf7ee2ea3e356a3c.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--kVMrkHfw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w34amfrfsqrmwjf0sc0x.PNG)

。babelrc 文件:
[![image of changes made to .babelrc file](../Images/74c5341f7ea4595805e854ac97701fc8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jG2dkf7R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uwp6dr5osdy4mtmgi9lm.PNG)

所有更改都在注释中进行了解释。由于我们的目标是快速开始编写测试，所以我不会对这些变化进行过多的描述。

### 写作测试:

项目已经创建，所有依赖项都已成功安装，所有设置都已完成。现在我们已经准备好开始编写一些令人敬畏的测试了！！

在我们的 Vue.js 应用程序中，有一个数据属性，通过单击一个按钮进行切换，然后另一个按钮调用一个函数，将一条简单的消息打印到控制台。我们将在测试中点击按钮(多棒啊！)，检查是否切换了数据属性，然后检查是否调用了该函数。

在 Jest 中，测试的工作方式是我们可以运行一些代码，然后告诉 Jest 我们“期望”一些输出/结果，并要求它将我们的期望与实际结果进行比较。我们可能期望一个值被改变或者一个函数被调用，或者甚至期望一个函数的结果。我们可以要求 Jest 检查任何东西(当然是在一定的范围内；)).让我们编写第一个测试:

在根目录下创建一个名为 tests 的新文件夹。如果你不熟悉根目录这个术语(我知道它一开始让我很困惑)，你的文件夹应该是这样的:

[![image of folder structure after creating tests folder](../Images/3a7156421df663e541b55a999176332e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GYCPDpsh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0r9lx6e2d9no9j03fr4o.PNG)

您猜对了，这个目录将包含您所有的测试。您的测试文件与您将要测试的文件具有相同的名称是很重要的。它需要有**。测试中间的**分机。例如，如果你正在为 **index.js** 编写测试，你的文件名将是 **index.test.js** 。如果您正在测试 **HelloWorld.vue** ，测试文件的名称将是 **HelloWorld.test.js** 。我们的文件将是一个常规的 JavaScript 文件，因此称为**。js** 扩展。由于我们将为我们的 **Test.vue** 组件编写测试，我们将适当地命名该文件( **Test.test.js** )。就像我上面提到的，Jest 通过比较实际结果和我们的预期来测试我们的代码。但是，它只允许我们写一个测试。

不用担心，我们可以写更多的测试。测试包含在一个叫做**测试套件**的东西中。

> 您可以将测试套件想象成一盒油炸圈饼。它可以装很多甜甜圈，我们需要多少就有多少(先别吃，我们要先工作！).但问题是，每个甜甜圈只能有一种口味。它可以是巧克力或草莓，但你不能两者都要。所以，一个简单的解决方案——你可以在盒子里塞进两个甜甜圈:一个巧克力，另一个草莓。然后你可以有任何你想要的味道:)。

类似地，对于我们的测试，即使我们只能告诉 Jest 每个测试只期待一件事，我们也可以在一个测试套件中编写多个测试。一步一步来；我们将首先单击第一个按钮，并检查数据属性是否已更新。

test . test . js:
[![image of all imported packages and first test case](../Images/f77d5009b9ca26a46cf6d783e201e191.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--41DjpTsG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zomkwmt59wah5erdq40k.PNG)

大部分解释都写在评论里了。所以我会讲一些很难理解的问题:

**描述**的关键词就是我前面提到的测试套件(甜甜圈盒子)。它将包含测试用例。如何构建测试套件和测试用例取决于我们自己。对于这个例子，我们将只有一个测试套件，因为只有一个组件需要测试。我们将编写两个测试用例来测试我们拥有的两个按钮。 **it** 是用于编写单元测试的另一个关键字。它需要两个参数。第一个是一个字符串，它是测试用例的名称。名称通常应该是信息性的，比如如果测试是为了检查一个链接是否工作，那么像 test 1 这样的通用名称是不直观的。现在，第二个参数是一个包含测试逻辑的函数。在前面的生产线上，我们安装了我们的组件。我们现在可以自由地使用组件的实例了。测试的第一行设置了实例的数据属性(记住:组件的实例与原始组件完全相同，只是结构不同。我们要补充所有的数据和函数)。现在我们已经设置了数据属性，下一行使用它的 ID 找到与这个数据属性相关联的按钮。它在组件的模板中搜索这个按钮，然后模拟一次点击。现在应该切换数据属性。为了检查这一点，我们可以告诉 Jest，我们*期望*改变数据属性。我们测试用例的最后一行实现了这一点。我们**期望**数据属性*内容*是某个值。这是使用 Jest 提供的一种方法实现的- *toBe(我们期望的值)*。Jest 将运行测试，并检查它得到的结果与我们预期的结果。不要和我们在 expect 里面写的东西混淆- *wrapper* 是组件实例， *vm* 就像我们如何使用 **this** 关键字， *content* 是我们的数据属性。

现在让我们运行测试:

测试命令在
[![image of test command and test result](../Images/298b054a0549443d8e6cd38e850b8d3c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_qbz-y0Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/031eni44iid1eizxb5tj.PNG) 图中高亮显示

测试结果给了我们很多信息。当我们编写不止一个测试时，这将是很有帮助的。

干得好！你刚刚写了你的第一个测试。作为庆祝，去吃个甜甜圈吧:)

现在我们将编写下一个也是最后一个测试来检查第二个按钮是否成功调用了一个方法。

[![image of second test](../Images/d8cbed22c86c3756e9de9deff3cfd28c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yEb2zYP4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a0kaiiquei64sg77gc2k.PNG)

下面是两个测试用例:
[![image of both test cases together](../Images/7280627e9c1bd95480fe6334d1ae8657.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iGO41shk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8hemmm4gkze9tsew6wdg.PNG)

第一次测试时，您应该已经熟悉了大多数命令。我会解释新的。在这个测试案例中，我们将检查一个函数在点击按钮后是否被调用。我们必须做一些类似于第一个测试用例的事情，在这里我们设置一个数据属性。在这里，我们要设置一个方法。此时，我们并不真正关心我们的方法内部发生了什么，我们关心的只是它被调用。虽然我们可以创建一个空方法，但是最好模仿一个方法(创建一个假方法)。然后我们将这个假方法赋给我们实际方法的名字。这样做的是，当调用方法时，Jest 注意到了 **setMethods()** 函数并调用我们的模拟函数。之后，我们做我们之前做的——找到按钮，模拟一次点击，然后像我们期望的那样开玩笑。在这种情况下，我们期望函数被调用，所以我们使用一个不同于以前的 Jest 内置方法。

两次测试的结果:
[![image of result of both test cases](../Images/a8d93c75933ba03c7d81a05f00ccce54.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lOQ4q-aW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5ch4nvjue6ieewszqdjx.PNG)

### 测试失败怎么办？

我将花一点时间来解释当测试失败时会发生什么。现在在测试中做一些改变。就像我们期望数据属性是`false`的那一行，把它改成`true`。别担心，不会有太大的不同。现在用同样的命令再次运行测试。您应该得到这样的结果:

测试中做出的更改:
[![image of change done in the first test case](../Images/e8e7eb431b8d9ba054f8b42860a9bab8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jjst36A3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wtq096g5gvgrixerwpqw.PNG)

测试结果失败:
[![image of test failing](../Images/0384c16881ce3510dfffd9f09fabf88b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kOsG5-w_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x21u7ifbllhfcuxlpg1k.PNG)

您可能已经注意到，当所有测试都通过时，会有一条消息简单地告诉您。但是当一个测试失败时，所有这些代码行，到处都是红线。它好像对你指指点点，告诉你做错了什么。但是没必要害怕。这些红色警告是测试的重要组成部分。在前几行中，您可能会在我们第一个测试的名称旁边看到一个叉号。指示哪个测试有问题。在另外几行之后，它陈述了它所期望的和所接收的。这对我们很有帮助。我们一眼就能看出有什么不同。结果是`false`，我们已经告诉 Jest 期待结果为`true`。这还不是全部，接下来要做的是指出这条线，这样我们甚至不必去寻找问题。就是这样；那些吓人的红线不应该让你担心。相反，它们在找出问题所在和问题所在方面帮助很大。

这些只是一些基本的测试。你如何着手编写一些复杂的测试？你写什么？你从哪里开始？什么时候写什么？

一个简单的答案是参考谷歌或 StackOverflow 寻找答案，或者在网上搜索文章。但那不会帮助你提高。如果你想让你的甜甜圈盒子有最好的，你需要努力工作。首先分解你需要测试什么。你需要检查数据属性的值吗？你想触发一个按钮或单选按钮或选择一个复选框？是否要确保调用了某个函数？或者检查它被调用了多少次？这完全取决于你想测试什么。去 [Jest docs](https://jestjs.io/docs/en/getting-started) ，花点时间看看各种方法。他们的名字是不言自明的。做大量的尝试和错误。这就是你能够编写一些令人敬畏的测试的方法。

在写这篇文章之前，我自己经历了一些尝试和错误。我最初是按照本教程中关于[培养基](https://medium.com/@kevin_peters/unit-testing-vue-js-components-with-jest-86e14ef499da)的说明来做的。但是当我访问 Jest 的文档和 vue-test-utils 的 GitHub 页面时，我发现有一些冲突的东西(可能是因为从作者写这篇文章时起，两个包都做了一些更改)；另外，作者对组件的 HTML 部分(模板)做了很多测试，而我想测试脚本逻辑。所以我不得不做一些实验。我写这篇文章是为了解释我的发现以及我如何编写我的第一个测试。所以继续尝试。如果你陷入困境，总会有好的资源帮助你。顺便说一下，我链接的这篇文章对于测试你的模板也很有帮助，看看吧。同时，这里有一篇关于测试 Vuejs 组件的稍微高级和深入的[文章](https://medium.com/pixelmatters/unit-testing-with-vue-approach-tips-and-tricks-part-1-b7d3209384dc)。

就是这样！您刚刚为您的 Vue.js 应用程序编写了一些很酷的测试。你感觉如何？如果你有任何问题，请在下面的评论中告诉我。我很乐意收到你的来信。