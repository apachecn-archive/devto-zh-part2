# 重构继承的代码库

> 原文：<https://dev.to/huijing/refactoring-an-inherited-codebase-pd7>

因此，在 2018 年，我给自己找了一份有收入的全职前端开发工作。你知道，就是那种你去办公室，有同事和老板的那种？此外，支付支票和中央公积金(这是新加坡相当于退休基金)，喜欢这些。💰💰💰

无论如何，我的职责之一是重构和清理公司现有产品的前端代码库。这并不是说我以前从未重构过代码库，我一半的职业生涯都是建立在重构遗留项目上的，但是这些项目比我以前的项目更大更乱。请注意，我使用了带“s”的“产品”一词。

但首先，一些背景故事。

1.  初创公司的创始人有伟大的想法。
2.  初创公司创始人与一些实习生一起制作原型，向潜在客户展示
3.  潜在客户变成实际客户
4.  实际的客户端发出功能请求
5.  实习生尽最大努力添加功能
6.  产品

我相信这种情况并不少见，几乎所有你扔石头砸的初创企业都有类似的历史。所以我一点也没有给我的雇主抹黑。事实上，我很钦佩他们用有限的资源执行了一个伟大的想法，并设法让真正的付费客户加入进来。很值得称赞。

## 评估形势

在加入该组织之前，我被告知代码库需要改进。事情进展顺利，但在幕后，随着吞吐量的增加和功能请求的堆积，事情变得难以处理。于是，我决定在进行任何实际的编码或重构之前，先制定一个总体策略。我认为这种情况下可以采用三阶段方法。

<figcaption>一般策略和方法</figcaption>

