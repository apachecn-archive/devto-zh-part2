# 通过 7 个简单的步骤在 Internet Explorer 11 中调试 JavaScript

> 原文：<https://dev.to/raygun/debug-javascript-in-internet-explorer-11-in-7-easy-steps-2imj>

*原载于 [Raygun 博客](https://raygun.com/blog/debug-javascript-internet-explorer/)T3】*

本文将重点介绍在 Internet Explorer 11 的[开发工具](https://msdn.microsoft.com/en-us/library/dd565628(v=vs.85).aspx)中调试 JavaScript 代码。尽管 Internet Explorer 经常受到批评，但 IE11 内置的开发工具使得在浏览器中开发和调试代码不再像过去那样令人畏惧。这款浏览器的工具拥有 Chrome 和 FireFox 等其他更注重开发者的浏览器的许多功能。

在本文中，我们将在 Internet Explorer 中调试一个示例。

我们将遵循的步骤是:

1.  示例项目介绍
2.  分析射线枪错误报告
3.  探索开发人员工具的结构
4.  向代码中添加断点
5.  逐句通过您的代码
6.  确定应用程序的状态
7.  修复 Bug！

所以，让我们开始吧！

## 第一步:样本项目介绍

为了演示如何使用 Internet Explorer 的开发工具调试应用程序，我将使用一个简单的 Add Person 表单。该表单允许您输入名、中间名和姓。点击“保存”按钮，表格会做一些处理，数据会被发送到你的(假想的)服务器。

[![Internet Explorer debugging example](img/e2e949e60041e4bd31ea43e62784baa7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MOC1rrey--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raygun.com/blimg/debug-explorer/image5.png)

该表单的代码有三个功能:

一个点击处理器
一个大写字符串函数
一个保存函数

```
var saveButton = document.getElementById('saveButton');
var firstNameField = document.getElementById('firstName');
var middleNameField = document.getElementById('middleName');
var lastNameField = document.getElementById('lastName');

function onSaveButtonClick(){
    var firstName = firstNameField.value;
    var middleName = middleNameField.value;
    var lastName = lastNameField.value;

    // capitalise the names
    firstName = capitalizeString(firstName);
    middleName = capitalizeString(middleName);
    lastName = capitalizeString(lastName);
    doSave(firstName, middleName, lastName);
}
function capitalizeString(value){
    return value.split('')[0].toUpperCase() + value.slice(1);
}
function doSave(firstName, middleName, lastName){
    alert(firstName + '  ' + middleName + '  ' + lastName + ' has been saved!');
}
saveButton.addEventListener('click', onSaveButtonClick); 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，在周五晚些时候将它交付生产后，您开始看到错误报告进入您的仪表板。有一个错误，你需要修复它。很快。

## 第二步。分析射线枪错误报告

进入 Raygun 的错误报告有大量的信息可以用来查找和修复错误，所以让我们来看看我们在处理什么。

[![Raygun Error Internet Explorer Debugging](img/fc50868e81aa702c19083a6b77b4ebfb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kF4Cz47P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raygun.com/blimg/debug-explorer/image18.png)

调试错误所需的信息位于堆栈跟踪模块中。

堆栈跟踪的消息部分是对错误的简要概述。在这种情况下，对未定义的值调用了`toUpperCase`方法。

堆栈跟踪告诉您错误发生的位置以及导致错误的函数调用序列。从上面的截图可以看出，错误发生在`index.js`文件第 20 行的`capitalizeString`函数中。

知道哪一行触发了错误意味着您可以直接跳到错误发生的地方，并开始深入研究导致问题的原因。

## 第三步:探索开发者工具的结构

第一步是在 Internet Explorer 中启动应用程序，并打开开发者工具。你可以通过键盘使用 F12 键或在**工具**菜单中选择 **F12 开发者工具**来完成。

[![Internet Explorer Debugging F12 Developer Tools](img/6b2aed98ac4bbc40d3eb05966dd7f7e6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zaIjVIsX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raygun.com/blimg/debug-explorer/image1.png)

开发人员工具现在将在浏览器选项卡中打开，并且 DOM Explorer 选项卡将处于活动状态。通过单击名称切换到控制台选项卡。Console 选项卡允许您在任何时候执行任意 JavaScript 代码，或者查看来自`console.log`调用的任何输出。

尝试输入`alert('Hello!');`并按回车键——您应该会立即看到警告。

[![Error alert Internet Explorer Debugging](img/7693a7b8a2ac7ce2c2f45c6bc0b6dae8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B6lqBPEw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raygun.com/blimg/debug-explorer/image2.png)

Console 选项卡是一个很有价值的调试工具，因为在诊断问题时，您可以将它作为一个暂存板来尝试代码和评估变量。

要调试代码，首先需要能够在开发人员工具中浏览源代码。您可以在调试器选项卡中完成此操作。

[![Internet Explorer Debugging Tab](img/6dbc9817d64e5fd4fe60f16a1f8d03fb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BNcvS-HA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raygun.com/blimg/debug-explorer/image3.png)

通过单击选项卡左上角的按钮，可以访问加载到页面中的所有源文件的树视图。您可以像在 IDE 中一样在此导航文件，因为内容显示在中央窗格中。

[![Internet Explorer debugging example](img/c79b96f2450437359783469077801ab4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5oQmvfX1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raygun.com/blimg/debug-explorer/image23.png)

右边的窗格为您提供了所有的调试选项，我稍后会谈到这些选项。

如果您有许多文件，您可以通过在文件浏览器顶部的文本框中键入文件名来搜索它们。

在应用程序中，您知道问题出在`index.js`文件中，因此从文件列表中选择它以查看其内容。

## 第四步:在代码中添加断点

现在你可以查看你的代码了，我们希望能够一次一行地检查，看看哪里出错了。为此，我们使用断点。断点是在代码中特定点停止执行的标记，以便您可以及时检查代码在该点的状态，并逐行继续执行。

添加断点有几种不同的方法，我将在这里一一介绍:

### 事件断点

当页面上发生特定事件(或一组事件)时，您可以强制中断执行。使用调试窗格中的断点部分，您可以单击**添加事件断点**按钮，并从列表中选择您想要中断的事件。您可以选择添加一个条件表达式，以防止每次执行都被停止。

[![Add event breakpoint Internet Explorer debugging](img/bf931d8cb62f528bc4a5433c479c4e33.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bBOwU3xb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raygun.com/blimg/debug-explorer/image21.png)

### 行断点

添加断点最常见的方式可能是找到您想要停止的特定行，并将其添加到那里。导航到您感兴趣的文件和行，然后单击行号。一个红色的标记将被添加到那一行，每次执行到这一行代码时将停止。在下面的截图中，它将停在`index.js`的第 7 行。

[![Internet Explorer debugging example](img/138d7fd75b72fdcc1475c4f845740d55.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zF3dv6hl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raygun.com/blimg/debug-explorer/image22.png)

### 程序化断点

您还可以通过编程方式添加断点，如果您不想在开发人员工具中搜索您的代码，而您的 IDE 中又有这样的工具，这将非常有用。您还可以使用这种方法有条件地引入断点，例如在循环的某些迭代中，或者如果代码在页面加载时运行，而没有时间手动添加断点。

为此，您可以在希望中断执行的位置添加`debugger;`语句。下面的代码将与上面的行断点具有相同的效果。

[![Programmatic Breakpoint Internet Explorer Debugging](img/4f4a7fc4b64ff7283dd37afbe4b01555.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hNWacqkc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raygun.com/blimg/debug-explorer/image19.png)

### 错误断点

Developer Tools 有一个方便的特性，当它在代码中遇到异常时会停止执行，允许您检查错误发生时正在发生什么。您甚至可以选择在已经由 try/catch 语句处理的异常上停止。

要启用此功能，请单击带有暂停符号的停止标志图标，并从列表中选择您想要的选项。启用时，它将呈蓝色。

[![Break on exceptions Internet Explorer Debugging](img/a641ffb1b57af0b8c1e15aa2ddfcf58a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RqDRkG9l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raygun.com/blimg/debug-explorer/image11.png)

## 第五步:单步调试你的代码

既然我们知道了如何进入我们的代码，我们现在要一步一步地检查每一行，这样我们就可以找出哪里出错了。首先，在第 7 行放置一个断点——就在 Add 按钮的 click 处理程序内，这样我们可以从头开始。

在上一节中，我们从 Raygun 错误报告中推断错误来自于`capitalizeString`方法。这个方法被调用了三次，那么，哪个实例是罪魁祸首呢？
您可以仔细查看堆栈跟踪，发现是来自第 13 行的调用导致了错误。您知道第 13 行与中间名值相关。因此，您应该通过正确地处理输入来集中精力重现错误。

有了这些额外的知识，您可以填写名和姓字段，但将中间名留空，以查看这是否会触发错误。

[![Internet Explorer debugging example](img/e2e949e60041e4bd31ea43e62784baa7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MOC1rrey--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raygun.com/blimg/debug-explorer/image5.png)

点击保存按钮。在这里，Source 选项卡将打开，您可以看到断点被激活。您现在可以开始逐句通过代码了。为此，您可以使用调试窗格中的四个按钮。

[![play pause Internet Explorer debugging](img/69bcbddb8386cb4c0296172a906c87de.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tIis3otG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raygun.com/blimg/debug-explorer/image13.png)

继续执行代码，直到下一个断点

跳过当前行，将我们移到下一行

单步执行该行上的下一个函数调用

跳出当前函数调用，将调用堆栈后退一级。

您将使用这些来一步步实现您的`capitalizeString`函数。所以从第 7 行开始，使用“跳过”按钮，直到我们到达第 13 行。活动线显示为黄色背景，并有一个指向它的橙色箭头。

[![Internet Explorer debugging example](img/138d7fd75b72fdcc1475c4f845740d55.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zF3dv6hl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raygun.com/blimg/debug-explorer/image22.png)

您现在可以使用“单步执行”按钮进入对`capitalizeString`函数的调用。

[![Internet Explorer debugging example](img/138d7fd75b72fdcc1475c4f845740d55.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zF3dv6hl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raygun.com/blimg/debug-explorer/image22.png)

### 导航调用堆栈

当你像这样遍历代码时，你可能想跳回一个父函数来检查当时发生了什么。为此，请使用 Callstack 部分，该部分列出了您的代码中到达这一点所经过的所有函数——与 Raygun 错误报告中显示的 Call stack 完全相同。

[![Call stack Internet Explorer Debugging](img/f7605b8f60aa56f8f0d2c3d2537b5eaa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Eyc16KWp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raygun.com/blimg/debug-explorer/image9.png)

您只需点击列表中的一个项目，即可返回该功能。请记住，执行中的当前位置不会改变，因此使用“单步执行”按钮将从调用堆栈的顶部继续。

## 第六步:确定你的应用程序的状态

现在您已经导航到了错误发生的地方，我们需要检查应用程序的状态并找出导致错误的原因。

在代码继续运行之前，有很多选项可以用来计算变量包含的值和表达式的值。我们将依次看一看:

### 鼠标悬停

确定变量的值的最简单的方法是将鼠标悬停在它上面，工具提示将弹出该值。您甚至可以选择一组表达式，并将鼠标悬停在上面以获得表达式的输出。

[![Mouse Hover Internet Explorer Debugging](img/ab7377b8fd4f0df2fbae9e466c5f69c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i_bUt3DU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raygun.com/blimg/debug-explorer/image7.png)

### 观察者

您可以将表达式添加到“监视”面板，该面板会在您浏览代码时显示表达式的当前值。这有助于跟踪更复杂的表达式是如何随时间变化的。

[![Watchers Internet Explorer Debugging](img/72234870c6e0817c532193f46bdb78f3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TLB3mRwY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raygun.com/blimg/debug-explorer/image4.png)

您可以通过单击面板顶部的按钮，单击底部的“添加监视”文本，或者通过右键单击并选择“添加监视”来选择表达式，从而添加这些内容。

### 范围

“监视”面板还显示当前范围内的变量及其关联值的列表。这类似于手动添加的观察器，但它们是由开发人员工具自动生成的。这一部分有助于识别局部变量，省去了您显式地将它们添加到监视列表中的麻烦。

### 事件跟踪点

IE 11 的开发者工具的一个独特的功能是事件跟踪点，它允许你定义一个表达式来计算和输出到控制台，只要一个选定的事件发生。例如，当鼠标事件发生时，可以输出当前的日期时间。

要添加事件跟踪点，请单击断点部分的按钮。从打开的窗口中选择要跟踪的事件，然后输入要输出的表达式。

[![Event tracepoint Internet Explorer Debugging](img/38c55d5e5791c1e6815b31c0e4ff9bef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yGFTesmO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raygun.com/blimg/debug-explorer/image17.png)

### 控制台

最后，Console 选项卡是检查表达式值和试验代码的好工具。只需切换回控制台选项卡，键入一些代码，然后按回车键。开发人员工具将在当前断点的上下文和范围内执行代码。

## 第七步:修复 Bug

切换到 Console 选项卡，让我们开始分解导致错误的行，这样您就可以使用 Console 选项卡来修复它

首先，检查`value.split(‘’)`调用的输出，这样就可以得到第一个字符，然后对它调用`toUpperCase`函数。

在控制台中执行表达式显示它返回一个空数组——这就是错误的来源！因为它返回一个空数组，我们试图在第一项上调用`toUpperCase`(这是未定义的，因为没有项)给你错误。

您可以通过在控制台中输入完整的表达式来验证这一点:

[![Console Internet Explorer Debugging](img/68a64989ae7eb11cf2533a5c7ea9b4fb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0SQP-jrV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raygun.com/blimg/debug-explorer/image10.png)

因此，要解决这个问题，您需要检查字符串是空的还是未定义的。如果是，您需要不做任何处理就返回一个空字符串。

```
function capitalizeString(value){
    if(!value || value.length === 0){
        return '';
    }

    return value.split('')[0].toUpperCase() + value.slice(1);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

这就结束了在 Internet Explorer 11 的开发工具中调试 JavaScript 的快速介绍。这些开发工具是一个受欢迎的飞跃，让开发人员在 Internet Explorer 上的体验变得令人畏惧。

与其他浏览器的产品相比，这些工具非常完整，甚至包括一些自己独有的功能。