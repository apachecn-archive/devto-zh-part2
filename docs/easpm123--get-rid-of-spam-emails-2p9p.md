# eas pm 123–清除垃圾邮件！

> 原文：<https://dev.to/jalbam/easpm123--get-rid-of-spam-emails-2p9p>

EASPM123(电子邮件反垃圾邮件 123) 是一个易于使用的 JavaScript API，它可以保护您想要在网站中使用的任何电子邮件地址免受垃圾邮件的侵扰。第一个版本(1.0)于 2015 年 11 月发布。

它可以在任何支持 JavaScript 的浏览器中工作，甚至是旧的浏览器(包括 Internet Explorer 5.0)，提供不同的安全选项(非常可配置)，并且它只是 2.57 KiB 缩小版。如果您愿意，您可以轻松地将其配置为同时使用多种语言。

尽管如此，这个脚本应该被认为是一种提高抵御垃圾邮件安全性的方法，而不是唯一的方法。一个好的反垃圾邮件的邮件(服务器或客户端)仍然总是被推荐。

可以在 GitHub 上找到:[https://github.com/jalbam/easpm123](https://github.com/jalbam/easpm123)

## 基本想法

基本思想是通过使用文本修改电子邮件地址来伪装电子邮件地址，该文本将在所需的 JavaScript 事件被触发时被脚本删除(或者，如果需要，在加载文档时自动删除)。

大多数垃圾邮件不会触发这些 JavaScript 事件，或者，即使它们触发了这些事件，很多时候也不会在触发后捕获到电子邮件地址，而是之前显示的电子邮件地址(因此，它们会以一个无效的电子邮件地址结束，其中包含本应删除的文本)。

要删除的文本应该是人类可以理解的，这样在 JavaScript 被禁用或出现故障的情况下，人类就可以找出真正的电子邮件。在这种情况下，为了帮助任何人找出真实的电子邮件地址，该脚本使用了所谓的“ *without-no-spam* ”元素，这些元素包含人们应该遵循以获得真实电子邮件地址的指令。如果 JavaScript 可用，脚本将自动删除这些没有非垃圾邮件的*元素。*

 *因为现在很多垃圾邮件已经可以把" *AT* "和" *DOT* "翻译成 *"@"* 和*"*相应地，为了提高安全性，这个脚本提供了一种将任何想要的文本翻译成 *AT* ( *@* )符号的方法。这里的关键是使用一个不太有名的文本，没有垃圾邮件将完全理解。

下面详细介绍了使用这个脚本的不同方法，有些方法比其他方法更安全。请继续阅读。

## 使用

您可以直接从 GitHub 下载项目，将其包含在您的项目中，也可以在项目中使用以下命令之一:

**鲍尔**:

```
bower install easpm123 
```

**纱线**:

```
yarn add easpm123 
```

**npm** (需要一个 *package.json* ):

```
npm i easpm123 
```

可以通过脚本使用的主(也是唯一的)对象轻松配置行为(更多信息，您可以阅读[README.htm](http://htmlpreview.github.io/?https://github.com/jalbam/easpm123/blob/master/README.htm)中的说明)。但是如果你很懒，尽管这不太安全，你只需要包含。js 文件，并更改包含电子邮件链接的元素的类名(或者给当前的类添加一个新的类名)(默认情况下，类名应该是“easpm123”)。

为了开始使用这个 API。js "文件(例如， [easpm123_min.js](https://github.com/jalbam/easpm123/blob/master/easpm123_min.js) 已经缩小)必须包含在您的文档中。最常见的方法是使用一个**标签:**

```
<!-- NOTE: Edit the "src" property to point to the right location of the .js file: -->
<script src="easpm123_min.js" type="text/javascript" language="javascript"></script> 
```

如果我们决定使用主(也是唯一的)对象，它看起来是这样的:

```
EASPM123
(
    autoLoad,
    //Next values can be set to null to force using default ones or use
    //an empty array if you do not want any value at all:
    linkClasses,
    withoutNoSpamElementClasses,
    linkIDs,
    withoutNoSpamElementIDs,
    textsToClear,
    atSymbolAliases,
    eventNames
); 
```

接受的参数如下:

| 参数 | 类型 | 缺省值 | 强制？ | 描述 |
| --- | --- | --- | --- | --- |
| **自动装载** | 布尔型 | 真实的 | 不 | 如果设置为 true，当文档准备好时，脚本将自动加载(不需要调用 **run** 方法)。如果设置为 false，所有剩余的参数将被忽略，它们将需要在 **run** 方法中使用。 |
| **链路类** | 字符串数组 | ["easpm123"] | 不 | 将受影响的标签(包含指向电子邮件地址的链接)所使用的类名。 |
| **withoutoutnospamelationclasses** | 字符串数组 | ["easpm123_label"] | 不 | 将被移除的不带非垃圾邮件的元素的类名。 |
| **linkid** | 字符串数组 | ["easpm123"] | 不 | 将受影响的标签(包含电子邮件地址的链接)所使用的 id。 |
| **不带参数** | 字符串数组 | ["easpm123_label"] | 不 | 将被移除的“ *without-no-spam* ”元素使用的 id。 |
| **文本清除** | 字符串数组 | ["无垃圾邮件欢迎"] | 不 | 要从电子邮件地址中删除的文本(区分大小写)。 |
| **at 符号别名** | 字符串数组 | ["{ *AT_HERE* }"] | 不 | 将被在 ( *@* )符号处的*替换的文本。* |
| **事件名称** | 字符串数组 | [“鼠标悬停”、“点击”、“触摸开始”] | 不 | 将触发脚本的事件。这些事件将被附加到所有受影响的标签(包含指向电子邮件地址的链接)以及所有受影响的“ *without-no-spam* ”元素。使用空数组在没有事件的情况下自动运行脚本。 |

注意，除了第一个参数( **autoLoad** )之外，如果我们想要使用它们的默认值，所有其余的参数都可以接受 **null** 值，或者在我们根本不想要任何值的情况下，也可以接受一个空数组(例如 **[]** 值)。在最后一个参数( **eventNames** )的情况下，使用一个空数组将会强制脚本自动运行它的魔法，而不必触发任何事件(不推荐，因为这样会降低安全性)。

如果**自动加载**参数设置为假(不推荐)，其余的参数将被忽略，并且需要时必须手动调用**运行**方法(总是在文件加载后！).该方法接受与主对象相同的参数，除了第一个(它不需要**自动加载**参数)。阅读下面的例子，看看如何使用它。

请记住，一旦脚本被包含，它将使用默认选项运行一次。之后，如果需要，我们可以再次运行它(通过主对象或 **run** 方法)。

主对象只能在触发**窗口**对象的 **onload** 事件之前使用。如果我们想在那之后运行脚本，我们需要使用 **run** 方法。事实上， **run** 方法只能在**窗口**对象的 **onload** 事件被触发后使用。

有关更多信息，请参见下面的示例。

### 例子# 1——对开发者来说最简单的方法(不那么安全),只有 HTML:

注意，在脚本被包含后，它立即修改**窗口**对象的 **onload** 事件(保留任何之前的事件，因此它不会覆盖任何内容)以在几毫秒后执行自身(它没有立即这样做是为了删除一些没有考虑到这一点的垃圾邮件)。如果在包含脚本和它执行之前的毫秒(默认为 100)之间的时间内，**窗口**对象的 **onload** 事件被覆盖，这个例子就不会工作。

这样，任何 **id** 为 **easpm123** 或 **class** 属性包含 **easpm123** 类的标签都会受到影响。要从电子邮件中删除的默认文本将是“ *NO_SPAM_WELCOME* ”，并且“{ *AT_HERE* }”文本将替换为 *AT* ( *@* )符号。

**id** 为 **easpm123_label** 或 **class** 属性包含 **easpm123_label** 类的元素内的文本将在页面加载时自动删除。这些元素称为“ *without-no-spam* ”元素，在 JavaScript 不可用时对用户很有用。

当用户经过或点击或点击电子邮件链接(如上所述的标签)时，将显示真实的电子邮件地址。

#### HTML:

```
<!-- Using ID: -->
<a id="easpm123" href="mailto:email{*AT_HERE*}NO_SPAM_WELCOMEexample.com">email{*AT_HERE*}<del style="text-decoration:line-through;"><s>NO_SPAM_WELCOME</s></del>example.com</a>
<span id="easpm123_label">(without NO_SPAM_WELCOME)</span>
<br />
<!-- Using class: -->
<a class="easpm123" href="mailto:email{*AT_HERE*}NO_SPAM_WELCOMEexample.com">email{*AT_HERE*}<del style="text-decoration:line-through;"><s>NO_SPAM_WELCOME</s></del>example.com</a>
<span class="easpm123_label">(without NO_SPAM_WELCOME)</span> 
```

### 示例 2——对用户来说最简单的方法(非常不安全),使用一点 JavaScript 和 HTML:

这个方法需要一点 JavaScript(使用一个空数组作为 **eventNames** 参数),但是不需要用户触发任何事件。剧本和它的魔力会自动执行。用户可能会更高兴，但一些垃圾邮件也会如此。出于安全考虑，不推荐这种方法。

由于我们使用的是主对象，这必须在触发**窗口**对象的 **onload** 事件之前运行。

#### JavaScript:

```
EASPM123(true, null, null, null, null, null, null, [] /* eventNames */); 
```

#### HTML:

使用与示例#1 相同的 HTML 代码。

### 例 3——对用户来说第二简单的方法(仍然不安全),使用一点 JavaScript 和 HTML:

这将比示例#2 稍微安全一点，因为我们用期望的值来改变默认值。使用不同的 id 和类名只是为了展示它是如何工作的。

由于我们使用的是主对象，这必须在触发**窗口**对象的 **onload** 事件之前运行。

#### JavaScript:

```
EASPM123
(
    true, //autoLoad.
    ["class_email_link", "class_email_link2"], //linkClasses.
    ["class_without_no_spam_label", "class_without_no_spam_label2"], //withoutNoSpamElementClasses.
    ["id_email_link", "id_email_link2"], //linkIDs.
    ["id_without_no_spam_label", "id_without_no_spam_label2"], //withoutNoSpamElementIDs.
    ["WITHOUT_THIS_TEXT", "TAKE_THIS_OUT"], //textsToClear
    ["[PUT_AT_HERE]", "{HERE_AN_AT_SYMBOL}"], //atSymbolAliases.
    [] //eventNames.
); 
```

#### HTML:

```
<!-- Using ID: -->
<a id="id_email_link" href="mailto:email[PUT_AT_HERE]WITHOUT_THIS_TEXTexample.com">email[PUT_AT_HERE]<del style="text-decoration:line-through;"><s>WITHOUT_THIS_TEXT</s></del>example.com</a>
<span id="id_without_no_spam_label">(without NO_SPAM_WELCOME)</span>
<br />
<a id="id_email_link2" href="mailto:email[PUT_AT_HERE]WITHOUT_THIS_TEXTexample.com">email[PUT_AT_HERE]<del style="text-decoration:line-through;"><s>WITHOUT_THIS_TEXT</s></del>example.com</a>
<span id="id_without_no_spam_label2">(without NO_SPAM_WELCOME)</span>
<br />
<!-- Using class: -->
<a class="class_email_link" href="mailto:email{HERE_AN_AT_SYMBOL}TAKE_THIS_OUTexample.com">email{HERE_AN_AT_SYMBOL}<del style="text-decoration:line-through;"><s>TAKE_THIS_OUT</s></del>example.com</a>
<span class="class_without_no_spam_label">(without NO_SPAM_WELCOME)</span>
<br />
<a class="class_email_link2" href="mailto:email{HERE_AN_AT_SYMBOL}TAKE_THIS_OUTexample.com">email{HERE_AN_AT_SYMBOL}<del style="text-decoration:line-through;"><s>TAKE_THIS_OUT</s></del>example.com</a>
<span class="class_without_no_spam_label2">(without NO_SPAM_WELCOME)</span> 
```

### 示例 4——对用户来说不那么容易的方法(更安全),使用了更多的 JavaScript 和 HTML:

这类似于示例 3，但是只在默认事件(onmouseover、onclick 和 ontouchstart)下触发脚本，而不是自动触发。

由于我们使用的是主对象，这必须在触发**窗口**对象的 **onload** 事件之前运行。

#### JavaScript:

```
EASPM123
(
    true, //autoLoad.
    ["class_email_link", "class_email_link2"], //linkClasses.
    ["class_without_no_spam_label", "class_without_no_spam_label2"], //withoutNoSpamElementClasses.
    ["id_email_link", "id_email_link2"], //linkIDs.
    ["id_without_no_spam_label", "id_without_no_spam_label2"], //withoutNoSpamElementIDs.
    ["WITHOUT_THIS_TEXT", "TAKE_THIS_OUT"], //textsToClear
    ["[PUT_AT_HERE]", "{HERE_AN_AT_SYMBOL}"], //atSymbolAliases.
); 
```

#### HTML:

使用与示例 3 中相同的 HTML 代码。

### 例子# 5——一个稍微困难一点(稍微安全一点)的方法，使用更多的 JavaScript 和 HTML:

这类似于示例 4，但是使用了 **run** 方法，而不是主对象。因此，我们将手动运行脚本，而不是自动运行。

注意，main 对象必须在触发**窗口**对象的 **onload** 事件之前使用，但是 **run** 方法必须在此之后运行。

出于安全考虑，默认情况下，主对象在自动运行脚本之前会等待 100 毫秒。在本例中，我们将多等一会儿，以增加更多的安全性。

#### JavaScript:

```
setTimeout
(
    function()
    {
        EASPM123.run
        (
            ["class_email_link", "class_email_link2"], //linkClasses.
            ["class_without_no_spam_label", "class_without_no_spam_label2"], //withoutNoSpamElementClasses.
            ["id_email_link", "id_email_link2"], //linkIDs.
            ["id_without_no_spam_label", "id_without_no_spam_label2"], //withoutNoSpamElementIDs.
            ["WITHOUT_THIS_TEXT", "TAKE_THIS_OUT"], //textsToClear
            ["[PUT_AT_HERE]", "{HERE_AN_AT_SYMBOL}"], //atSymbolAliases.
            []
        );
    },
    300
); 
```

#### HTML:

使用与示例 3 中相同的 HTML 代码。

## 提高安全性

为了提高安全性并尽可能多地清除垃圾邮件，我推荐以下方法:

1.  不要自动触发脚本(不需要事件)。因此，不要像例 2 或例 3 那样做。

2.  不要对替换 ( *@* )符号处的*的文本使用默认值。运用你的想象力去发明新的。看看例子#4。*

3.  不要对应该从电子邮件地址中删除的文本使用默认值。运用你的想象力去发明新的。看看例子#4。

4.  不要使用类或 id 的缺省值(对于元素和不带-no-spam 元素的“*”),请使用您自己的值。看看例子#4。*

5.  使用 **run** 方法代替主对象，在**窗口**对象的 **onload** 事件被触发后几毫秒调用它。看看例子 5。

6.  可以在“*with-no-spam*”元素中使用图像或其他元素来代替文本，以使垃圾邮件发送者更难识别电子邮件地址。请记住，有些浏览器可能会禁用图像，甚至有些人无法正常查看它们。

7.  您可以考虑使用其他事件而不是默认事件来触发脚本。这也许可以提高安全性。

8.  如果你足够偏执，你可以考虑使用不同的方法来混淆可选参数中使用的数组中的字符串，也可以使用其他的混淆方法。再次，发挥你自己的想象力。

## 最终意见

按照说明和一些指南(包含在软件包中)，电子邮件地址应该是人类可以理解的，即使 JavaScript 被禁用。

如果你厌倦了被垃圾邮件，你应该试试这个小脚本(带有一个垃圾邮件还不知道的电子邮件地址！).

活生生的例子:[http://htmlpreview.github.io/?https://github . com/jalbam/eas pm 123/blob/master/test _ min _ eval . htm](http://htmlpreview.github.io/?https://github.com/jalbam/easpm123/blob/master/test_min_eval.htm)

## 许可证

禁止在未保留作者姓名和版权条款的情况下使用。除此之外，只是免费使用这个脚本，但绝不出售！***