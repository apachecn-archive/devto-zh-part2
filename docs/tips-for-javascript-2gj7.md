# Javascript 技巧

> 原文：<https://dev.to/senperfect12/tips-for-javascript-2gj7>

如果您在 Javacript 中迈出了第一步，并且已经开始变得又脏又乱...你没有任何借口去编写有序的代码，一切都会变得更容易。论坛上到处都是关于 Ajax、DOM 以及如何使用一些库或效果的信息请求。有大量的信息、脚本、正在开发的库、博客和专门研究这个主题的新网站，你只需要一点时间来看看...很容易在 Digg.com 或华盛顿找到最好的。icio.us，Javascript 和 DHTML 成为你简历中主要技能的不受欢迎的人的日子已经过去了。今天，绝大多数 Javascript 代码比“时代”的 DHTML 要干净得多。
现在是成为 Javascript 爱好者的好时机。尽管很久以前发生的一些缺陷会重复出现。
这里有一系列的提示，可以让你更容易地组织你的 Javascript 代码，有些提示太明显了，但是我们都知道人类是唯一能够...
保持代码的语法和结构整洁干净
这意味着你可以节省，比如说，一行的长度限制(80 个字符)以及你可以编写合理的小函数。一个错误是认为在一个长函数中我们可以做任何事情。函数有一个合理的大小意味着你可以在扩展代码时重用它们，不要极端化，只做一两行的函数。这可能比使用单一函数更令人困惑。
这是一个示例，显示了根据功能的大小和任务的划分，什么是正确的度量:
function tool links(){
var tools = document . createelement(' ul ')；
var item = document . createelement(' Li ')；
var item link = document . createelement(' a ')；
item link . set attribute(' href '，' # ')；
item link . appendchild(document . create text node(' close '))；
item link . onclick = function(){ window . close()；}
item . appendchild(item link)；
tools . appendchild(item)；
var item 2 = document . createelement(' Li ')；
var item link 2 = document . createelement(' a ')；
item link 2 . set attribute(' href '，' # ')；
item link 2 . append child(document . create text node(' print ')；
item link 2 . onclick = function(){ window . print()；}
item 2 . appendchild(item link 2)；
tools . appendchild(item 2)；
document.body.appendChild(工具)；
}
您可以通过将每个任务与它自己的功能分开来优化这个功能:

function tool links(){
var tools = document . createelement(' ul ')；
var item = document . createelement(' Li ')；
var itemlink = createLink ('# '，' close '，close window)；
item . appendchild(item link)；
tools.appendChild(项目)；
var item 2 = document . createelement(' Li ')；
var itemlink2 = createLink ('# '，' print '，print window)；
item 2 . appendchild(item link 2)；
tools . appendchild(item 2)；
document.body.appendChild(工具)；
}

函数 print window(){
window . print()；
}

函数 close window(){
window . close()；
}

函数 createLink (url，text，func){
var temp = document . createelement(' a ')；
temp.setAttribute ('href '，URL)；
temp . appendchild(document . create text node([买短文](https://senperfect.com/buy-essay-paper-online.php)))；
temp . onclick = func；
返回温度；
}

巧妙地使用变量名和函数名
这是一项基本的编程技术，它使用完全描述性的变量名和函数，甚至另一个人在看到代码之前也能想到它们执行什么功能。
请记住，使用连字符或大写字母来连接不同的单词是正确的，在这种情况下，由于语言的语法，更典型的是使用大写字母，(例如 getElementsByTagName())。

ChangeFormatDate()；
change _ date _ date()；

评论代码
感谢评论可以让你摆脱不止一个头疼的问题，不如只解决一次问题。

你怎么能在任何一本编程书上看到每一行都有解释其目标的注释呢？

根据重要性区分变量
这一步很简单:将脚本中用到的变量放在代码的头部，这样你就知道在哪里可以找到决定代码结果的变量。

函数 toolLinks () {
var tools，closeWinItem，closeWinLink，printWinItem，printWinLink

//临时变量
var print linklabel = ' print '；
var close linklabel = ' close '；#

tools = document . createelement(' ul ')；
closeWinItem = document . createelement(' Li ')；
closeWinLink = createLink ('# '，closeLinkLabel，close window)；
closewinitem . appendchild(closeWinLink)；
tools . appendchild(closeWinItem)；
print winitem = document . createelement(' Li ')；
printWinLink = createLink ('# '，printLinkLabel，print window)；
printwin item . appendchild(printwin link)；
tools . appendchild(printwin item)；
document.body.appendChild(工具)；
}

分离代码文本
你可以使用 JSON 格式的 texto.js 文件将文本从代码中分离出来。

一个非常有效的例子是:

var locales = {
' en ':{
' homePageAnswerLink ':'立即回答问题'，
'homePageQuestionLink ':'立即提问'，
'contactHoverMessage ':'单击以消息形式发送此信息'，
'loadingMessage ':'正在加载您的数据...'，
'noQAmessage ':'您还没有任何问题或答案'，
'questionsDefault ':'您还没有提出任何问题'，
'answersDefault ':'您还没有回答任何问题'，
' question heading ':' My Questions '，
' answersHeading ':' My Answers '，
'seeAllAnswers ':'查看您的所有答案'，
'seeAllQuestions ':'查看您的所有问题'，
'refresh': 'refresh'
}，
' is ':{
' home page Answer link ':'回答一个问题'，
'homePageQuestionLink ':'提出一个问题'，
'联系方式，
'noQAmessage ':'无问题剩余'，
'问题默认':'问题未回答'，
'答案默认':'无问题剩余'，
'问题标题':'我的问题'，
'答案标题':'我的答案'，
'seeAllAnswers ':'查看所有答案'，
'seeAllQuestions ':'查看所有问题'，
'刷新':'刷新'
}，
' fr

这将允许任何不是程序员的人翻译脚本的文本，只改变标签而不必访问代码。

为你的脚本/库或效果写一份好的文档。好的文档保证了代码的质量，但是问问你自己，因为在任何 API 中都有经典的文档，包含所有可能的属性和参数，但是毫无疑问，最好的事情是用包含一系列可能性的例子来解释。