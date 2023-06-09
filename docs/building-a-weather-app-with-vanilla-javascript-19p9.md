# 用普通 Javascript 构建天气应用程序

> 原文：<https://dev.to/drewclem/building-a-weather-app-with-vanilla-javascript-19p9>

这是我一年前写的一篇旧文章。我只是把一些东西从中号移过来。

[![](img/941b4a8e2e2f3846625915112bdc5085.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PvFQKS1O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AasG6MAH0kHPrI8NqnmTn4w.jpeg)

目前，在我的编码之旅中，我正在学习自由代码营的课程。我最近的一个项目要求我开发一个天气应用程序，该应用程序与我选择的 API 配合使用，根据个人的地理位置提供某个地区的天气和位置信息。我需要满足的另一个“用户故事”是允许用户在华氏温度和摄氏温度之间切换。这是我第一次介绍使用 API，所以我知道会有一个陡峭的学习曲线，但我很高兴有机会最终获得 Javascript 的一些实际应用并构建一个功能产品。

我通过决定我的项目要使用什么 API 来开始我的项目。这些项目是在 codepens 中构建和提交的，为了最大限度地减少我将使用的外部资产的数量，我知道我想要一个带有天气图标的 API。在做了一些研究并看到一些来自其他露营者的反馈后，我决定使用 OpenWeatherMap API。建立一个账户非常简单，他们的免费账户每天最多允许 7200 次通话，每分钟通话上限为 60 次。这显然不会是一个理想的高流量产品，为了这个项目，它会工作得非常出色。

一旦我设置好了，我就可以开始编码了。我从构建我的 HTML 开始。我对架构或设计并不太感兴趣，因为构建的重点是 Javascript。最初的 HTML 标记在 temp 和 location div 中有占位符信息，所以我可以看到我设计的样式，但后来被删除了，因为应用程序会在加载时填充这些 div 的信息。我在 HTML 中做的一件事是，我确信这是其他人的标准做法，我继续将 id 放在我知道以后要用我的 Javascript 定位的元素上。

<figure>[![](img/a52f86f9265bfc2ed8437c1887c7b62c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QSdGUyZ_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1022/1%2AO9MR7ld2eLRb6kSHpSTqPA.png) 

<figcaption>简单&快速 HTML 布局。</figcaption>

</figure>

HTML 就绪后，我花了几分钟时间给它做了一点样式。同样，这个项目的主要目的是使用 Javascript，所以 HTML 和 CSS 得到了最低限度的处理。起初，我想给每个人同样的关注，但我最终太急于进入 JS。

<figure>[![](img/819bc68fd3e526588125d54f871fa971.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M2KO3eNv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/672/1%2AcDs-D43r7ivu6LEcjIgWSg.png) 

<figcaption>简单&居中。</figcaption>

</figure>

现在开始有趣的事情。

这是我第一次在这个层面上使用 API 和 Javascript，所以我必须做大量的阅读、观察和研究来完成这个项目。对我来说不幸的是，这个项目 99.9%的教程、论坛、博客等都是用 jQuery 的。我并不反对这种语言，但是我已经下定决心要使用普通的 JS，所以在这个过程中我所能得到的帮助是有限的。

我从创建空变量开始我的 JS，这些空变量是我以后要调用和操作的信息，它们是 icon、temp 和 location。我还创建了一个名为 APPID 的变量，并在其中存储了我的 API 密钥，这样我就可以在需要时轻松调用它，而不是每次都键入 32 位字母数字序列。一旦我设置好了全局变量，我就写了一个函数，用 API 随后检索的天气信息来更新适当的 div。我能够通过使用字符串和数字来测试这一点，因为我还没有完全设置好 API。

<figure>[![](img/5f50f13f7c8d04798b1487888ebcec91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vkCvpwFj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ANqXOCpDQrW49c5xY4Jc8yQ.png) 

<figcaption>替换输入右侧的=以测试更新功能是否正常工作。</figcaption>

</figure>

随着更新功能正常工作，我设置了一个 window.onload 函数，当屏幕加载时，它将使用 navigator.geolocation 收集用户的地理位置。在 navigator.geolocation 中，我将纬度和经度信息包装在对函数 updateByGeo 的调用中，这是下一步。onload 函数还获取需要定位的 HTML 元素。

<figure>[![](img/bd267666cd42d7815e398c87566112da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---FhIFc5S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Am20svo2I91XuGYpBix-eJA.png) 

<figcaption>Onload 功能聚集用户位置。</figcaption>

</figure>

现在构建 updateByGeo 函数，该函数将创建使用 OpenWeatherMap API 收集天气信息所需的 URL。这个函数相当简单。它只是创建一个名为 URL 的变量，并创建 API 所需的 URL，连接所需的纬度和经度，以及您的 API 键。在函数的结尾，有一个对 sendRequest(URL)的调用，它接受我们刚才在函数中创建的 URL。下一步是构建 sendRequest。

[![](img/8d78c971c4500fe55b8e65ca5f336a9c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZK1mj6zn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AMTzjSCjHQIJ60TvMGd-HuA.png)

构建一个 JSON 发送请求对我来说是一个新领域，我必须依赖大量外部资源来完成这一部分。

[![](img/b000aaa7be2da1763ebbb902d993f502.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--H88f45bw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AWYQ6VcQ6Qtg05pcS6ysCjw.png)

本质上，该函数采用我们在 updateByGeo 函数中创建的 url，该函数使用用户的地理位置，并将它发送到 OpenWeatherMap API，从那里我们可以解析接收到的信息，并将其发送回用户。

还记得之前我们构建了一个用适当的信息更新 div 的函数吗？这就是这些信息的来源！在上面的 weather.icon、weather.loc 和 weather.temp 对象中，您可以看到我们从 API 中检索天气信息的位置。

数据.天气[0]。icon、data.name 和 data.main.temp 从 API 返回的数组中检索信息。它看起来像下面的图像。在所有数据收集的最后，我们告诉 javascript 运行我们之前创建的更新函数。因此，现在我们根据用户的全球定位，用用户的天气状况填充应用程序！

[![](img/c98ebd8339e38d9cca6c4bec30d8f205.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8ER8NkR4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ANGUsWTVlodxe3tKf2LERHg.png)

虽然我们还有一些事情要做。OpenWeatherMap 以开尔文表示温度，因此我们需要将其转换为摄氏度或华氏度。我们还需要允许用户在华氏温度和摄氏温度之间来回切换。

[![](img/9f19c421fb74c9f70ec3be30613dab85.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dxNL_NQQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/336/1%2AB2_KY0IWphbeJ0sT0oDgNQ.png)

您可能已经注意到，我们的 data.main.temp 被包装在一个 K2F 函数中。那是因为我在写这个之前已经写了函数。我不是科学家或数学家，所以为此我不得不谷歌转换公式。一旦你有了这个公式，你可以简单地把它放到一个函数中，调用' return '要记住的一点是，默认设置会给你数字，会给你小数点，所以你可能想把方程放在一个数学公式里。

既然我们已经进入了数学模式，我们将继续构建将摄氏温度转换为华氏温度的函数，反之亦然。同样，快速的谷歌搜索会给你你需要的公式，然后你可以把它放到一个函数里。

[![](img/632eabe49e2b0b148e9887bfb877b42d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--q6aKJB4E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Afwd3HSl2Eo5SqeT9dndNxg.png)

我们快到了！

剩下的就是建立摄氏和华氏之间的切换。我们将用一个 if 语句来实现。我们告诉 javascript，如果你在这个 div 中看到一个 degree 类型在被点击时变成另一个 degree 类型，那么这个函数应该是双向的。

[![](img/a79ea3c305f54a6b400fc38310458251.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gbuj-IUD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AnZYMAuSmJfJ303VDHznB1A.png)

还有一件小事。我们需要一个事件监听器！我们需要告诉 javascript 监听特定的事件，在本例中是点击，以运行我们的切换功能。对我来说，以编写这样一个简单的函数来结束这个项目就像是摘了樱桃。

[![](img/cf7bb2219eb41d11db206e08b50ba4d8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zVwacdv1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AIKPtH8OA00ELeHlm6rzGog.png)

就是这样！我们现在有了一个用普通 javascript 构建的正常运行的天气应用程序！我在这个项目中学到了很多。最值得注意的是，如何使用 API 在某种程度上。我知道每个 API 的工作方式都不同，但是最终接触到一个 API，并且在实际意义上使用 javascript，而不是编写随机的循环或字符串，这是一次非常有益的学习经历。

如果你已经做到了这一步，感谢你花时间阅读这篇文章，我希望它能帮助那些因为寻求帮助而来到这里的人。

我的下一个项目是自由代码营的维基百科浏览器，将会有一个后续的博客。