# 智能查找项目名称

> 原文：<https://dev.to/itsjzt/finding-project-names-smartly--4ln4>

[![cover image](img/2adfa2219f545c09cfd540bdaf9a773e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3WA7tgXY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/chrenr3uchoyrz284mho.jpg)

> 你需要在忘记之前写下你所做的事情，因为你最终会忘记的
> ➡[@韦斯博斯](https://dev.to/wesbos)不记得 _(ツ)_/的原话

长话短说，我总是不擅长命名项目，我曾经命名过 node_mongo_react_app 这样的项目。有些人可以接受，但我总是想要更好的。

> 您仍然可以将项目命名为“random_number_generator”。它们还是很烂，又长又无聊。

感谢 [@mpjme](https://twitter.com/@mpjme) ，让我有了一个更好的项目命名方式，“找一个 Intel 芯片的代号，以此为基础命名你的项目”。

这是一个很好的方法，直到我想，你知道我是一个程序员，我可以自动化这一点。现在让我告诉你这整个想法是如何形成的。

像所有优秀的程序员一样，我把问题分成许多部分，

1.  我需要一份英特尔芯片代号的 JSON 列表
2.  我需要一个随机数来找到一个随机代号
3.  我需要把它放到网上。

对于第一个问题，我试图在谷歌上找到一个英特尔芯片代号的 JSON 列表，但我没有得到任何。现在，我只有一种方法来获得列表，通过解析维基百科页面来制作列表。所以我试着用 cheerio 和 node.js 解析维基百科，但对我来说没用。然后我尝试了蟒蛇配漂亮的汤。我不得不说它们太棒了，我现在可以轻松地解析页面了，但是仍然有一个问题。我得到的回应类似于:

```
<td>skyLake</td>
<td>rapid Ride</td>
<td><a href="blah blah blah">kabyLake</a><td> 
```

Enter fullscreen mode Exit fullscreen mode

我舍入了整个堆栈溢出，以找到从 HTML 元素中提取字符串的正确方法，但不幸的是，我得到了一些不起作用的答案。下一个方法是使用 regex 解析它。但是，我对 regex 不是很了解。现在，最简单的方法是用 string 替换 HTML 标签，忽略那些带有 anchor 标签的标签，即使忽略了它们，剩下的 518 个名字对我来说已经足够了。所以我试了一下。

之后，我只是将输出保存到一个. csv 文件，然后将其转换为 JSON。现在我有了 JSON 文件，我编写了一个 python 脚本来解析 JSON 文件，并从庞大的英特尔芯片名称数组列表中随机给出一个单词。

现在，我打算做一个网络版的。但是问题是，我从来没有用 python 制作过服务器，我用 Nodejs 制作服务器会容易得多。幸运的是，这很容易，因为我有 JSON 中的芯片名称。我可以选择使用数据库或只有一个文件，但我选择了最奇怪的版本，使用一组单词。它真的比数据库或文件快。

它在 Heroku 举办，这里是 https://rapid-ride.herokuapp.com/。我没有做前端，因为我想卷曲它。我在 PowerShell
里做了一个函数

```
function  pr  {  Write-host  (curl  https://rapid-ride.herokuapp.com/).Content  } 
```

Enter fullscreen mode Exit fullscreen mode

痛击你也能做到的家伙，然后把它传给`mkdir`

酷，现在完成了。我很高兴它能帮助我找到 FreeCodeCamp 后端项目的名字。