[![A three-phase plan](img/9d1bffe2d556b151be829e329457f9c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OqDahjg9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/refactoring/plan-640.png)

在我的项目结束时，*阶段 3* 仍然很遥远，因为整个应用程序需要进行重大的架构变更。但是，嘿，*二期*(清理)本身就已经是一项重大的努力。

在此之前，我对 Python 应用程序没有太多的经验，但是我逐渐意识到，当涉及到 web 应用程序时，某些模式是普遍的。我必须处理的产品是基于 [Flask](http://flask.pocoo.org/) 构建的，由于我被雇佣担任前端角色，所以我几乎所有的工作都只发生在`static`和`templates`文件夹中。差不多了。

```
# Structure of a basic Flask application
PROJECT_FOLDER/
    |-- yourapplication.py
    |-- static/
    | `-- style.css
    `-- templates/
        |-- layout.html
        |-- index.html
        |-- login.html
        `-- … 
```

Enter fullscreen mode Exit fullscreen mode

鉴于我是他们雇佣的第一个专门的前端角色，这个项目前端的状态，我们该怎么说呢，还不够好，就不足为奇了。显然，事情的后端并没有漂亮到哪里去，但我再次怀疑大多数初创公司直到生命的后期才拥有架构良好的代码库。

### 初步现场评估

在深入研究代码之前，我做了一个基本的点击，以了解网站的外观和感觉，以及它的作用。对我来说，这是相当重要的一步，因为你一生只能遇到一次第一次的地点*。我做了笔记。关于我花了一段时间才弄明白的事情的笔记，我不得不问“嗯，这到底是什么意思？”，需要额外的认知努力才能理解的功能。*

<figcaption>不确定那些是做什么的……</figcaption>

[![Mysterious inputs without labels](img/e1e876c0a9132dbf163ec5987ac1f259.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t5v6tJjo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/refactoring/unknown-inputs-640.jpg)

作为一个在过去 5 年中以开发界面为生的人，相信我，每天从事产品工作的人是最不适合做用户测试的群体。因为我们与产品、bug 等等的互动比普通用户要频繁得多。

因此，我们无意中对大量的可用性问题视而不见，只是因为无论我们做什么来解决这个问题，都致力于肌肉记忆。即使是一些不直观的东西，比如必须按特定的顺序点击某些东西才能工作。

所有这些都记录在一个可靠的电子表格中。我不知道你怎么想，但是我很喜欢把电子表格作为一种组织工具。对我来说，这是将数据组织成有用信息的最简单的方法。但这只是我。你做你的。

<figcaption>我能说什么呢，我喜欢电子表格</figcaption>

[![Compiling findings into a Google Sheets document](img/206c281183bb4ea423e4aa400d06fecc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fMOuQjQS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/refactoring/tracker-640.jpg)

### 现场分析用工具

最基本的分析可以通过 DevTools 在任何主流浏览器中从*网络*标签完成。这更多的是个人偏好，但我更喜欢 Chrome 和 Firefox 的做法，它们都告诉你请求的数量、传输的大小、完成加载所需的时间以及`DOMContentLoaded`何时被触发。

<figcaption>嗯，这里有点偏多</figcaption>

[![Chrome's network tab](img/7a942ca9fcfcc8fe42fdcdc3869453f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4DNfWiWy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/refactoring/performance-640.png)

在完成整个产品之后，我总共看到了 24 个不同的页面，有些页面是纯文本内容，有些页面则包含了大量用于创建复杂规则和条件的业务逻辑。我把我的发现扔进了前面提到的电子表格，最后得到了以下统计数据:

*   平均请求数:76
*   平均页面重量:6.017mb
*   外部 JS 库的总数:49
*   外部 CSS 文件总数:30

Chrome 也有[一个漂亮的代码覆盖工具](https://developers.google.com/web/updates/2017/04/devtools-release-notes)，它可以让你大致了解你的应用程序中有多少冗余代码。通常冗余代码的最大来源来自外部库，但有时这是不可避免的，所以有必要看得更深一些，而不仅仅是原始数字本身。

<figcaption>没用的代码还真不少</figcaption>

[![Chrome's coverage tool](img/3a1964c7f074bef662c83fd3733433b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--beYv6a2h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/refactoring/coverage-640.jpg)

### 评估审计结果

很明显，任何重构代码的尝试都将花费更多的时间和精力，这是我一个人的团队所无法承担的。我称之为*一个大脑两只手*问题，即惠晶只有一个大脑和两只手。

来自后端的数据通过 Jinja 变量传递到前端。有时，一些变量不仅仅是原始数据，而是包含预先格式化的字符串和值，这使得改变事情的实现方式变得很棘手。

<figcaption>这是痛苦的经历，说实话</figcaption>

[![Mish-mash of HTML, CSS, Javascript and Jinja variables](img/9f2e849d431c9d219834ef76203b1ad7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lXb7FhL6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/refactoring/messy-code-640.png)

模板文件中有大量的内联 Javascript，Jinja 变量在 Javascript 函数中被直接调用和使用在`<script>`标记之间。这对我来说很难拒绝，我坚持将所有 Javascript 提取到单独的`.js`文件中，并保持模板内联样式和脚本的整洁。

## 烧毁它，建设它

说实话，我不会轻易推荐这种方法。但是考虑到目前的情况:

*   这不是一个太大的应用程序
*   根本没有合适的 HTML 结构
*   外部库过时且安装不一致
*   代码是内联脚本和内联样式中 Jinja 变量的大杂烩
*   事实是我的青春正在溜走，

我决定去掉所有的样式和脚本，从头开始重写，只在必要的地方包括外部 Javascript 库，比如 [Moment.js](https://momentjs.com/) 和 [Chart.js](https://www.chartjs.org/) 。

虽然我的团队由我的左手和右手(以及我的大脑，团队中非常重要的一员)组成，但我有理由相信我可以完成这件事，因为我可以比我认识的大多数开发人员更快地使用 CSS，所以外观和感觉部分不会花我太多时间。从 UI 功能的角度来看，事情并没有显得过于复杂。

即便如此，完成最初的重写、整理和文档工作可能需要 **5 个完整的人工月**。与此同时，还有一些演示要做(贸易展览之类的)，以及我自己的非雇主相关的承诺。所以可以肯定地说，我在过去的 8 个月里一直很忙。

### 功能匹配

我想说，在重写时，我可以参考一些功能需求，但不幸的是，一个也没有。或者至少没有更新来反映产品在当前状态下的功能。但是如果一切顺利的话，我现在就不会得到这份工作了，不是吗？

计划 B:当我重写应用程序的每一页时，运行一个跟踪当前主版本的应用程序实例，并匹配所有可观察到的功能。我想重申的是，这之所以可行，只是因为应用程序一开始就具有可管理的规模。

<figcaption>原文在左边，改写在右边</figcaption>

[![Before and After look and feel](img/6f484ecebe8aaa6416ff24f768b475be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Pc1dPiwQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/refactoring/before-after-640.png)

这种方法也解决了大量的实现问题。例如，访问存储在数据库中的信息的 REST APIs 是有限的，因为以前的实现直接在内联 Javascript 函数中使用 Jinja 变量。参见下面的图表:

```
<script>
  (function() {
    $(document).ready(function() {
      $('input[name="is_activated"]').bootstrapSwitch();

      var formData = &lcub;&lcub; formData|tojson &rcub;&rcub;;
      &lcub;% if name %&rcub;
      $("#rule_form_name_input").val(htmlTextToString("&lcub;&lcub; name &rcub;&rcub;"));
      &lcub;% endif %&rcub;
      &lcub;% if description %&rcub;
      $("#comments-text").val(unescapeJs("&lcub;&lcub; description|escapejs &rcub;&rcub;"));
      &lcub;% endif %&rcub;

      var editorHelpers = EditorHelpers();

      &lcub;% for field in fields %&rcub;editorHelpers.addSelectData("field", "&lcub;&lcub; field['name']|safe &rcub;&rcub;", "&lcub;&lcub; field['display_name']|safe &rcub;&rcub;", &lcub;&lcub; field['filter_equivalence_classes']|tojson &rcub;&rcub;);&lcub;% endfor %&rcub;
      &lcub;% for operator in operators %&rcub;editorHelpers.addSelectData("operator", "&lcub;&lcub; operator['name']|safe &rcub;&rcub;", "&lcub;&lcub; operator['display_name']|safe &rcub;&rcub;", &lcub;&lcub; operator['filter_equivalence_classes']|tojson &rcub;&rcub;);&lcub;% endfor %&rcub;
      &lcub;% for value in values %&rcub;editorHelpers.addSelectData("value", "&lcub;&lcub; value['name']| replace('\r\n', ' ') &rcub;&rcub;", "&lcub;&lcub; value['display_name']| replace('\r\n', ' ') &rcub;&rcub;", &lcub;&lcub; value['filter_equivalence_classes']|tojson &rcub;&rcub;);&lcub;% endfor %&rcub;

      if (formData!=null) {
        var formAlert = formData["alert"]
        if (formAlert) {
          editorHelpers.populateAllAlertData(formAlert);
        }
      }
      var groupObjs = [];
      /* Another 200 plus lines of this */
</script> 
```

Enter fullscreen mode Exit fullscreen mode

还有几个例子，传递到前端的数据包含嵌入的标记，这迫使我修改 Python 应用程序文件来删除不需要的预处理。别管后端了。_(ツ)_/

### 重新评估现场表现

当烟雾散去时，现场的统计数据如下:

*   平均请求数:30
*   平均页面重量:325kb
*   外部 JS 库的总数:15
*   外部 CSS 文件总数:4

很大一部分重量来自功能齐全的外部库，它们的功能很少被使用。其中很大一部分要么被轻量级替代方案所取代，要么被重新编写。

<figcaption>加载代码从 5.1mb 降到 291kb</figcaption>

[![Coverage tool results after rewrite](img/87be29e8dad49ee529975a914de1b217.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YAPWoe60--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/refactoring/coverage2-640.png)

唯一加载的外部 CSS 是用于日期选择器和数据表的，甚至那些被定制为只包含相关的样式，所以在 CSS 方面也节省了很多。

<figcaption>从 83 个 7.5mb 的请求减少到 40 个 563kb 的请求，还有改进的空间</figcaption>

[![Page weight after rewrite](img/10427a3e1a24c64cb78439374f78d6e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JoBwxBIX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/refactoring/performance2-640.png)

作为初步评估，这是一个相对较好的结果。第二轮重构还有进一步改进的空间，无论是在 Javascript 方面还是在 CSS 方面，但是这部分工作可能会落到下一个接手这个项目的人身上。

## 包装完毕

我对这一经历的最好类比是在飞行的飞机上交换引擎。但我认为这种情况并不少见，尤其是在初创企业中。我确实从这次经历中学到了很多东西(甚至学会了一点 Python)，而且不仅仅是从编码的角度。

偿还技术债务根本不是一件小事，它强调了在编写任何代码行之前就有一个坚实的架构的重要性。正如几位经验丰富的前辈曾经建议我的那样:

> 最好的代码是没有写出来的代码

在初创企业中，文档似乎是另一个退居二线的东西，但事实是，人们会来来去去。在一个流失率相对较高的环境中，适当的文档和移交对于确保您正在构建的软件的连续性和一致性变得更加重要。

在一个独角兽拉屎彩虹的理想世界里，我们不需要做这么大的重写或重构项目，然而，鉴于每个组织都有自己的一套约束，有时这只是产品开发过程中不可避免的一部分。

为不断进步干杯，愿你写的代码永远比你昨天写的更好。🥃