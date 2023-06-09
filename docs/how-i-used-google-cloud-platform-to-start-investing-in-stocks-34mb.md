# 我是如何使用谷歌云平台开始投资股票的

> 原文：<https://dev.to/dansyuqri/how-i-used-google-cloud-platform-to-start-investing-in-stocks-34mb>

在参加了一个朋友推荐的简短讲座后，我对投资产生了兴趣。我决定做些研究，并开始阅读乔尔·格林布拉特的小书《依然跑赢市场》。从这本书里，我找到了一些公式，在我决定是否投资于[新加坡](https://en.wikipedia.org/wiki/Singapore)的公司股票时，这些公式可能对我有用。这篇文章不是为了推销这本书或它的投资策略，而是为了展示以下内容以及我是如何做到的:

1.  通过 python 与 firestorm 交互
2.  在计算引擎上以特定的时间间隔运行 Python 脚本
3.  使用云函数从 firestorm 中检索数据

首先，我创建了一个 Python 脚本，用在[新加坡交易所网站](http://sgx.com/wps/portal/sgxweb/home/!ut/p/a1/04_Sj9CPykssy0xPLMnMz0vMAfGjzOKNHB1NPAycDSz9wwzMDTxD_Z2Cg8PCDANdjYEKIoEKDHAARwNC-sP1o8BK8JhQkBthkO6oqAgAzDYPQQ!!/dl5/d5/L2dBISEvZ0FBIS9nQSEh/)上上市的公司的财务细节和自行计算的比率填充谷歌表单。我发现这很麻烦，因为我必须每天运行 Python 脚本来获取股票的最新价格。然后，我决定将这个日常流程转移到谷歌云平台，这样我就不再需要自己处理日常流程，而是让云来替我处理，:D

下面将解释我是如何做的，希望能帮助那些想像我一样使用谷歌云平台的人。

### **先决条件**

在继续讨论之前，我想指出的是，为了使这篇文章简短，必须先做以下几件事。我也包含了帮助你开始的链接。

1.  [创建谷歌云平台项目](https://cloud.google.com/resource-manager/docs/creating-managing-projects)
2.  [检索服务帐户密钥](https://cloud.google.com/iam/docs/creating-managing-service-account-keys#iam-service-account-keys-create-gcloud)
3.  [创建云引擎虚拟机实例](https://cloud.google.com/compute/docs/instances/create-start-instance)
4.  [设置 Firebase 云功能](https://firebase.google.com/docs/functions/get-started)

### **概述**

[![Overview](img/bdee430f1dba4f0a6b266ab8c3888333.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v_FYSAkb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/0Hu_uP6l37EDMuwCmPb1Ty3NMX9RlGdBB6qU-ne03OdwwXrshRTe6mNd5BeHqyQL7uLTeKtcuc9COVvv75MA%3Dw1920-h920)

从上面的图中，我唯一要做的事情就是通过云函数 HTTP API 发出 GET 请求，它将返回 Firestore 中存储的所有已经计算好的公式和值。本质上，步骤 1、2 和 3 涉及我创建的 Python 脚本。通过使用[请求库](http://docs.python-requests.org/en/master/)可以简单地完成步骤 1 和 2。

### **通过 Python 与 Firestore 互动**

Firestore 使用集合、文档和字段的概念来存储您想要的数据。举个例子，用图书馆做类比，如果你有一个书架的书，在 Firestore 看来，那就是收藏。书籍本身就是文档，书中的每一页都是一个独立的领域。每个文档也可以有自己的集合，但是我不会深入讨论。

```
shelf [collection]
|--book1 [document]
  |-- page1 [field]
  |-- page2 [field]
|--book2 [document]
  |-- page1 [field] 
```

要从 Python 脚本中交互和更新云 Firestore 上的数据，首先必须通过`pip install google-cloud-firestore`安装 Google Cloud Firestore 库。以下是用您以前检索的服务帐户密钥初始化 Firestore 的代码片段。

```
from google.cloud import firestore
db = firestore.Client.from_service_account_json('/path/to/service/key') 
```

事实上就是这样！要将数据写入 Firestore，只需执行以下操作:

```
doc_ref = db.collection(u'name_of_collection').document(u'name_of_document')
doc_ref.set(data_to_update) 
```

`data_to_update`是一个 Python 字典，它保存了您希望 Firestore 文档保存的键和相应的值。`.set()`允许您在文档中更新或插入新的字段。对我自己来说，我把公司名称、股票价格、财务比率和其他字段放在这里。

这里需要注意的一点是，即使文档或集合尚不存在，`.set()`函数也会自动为您创建集合和文档，并用前面提到的字段填充文档。

### **在计算引擎上运行 Python 脚本**

有几种方法可以将 Python 脚本推送到 VM 实例。我的做法是在我的谷歌云项目中创建一个存储库，并把它推到那里。我创建存储库的原因是因为我仍然想要某种形式的版本控制，因为我了解自己，我喜欢做出改变，探索不同的方法在我的代码中做事情，最终让自己感到困惑。尽管这是一个小项目，但我觉得对我个人来说这是一个很好的实践。然后，我通过 SSH 远程访问 VM 实例，并将存储库克隆到实例中。

现在是 Python 脚本的调度。最初，我认为每 30 分钟调用一次 Python 脚本是个好主意。然而，经过一番考虑后，我觉得将脚本安排在下午 6 点(GMT +0800)运行是最理想的情况，因为新加坡交易所上午 9 点开门，下午 5 点关门，我下班后只有时间查看股票价格。

要安排 Python 脚本在特定时间间隔或特定时间运行，可以像我一样使用 Cron 作业。在 VM 实例的 SSH 会话中，使用`crontab -e`命令编辑用户的 Crontab。在文件的最后，在你的时间表中，格式如下

```
# m h  dom mon dow   command
0 10 * * 1-5 cd /path/to/python/folder && python main.py 
```

上面的代码片段在 UTC 时间上午 10 点(也就是 SGT 时间下午 6 点)运行 Python 脚本，由`1-5`段表示。如果您希望您的脚本在每个时间间隔后运行，您可以改为执行以下操作:

```
# Runs the command every hour at the 0th minute
0 */1 * * * <some command>

# Runs the command at the 0th minute every day
0 * */1 * * <some command> 
```

注意:我第一次在 VM 实例中使用 Crontab 时犯的一个错误如下:

```
# Runs the command every minute after every hour
* */1 * * * <some command> 
```

我的意图是每小时运行一次。但是我错过了克隆工作的第一分钟。所以它每小时每分钟都在**运行脚本。**我的脚本每次被调用都要花大约 3 分钟来运行。我不介意相对较长的运行时间。但是，由于脚本每分钟运行一次，每次运行需要 3 分钟才能完成...好吧，你可以算算。愚蠢的我试图找出为什么我的虚拟机实例上的 CPU 使用率总是在 150-200%,而我甚至不能通过 SSH 访问它。那是一堂有趣的课:P

### **使用云功能从 Firestore 检索数据**

对于这一步，我将 Google Cloud 项目链接到 Firebase。我这样做的原因是为了未来可能的版本，我可以在 Firebase Hosting 上托管一个网站，该网站利用来自云 Firestore 的数据，允许任何人一眼就可以查看财务细节。另一个原因也是因为我更熟悉 Firebase 和那里对云功能的要求。

我通过`npm install --save express`把 Express.js 安装到我的云函数文件夹里。Express.js 允许我轻松地创建 web APIs，因为我需要多个端点来从我拥有的 Firestore 中检索各种公司信息。

```
var  db  =  admin.firestore();

const  express  =  require("express");
const  app  =  express();

app.get('/:nameOfDocument',( req, res)=>{
    const  nameOfDocument  =  req.params.nameOfDocument;
    var  firestoreRef  =  db.collection("name_of_collection").doc(nameOfDocument);
    res.setHeader('Content-Type', 'application/json');
    firestoreRef.get().then((snapshot) => {
    if (snapshot.exists) {
        var  returnObj  =  snapshot.data();
        return  res.status(200).json(returnObj);
    }
    else {
        return  res.status(422).json({error:"Invalid document name"});
    }
    }).catch(errorObject  => {
        return  res.status(500).json({error:"Internal Server Error"});
    });
})
exports.api  =  functions.https.onRequest(app); 
```

上面的片段是对正在发生的事情的逐步解释。首先，对 Firestore 的访问由`var db = admin.firestore();`初始化。

```
app.get('/:nameOfDocument',( req, res)=>{
...
} 
```

上面告诉 Express 我们想要创建一个带有端点`'/:nameOfDocument'`的 GET 请求，其中`:nameOfDocument`是 URL 中的一个参数。`req`和`res`分别是接收和将要发送的请求和响应对象。目前，只有`res`在使用，但是以后会有更多的介绍。

```
const nameOfDocument = req.params.nameOfDocument; 
```

这一行从 URL 中获取参数，在本例中为`:nameOfDocument`，并将其存储为一个名为`nameOfDocument`的变量，该变量将在下一行中使用。

```
var firestoreRef = db.collection("name_of_collection").doc(nameOfDocument); 
```

这一行实际上创建了对文档`nameOfDocument`的引用。集合名称当前不是变量。您也可以使用 include 集合名称作为参数，如下:

```
app.get('/:nameOfCollection/:nameOfDocument',( req, res)=>{
    const nameOfDocument = req.params.nameOfDocument;
    const nameOfCollection= req.params.nameOfCollection;
    var firestoreRef = db.collection(nameOfCollection).doc(nameOfDocument);
    ...
} 
```

这样，您可以在 URL 中指定它，而不必修改代码。

```
firestoreRef.get().then((snapshot)  =>  {
    if  (snapshot.exists)  {  
    var returnObj = snapshot.data();  
    return res.status(200).json(returnObj);  
    }
    ...
} 
```

上面的代码段采用前面提到的引用，并检查它是否存在。这一点很重要，因为用户可能会意外地键入错误的文档或集合名称，我们希望返回正确的响应。`snapshot.data()`检索所有的字段键值对，并将其放入名为`returnObj`的对象中，然后我们将其作为 JSON 对象返回，状态代码为 200。

```
exports.api  =  functions.https.onRequest(app); 
```

这一行告诉云函数，当向`<cloudfunctions.net url>/api`发出请求时，请求应该被传递给名为`app`的 Express 对象，并根据在`app`对象本身中指定的端点进行相应的处理。

就是这样！现在，您可以通过 Firebase Cloud Functions 页面上提供的链接调用您的云函数，这将从 Firestore 中检索您想要处理的相关数据。

P.S .这是我的第一篇教程/个人经验贴。请让我知道什么可以改进，以及我如何才能成为一名更好的程序员。欢迎所有建设性的反馈。感谢您通读我的帖子！:D