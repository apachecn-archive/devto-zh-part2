# PaperStream:从多答案问题文档中收集数据

> 原文：<https://dev.to/julio_ui/paperstream-collecting-data-from-multiple-answer-question-documents-50c5>

之前发表在[软件可持续发展研究所的博客](https://www.software.ac.uk/blog/2018-08-02-paperstream-software-collects-data-multiple-answer-questions-documents)

**[![image1_0.png](../Images/ca8dbe74b5264292929baf552bebef68.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F73rKUUc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5i1jonpic5cubbtt3q6k.png)T4】**

**作为我博士学位的一部分，我们正在研究是否可以使用智能手机数据来监测帕金森病的进展，我们发现我们必须使用[“回到模拟”](https://doi.org/10.1145/3173574.3173648)，因为纸质日记是患者自我报告症状的最佳工具。这对这项研究来说是极好的，但它给了我们另一件要担心的事情，我必须手动将参与者的答案从纸上转录到电子文件中。我们的目标是 10 名参与者需要在一年内完成 365 页的日记；如果转录每一页需要 45 秒(非常乐观的话)，那么对所有十本日记进行编码将需要大约 114 个小时，或者大约 19 天的工作！**

作为一名计算机科学家，我想为我必须写论文的时候节省整整 114 个小时，于是我在互联网上搜索一个工具，这个工具可以让我自动创建和编码纸质日记(也许是某种给多答案考试打分的软件？).令我沮丧的是，为数不多的可用选项是不再受支持的软件项目，没有很好的文档记录，不是自由或开源的，并且不是非常用户友好的。我决定借此机会回报社区，因为我们实验室中用于数据分析的许多工具都是免费的，这要归功于其他人的工作。 [PaperStream](https://paperstream.netlify.com/) 就是这样诞生的。

PaperStream 是一种软件，研究人员和学者可以用它来创建纸质日记、调查、测验或任何其他带有多选题的文件，人们可以使用笔和纸来回答这些问题，然后可以自动编码为 CSV 文件。PaperStream 是免费、开源的，可用于 Windows、Mac OS 和 Linux，并且有完整的文档，因为它是为没有技术背景的任何人设计的。

为了这篇文章，我将用日记和调查两个例子来展示 PaperStream 的特性。如果您正在处理测验或其他类型的文档，我在这里描述的内容应该与您需要做的事情没有太大的不同。因此，在我们的例子中，日记是一份问卷，一个或多个人必须在几天、几周或几个月内每天回答。类似地，调查是一份一个或多个人只需回答一次的问卷。

对于日记和调查，你只需要一个单页的 PDF 文档，它将作为每一页的模板。对于日记，PaperStream 将为每一页标注一个唯一的日期，如下图所示，对于调查，PaperStream 将使用一个唯一的 ID 来枚举它们。PaperStream 处理完您的模板后，您将获得一个压缩文件，其中包含 A4 和 A5 大小的日记或调查，可以随时打印和装订。

[![image2_0.png](../Images/60ff0bb5954a5608260b9c96ab9b2213.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5yGEWmNl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.software.ac.uk/sites/default/files/image2_0.png)

在你的参与者用笔回答了这些打印出来的问题后，你需要把它们扫描成多页的 TIF 图像，或者压缩成 ZIP 文件的单个 PNG 图像。一旦准备好了，你需要通过一个标记来告诉 PaperStream 在哪里寻找什么答案。标记标题只不过是一组圆圈，指示参与者可以用笔标记页面的哪些区域，以及这些标记/答案的含义，例如，一天中的某个小时或李克特量表中的某个点。因为你使用单一的模板来创建你的日记或调查，你只需要设计一个标记标题一次，就是这样！当标题准备好了，PaperStream 会给你一个 zip 文件和一个 CSV 文件，包含你想要处理的每篇日记或调查的所有答案。同样有用的是，PaperStream 可以检测重复的、丢失的数据，并且非常宽容，因为当至少 15%的答案区域被填充时，它会检测到答案，并且当笔超出它时没有问题。这意味着您的参与者不必担心如何回答问题，这就像使用纸笔一样简单。

从技术角度来看，Python 是开发 PaperStream 的最佳语言。它有多平台支持，它可以通过 [OpenCV](https://opencv.org/) 处理 PDF 文件，这要感谢[pypdf F2](https://pythonhosted.org/PyPDF2/)库和图像。PaperStream 的第一个原型是一个将 PDF 模板转换成 PDF 小册子的脚本，它基于[Luke Plant](https://bitbucket.org/spookylukey/booklet-maker/)的小册子制作商项目。第二个原型需要将纸上的答案编码成电子文件，稍微复杂一些。我想保持 Python 的多平台能力，同时给用户一个不需要花太多时间开发的图形界面。用 Python 创建 GUI 有很多选择。首先，我尝试了 Tkinter，但是画布支持和几何形状操作(比如拖放)并不直接。出于这个原因，我决定将 PaperStream 视为一个桌面 web 应用程序，这意味着 GUI 将基于 HTML/CSS/Javascript，利用 HTML、SVG 和丰富的 Javascript 生态系统，同时依靠本地 web 服务器将所有调用从 web GUI 路由到处理脚本。 [Falcon](https://falconframework.org/) 是我选择的 web 服务器，因为它重量轻，文档丰富，实现简单， [Fabric.js](http://fabricjs.com/) 用于几何操作，加上 [Async。JS](http://caolan.github.io/async/) 用于异步调用，而 [Noty.js](https://ned.im/noty/) 用于通知。然后，对于实际的编码逻辑，我修改了由 [Raphael Baron](https://github.com/rbaron/) 完成的[工作，该工作使用 OpenCV 提取由标记框住的页面部分，并通过比较两张纸之间的黑色像素来补充答案提取功能。所有这些开源软件使得 PaperStream 的开发更快更容易。](https://github.com/rbaron/omr)

最后，PaperStream 第一版的最后冲刺是它的测试、分发和文档。我使用 Python 的单元测试库为创建和编码文档的脚本的核心功能实现了一些单元测试。然后，我考虑了 [Docker](https://www.docker.com/) 和其他类似的选项来使 PaperStream 可用，但最终我选择了 [PyInstaller](https://www.pyinstaller.org/) ，它允许开发人员将 Python 项目(包括启动 Falcon 服务器)作为单个可执行文件或单个 zip 文件分发，可以在所有主要的操作系统上工作。我还在 pip 存储库中部署了 PaperStream，因此开发人员和其他技术用户可以通过一行代码安装它。最后，为了文档，我决定让[雨果](https://gohugo.io/)第一次尝试；用 Markdown 编写它很简单，并且通过每次 [GitHub](https://github.com) 提交自动将静态网站发布到 [Netlify](https://www.netlify.com/) 非常方便。

在这个项目的开发过程中，我学到了很多东西，如果社区发现 PaperStream 有用并将其向前发展，我会很高兴。未来的酷功能可能包括检测不同的钢笔颜色，形状标记，甚至手写文本。与此同时，你可以在 [GitHub](https://github.com/JulioV/paperstream) 免费获得 PaperStream 及其源代码，并通过 [Netlify](https://paperstream.netlify.com/) 获得创建和编码文档的操作指南。哦，如果你想知道的话，我用 PaperStream 在大约 5 分钟内完成了 3650 页的编码；快了 1300 倍。