# 用美人鱼图在(隔离)聚会上给朋友留下深刻印象

> 原文：<https://dev.to/okeeffed/impressing-friends-at-isolation-parties-with-mermaid-diagrams-njm>

*注意:这是我最受欢迎的帖子之一的再分享，最初是在 2018 年底发布到我的旧网站上的。现在再次分享，以保持我每天的连续发帖，这样我就可以发布一些工作特写(在周五，我知道🤦).如果您希望在将来看到一些更整洁的图表内容(使用像 D3 这样的库)，请直接将它们添加到您的 feed 中！*

[点击此处查看博文原文。](https://blog.dennisokeeffe.com/blog/diagrams-with-mermaid/)

这是动态构建 HTML 图表的一个小介绍。

我真的，真的希望能够看到我在赛道上建造的一些筹码，所以我认为这是一个非常方便的探索方式。

**入门**

**TL；**博士

```
create-react-app hello-mermaid
cd hello-mermaid
yarn add mermaid debounce 
```

Enter fullscreen mode Exit fullscreen mode

**基础知识**

对于这个特殊的例子，我决定只使用 create-react-app hello-mermaid 来启动和运行。

一旦安装完毕，就进入文件夹并选择`yarn add mermaid`或`npm install mermaid --save`。

在这种情况下，我还想动态更新图表，所以还添加了 yarn add debounce 或 npm install debounce，因为我将为此使用一个 textarea html 元素，我想在去抖后只触发一次。有关更多信息，请查看我在 React 中关于去抖的原始[博客帖子。](https://blog.dennisokeeffe.com/blog/react-debounce/)

**代码**

我决定开始使用美人鱼的[介绍学习页面。按照说明，我更新了我的`src/App.js`文件，如下所示:](https://mermaidjs.github.io/usage.html)

<figure>[![Updated CRA App.js file](img/d067880b86d03da23325829f2c309a32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SMsi_OdB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AEm4VE_j26ZA-ibM2HCNU6g.png) 

<figcaption>更新 CRA App.js 文件</figcaption>

</figure>

这是怎么回事？首先，我正在导入所需的包。

<figure>[![1\. Import downloaded packages](img/4bf74dc08754acfba625d0a4098d4b34.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v7yLd2br--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AKjxm4QymKl9H_iYEFkBsBg.png) 

<figcaption>1。导入下载的包</figcaption>

</figure>

其次，我已经更新了渲染代码，给了我一个`div`来定位输出的图形和一个我可以添加 markdown 的文本区域:

<figure>[![2\. Updated render() method](img/a07a287f210cca30fea8a824f1423675.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c5P2CACJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AeTBQKE05wFfpypfCl1hbtA.png) 

<figcaption>2。更新了 render()方法</figcaption>

</figure>

第三，我想在 mount 上用一个基本图表初始化图形:

<figure>[![3\. Add in the componentDidMount lifecycle method](img/d1fe3ec2f20ffcba0c4c7a26bafff9b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EmgXgZO8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AXvRvdLdVsngEO_y8Yhap1g.png) 

<figcaption>3。添加 componentDidMount 生命周期方法</figcaption>

</figure>

最后，我添加了`handleChange`函数来尝试更新图表。

<figure>[![4\. Handle any changes to the text box](img/b76c5b32cd1b97d473fa5c44b57ef555.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qC0yvNzr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ak_FpEER8kB7aWIZPOf3L7Q.png) 

<figcaption>4。处理对文本框</figcaption>

</figure>

的任何更改

现在，当我们在终端上运行`yarn start`并且网页打开时，我们得到以下站点:

<figure>[![Aftermath of our updated App.js file](img/4374849997823a3961f5de34c0945d0f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ISftXO46--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1018/1%2Aay1WRUuOalNzLIfZpjSQsQ.png) 

<figcaption>余波我们更新 App.js 文件</figcaption>

</figure>

太好了！现在，由于我们的`handleChange`函数和优雅的处理，我们还可以动态更新图表。

我从网上添加了一些例子，你现在可以复制并粘贴到我们的文本框中，看看它是如何工作的！

```
graph  LR  A[Hard  edge]  -->|Link  text|  B(Round  edge)  B  -->  C{Decision}  C  -->|One|  D[Result  one]  C  -->|Two|  E[Result  two] 
```

Enter fullscreen mode Exit fullscreen mode

<figure>[![Graph LR after updating the textarea](img/6eca45122b5cad6b34507ba92edabf41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fFHfKY5v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AihAdCedCAPFhvq43vUUMmA.png) 

<figcaption>图 LR 后更新 textarea</figcaption>

</figure>

```
sequenceDiagram  Alice->>+John:  Hello  John,  how  are  you?  Alice->>+John:  John,  can  you  hear  me?  John-->>-Alice:  Hi  Alice,  I  can  hear  you!  John-->>-Alice:  I  feel  great! 
```

Enter fullscreen mode Exit fullscreen mode

<figure>[![Sequence diagram](img/50f60ae666a2b4a23dbf103465dc7494.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b5jVodOJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0tXKjrWvtv2utVc0wKwWDQ.png) 

<figcaption>序列图</figcaption>

</figure>

我们甚至可以做像塘鹅图一样复杂的事情！用更复杂的例子更新`textarea`可以给出如下结果:

```
gantt  dateFormat  :YYYY-MM-DD  title  :Adding  GANTT  diagram  functionality  to  mermaid  excludes  :excludes  the  named  dates/days  from  being  included  in  a  charted  task..  (Accepts  specific  dates  in  YYYY-MM-DD  format,  days  of  the  week  ("sunday")  or  "weekends",  but  not  the  word  "weekdays".)  section  A  section  Completed  task  :done,  des1,  2014-01-06,2014-01-08  Active  task  :active,  des2,  2014-01-09,  3d  Future  task  :  des3,  after  des2,  5d  Future  task2  :  des4,  after  des3,  5d  section  Critical  tasks  Completed  task  in  the  critical  line  :crit,  done,  2014-01-06,24h  Implement  parser  and  jison  :crit,  done,  after  des1,  2d  Create  tests  for  parser  :crit,  active,  3d  Future  task  in  critical  line  :crit,  5d  Create  tests  for  renderer  :2d  Add  to  mermaid  :1d  section  Documentation  Describe  gantt  syntax  :active,  a1,  after  des1,  3d  Add  gantt  diagram  to  demo  page  :after  a1  ,  20h  Add  another  diagram  to  demo  page  :doc1,  after  a1  ,  48h  section  Last  section  Describe  gantt  syntax  :after  doc1,  3d  Add  gantt  diagram  to  demo  page  :20h  Add  another  diagram  to  demo  page  :48h 
```

Enter fullscreen mode Exit fullscreen mode

<figure>[![Gantt chart example](img/308db8c895d464be317d35ad810a5e66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZZUUvSMU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AQxOJoPnZewbNyKsniVAI_w.png) 

<figcaption>甘特图示例</figcaption>

</figure>

**接下来的步骤**

非常酷！现在，我们可以轻松地开始创建一些很酷的动态流。接下来呢？要有创意！我计划从输出中解析 markdown 或 html，并使用它来帮助生成重要的报告或伪代码，以帮助构建一些基础设施或数据库模式。

你可以在这里看到回购协议的最终代码。

此外，查看他们的文档，看看你还能做些什么！

[点击此处查看博文原文。](https://www.dennisokeeffe.com/blog/diagrams-with-mermaid)