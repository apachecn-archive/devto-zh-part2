# JSON 简化版

> 原文：<https://dev.to/programliftoff/json-simplified--23g2>

*本文原载于[programmingliftoff.com](http://programmingliftoff.com/)上，为 [JSON 简体](http://programmingliftoff.com/json-simplified/)。*

[![Website with JSON data](img/08333b6b2c8635e862443a6f051e3de1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Qfe3vebm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xf8uquh4inpw27s1b0z7.png)

JSON 是当今网络的重要组成部分。这对于任何用 JavaScript 编程的人来说都是不可避免的。JSON 代表 JavaScript 对象符号。JavaScript 中使用 AJAX 请求向服务器请求 JSON 数据。AJAX 代表异步 JavaScript 和 XML。XML 是一种曾经代替 JSON 使用的数据格式，但是今天 JSON 已经成为这个领域的主导。在本教程的最后，我们将有一个简单的本地网页，它发送一个 AJAX 请求，请求我们上传到服务器的 JSON 数据，然后在本地网页上显示这些数据。我们开始吧！
***注**:如果你从视频中学得更好，点击这里观看视频演练: [JSON 简化版 YouTube 视频](https://youtu.be/dK90uu-nycA)。*

首先让我们来看看一些真实的 JSON。

```
{  "class":  "Biology",  "homework":  "Memorize cell structure",  "classId":  1  } 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，JSON 看起来像一个 JavaScript 对象(这一定是它被称为 JavaScript 对象符号的原因！).关于 JSON 的一些特殊注意事项是，所有的对象键(上面代码片段中的 class、have 和 classId)必须用双引号括起来。此外，如果值是一个字符串，它也必须有双引号(单引号无效)。然而，数字没有也不应该用双引号括起来。

好了，现在我们已经看到了一些 JSON，让我们有一些乐趣！

我在这里上传了一些 JSON 到 GitHub:[https://GitHub . com/programming-lift off/JSON-simplified/blob/master/JSON data . JSON](https://github.com/programming-liftoff/json-simplified/blob/master/jsondata.json)。我们将创建一个简单的网页，从 GitHub repo 中获取 JSON 数据，并显示在网页中。听起来很棒，对吧！？

在你喜欢的文本编辑器中，创建一个名为*index.html*的文件。在它过去下面的 HTML:

```
<html>
<head></head>
<body>
  <div id='page-data'></div>
  <script>
  var xhttp = new XMLHttpRequest();
  xhttp.onreadystatechange = function() {
      if (this.readyState == 4 && this.status == 200) {
        var pageData = '';
        var data = JSON.parse(xhttp.responseText);
        pageData = '<h1>' + data.student.name + '</h1>';
        pageData += '<h4>Let\'s learn some ' + data.class + '!</h4>';
        pageData += '<h4>For homework: ' + data.homework[0] + '</h4>';
        document.getElementById("page-data").innerHTML = pageData;
        console.log('Done!');
      }
  };
  xhttp.open("GET", "https://raw.githubusercontent.com/programming-liftoff/json-simplified/master/jsondata.json", true);
  xhttp.send();
  console.log('here')
  </script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

然后在浏览器中打开该文件。这通常可以通过在 Finder 或 Windows 资源管理器中双击文件来完成。您也可以尝试在文本编辑器中右键单击该文件，选择“复制完整路径”，然后将该路径粘贴到您的 web 浏览器中(例如 Chrome)。您现在应该会看到以下网页

[![Image of simple webpage displaying JSON data](img/c447085e1b277b86aea6a750bc858bbd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MzbHJa9U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://programmingliftoff.com/wp-content/uploads/2018/02/json-simplified-webpage.png)

现在让我解释一下这是怎么回事。

首先，行`var xhttp = new XMLHttpRequest();`创建一个新的请求对象。XML 数据格式曾经比 JSON 更受欢迎，但是现在 JSON 用得更多了，我们只能使用名称`XMLHttpRequest`。该对象可用于从文件或服务器请求 JSON 数据。

让我们跳到第 21 行`xhttp.open("GET", "[https://raw.githubusercontent.com/programming-liftoff/json-simplified/master/jsondata.json](https://raw.githubusercontent.com/programming-liftoff/json-simplified/master/jsondata.json)", true);`。这一行指定了我们想要发送的请求的类型。我们发送一个`GET`请求，因为我们想从服务器获取数据。另一个选项是`POST`，它可以向服务器发送数据。接下来，我们指定 JSON 数据的位置。在这种情况下，它位于 GitHub 存储库中。最后，我们通过`true`。这意味着请求应该异步执行。异步执行请求是首选方法，因为它允许在请求等待来自服务器的数据响应时加载 web 页面的其余部分。因此，这是一个 AJAX(异步 JavaScript 和 XML)请求。尽管如前所述，由于我们使用 JSON 代替 XML，您可以将其视为一个 AJAJ 请求。顺其自然，称它为 AJAX，因为每个人都这么叫它(所以它一定是对的，对吗？).还要注意，实际发送请求需要使用`xhttp.send();`。

现在我们来看看第 7 行的`xhttp.onreadystatechange`函数。该函数的第一行是一个 if 语句`if (this.readyState == 4 && this.status == 200)`。这就是说，当请求的状态变为`4`，状态为`200`时，应该执行`if`语句中的代码。具有值`4`的就绪状态意味着请求已经完成，而状态`200`意味着请求成功。换句话说，我们已经收到了 JSON 数据。查看 [Mozilla 的帖子](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/readyState)和 [W3School 的帖子](https://www.w3schools.com/tags/ref_httpmessages.asp)分别获得更多关于就绪状态和 HTTP 状态代码的信息。

最后在第 11 行，行`var data = JSON.parse(xhttp.responseText);`检索 JSON 响应，使用`JSON.parse`将响应字符串转换成 JSON 对象，并将该 JSON 对象存储在`data`变量中。该对象现在可以用作 JavaScript 对象，数据可以显示在网页上。

### 奖金:

尝试将第 21 行的`true`改为`false`，然后刷新页面。你注意到什么变化了吗？网页上没有任何明显的变化。但是打开控制台(在 Chrome 中，右键点击网页，选择‘Inspect’，点击‘Console’选项卡)。并再次刷新页面。你会注意到`console.log`语句的顺序发生了变化。这是因为当调用不是异步的(`false`)时，页面在继续之前会等待请求完成。因此“完成！”在“这里”之前打印。当调用异步时，页面发送请求，在服务器响应请求之前有时间打印“here”。

当参数设置为`false`时，您可能还会注意到控制台中的警告。这是浏览器告诉你这样做不是一个好主意，因为如果它不是异步的，页面加载会更慢！

现在，您已经学会了 JSON，并且使用了 AJAX。我把这个挑战留给你。如果您没有免费帐户，请使用 [GitHub](https://github.com/) 创建一个。然后创建一个新的存储库，并将本教程中的 JSON 数据复制到该存储库中的一个文件中。接下来，单击“Raw”按钮获取原始 JSON 数据的 URL。最后，将第 21 行中的 URL 更新为您的 URL。现在，您正在将 JSON 从 GitHub 服务器空间拖到您的网页上。厉害！

现在继续修改 JSON 和 web 页面，对其进行更多的修改。如果你发布了你的站点，请在下面随意发表评论，这样其他人就可以看到并得到启发！

在任何地方卡住，或者想在视频中再次听到所有内容的解释？点击下面的链接观看所有这些步骤的视频演示！ [JSON 简化版 YouTube 视频](https://youtu.be/dK90uu-nycA)

感谢阅读！

-Andrew @编程升空
**网址:**[programmingliftoff.com](https://programmingliftoff.com)