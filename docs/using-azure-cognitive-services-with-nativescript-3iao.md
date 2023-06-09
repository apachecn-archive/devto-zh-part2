# 通过 NativeScript 使用 Azure 认知服务

> 原文：<https://dev.to/rdlauer/using-azure-cognitive-services-with-nativescript-3iao>

让我们面对事实:电脑赢了！未来实际上掌握在我们最高机器人领导人的手中。

[![i for one welcome our robot overlords](img/48acef46958f0173192d124b0bae915f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--piy8wZk8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/azure-face/robot-overlords.png)

虽然今天并不完全是“终结者”式的现实🤖，事实是“认知服务”正在爆炸。从机器学习到人工智能，再到其他认知 API，这些“思考”服务正在将难以置信的能力卸载到云上。

> 阅读更多关于 Progress 如何加速认知应用开发的信息，也可以阅读一本关于 T2 移动开发新前景的免费电子书。

[微软 Azure](https://azure.microsoft.com/) 是专门的认知 API 的可靠选择，如[视觉](https://azure.microsoft.com/en-us/services/cognitive-services/directory/vision/)、[知识](https://azure.microsoft.com/en-us/services/cognitive-services/directory/know/)、[语言](https://azure.microsoft.com/en-us/services/cognitive-services/directory/lang/)、[语音](https://azure.microsoft.com/en-us/services/cognitive-services/directory/speech/)和[搜索](https://azure.microsoft.com/en-us/services/cognitive-services/directory/search/)。让我们看看如何在用 NativeScript 构建的原生 iOS/Android 应用程序中利用这些 API 之一。

## Azure Face API

其中一个比较好玩的 API 是 Azure 的 [Face API](https://azure.microsoft.com/en-us/services/cognitive-services/face/) 。使用 Face API，你可以向 Azure 发送一张带有人脸的图像，它将返回各种解释的属性:

```
{
    "faceId": "da450841-201c-40a4-b330-2e43ba7f509f",
    "faceRectangle": {
        "top": 87,
        "left": 61,
        "width": 107,
        "height": 107
    },
    "faceAttributes": {
    "gender": "male",
    "age": 21,
        "emotion": {
            "anger": 0,
            "contempt": 0.001,
            "disgust": 0,
            "fear": 0,
            "happiness": 0.762,
            "neutral": 0.237,
            "sadness": 0,
            "surprise": 0
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看如何开始使用 Face API。

## 设置 Azure 帐户

Azure 提供完全免费的 7 天试用，以使用其认知服务(以及更多)。之后，你必须使用信用卡才能继续访问。

[![azure sign up](img/b40edcf795ff4631d54c927ece84943b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h29p3mC9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/azure-face/azure-signup.png)

不过不用担心，因为 Azure 还提供前 30 天 200 美元的信用，并会在你开始收取任何费用之前警告你。

创建好 Azure 账户后，打开 [Azure 门户](https://portal.azure.com/)，导航到**创建资源**，搜索“face”并启用 Face API:

[![search for azure face api](img/68fdb20e0aab395345f95dc5d4575a1b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cfFZZB4M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/azure-face/azure-face.png)

点击 **Create** 按钮后，您将填写一个简短的表格，上面有服务的*名称*、*订阅*类型(预填)、*位置*(选择离您最近的一个！)、*定价层*(选免费的！)，并输入一个*资源组*名称:

[![azure face api details](img/93aa53830e6184a6da55ba06b93e4905.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UXk89Ain--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/azure-face/azure-details.png)

此时，请注意两件事(在您的 NativeScript 应用程序中需要这两件事):

1.  您的 API 密钥。
2.  您的 Azure 端点的 URL。

完成了吗？**太好了！**这就是我们对 Azure 控制台的所有需求。

## 简介 NativeScript

如果你是第一次接触 [NativeScript](https://www.nativescript.org/) ，不要害羞。这是一个很容易理解的框架！

NativeScript 是一个免费的开源框架，用于从单个代码库为 iOS 和 Android ( [和 Web](https://blog.angular.io/apps-that-work-natively-on-the-web-and-mobile-9b26852495e7) )创建真正原生的*应用。*

NativeScript 允许你使用你的网络技能(比如 JavaScript/TypeScript 和 CSS)和流行的 JavaScript 框架(比如 [Angular](https://www.nativescript.org/nativescript-is-how-you-build-native-mobile-apps-with-angular) 和 [Vue.js](https://www.nativescript.org/vue) )来创建移动应用。它在许多方面与 Ionic 相似，但没有为您的 UI 层使用 webviews。

[![nativescript web skills](img/3d66393928dd68d6bd24b2ebac673772.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--80tY74K5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/azure-face/nativescript.png)

> **提示:**开始使用 NativeScript 的最佳方式是浏览 [NativeScript 游乐场](https://play.nativescript.org/?template=play-ng&tutorial=getting-started-ng)上的快速教程。你还可以在[学习 NativeScript Layouts](https://www.nslayouts.com/) 了解 UI 布局系统是如何工作的！

## 构建 NativeScript 应用

让我们构建一个有两个页面/视图的简单应用程序。一个视图列出人物，另一个视图显示个人照片以及从 Azure 返回的面部属性。

> **注:**这款应用的灵感来自于 [Ignacio Fuentes](https://twitter.com/ignacioafuentes) 在 JavaScript NYC meetup 上举办的 meetup 会议。[点击此处](https://livestream.com/internetsociety/js-nyc-feb18?t=1519343631)查看完整课程。

由于 [Meetup API](https://www.meetup.com/meetup_api/) 如此慷慨地返回照片，我们可以很容易地使用它作为我们第一个视图的数据源。

```
https://api.meetup.com/JS-NYC/events/246938509/rsvps?response=yes&only=member 
```

Enter fullscreen mode Exit fullscreen mode

...为所选的 [JavaScript NYC](https://www.meetup.com/JS-NYC/) meetup 返回以下 JSON:

```
{
    "member": {
        "id": 207291595,
        "name": "Adam Chen W.",
        "photo": {
            "id": 271369722,
            "highres_link": "https://secure.meetupstatic.com/photos/member/c/2/3/a/highres_271369722.jpeg",
            "photo_link": "https://secure.meetupstatic.com/photos/member/c/2/3/a/member_271369722.jpeg",
            "thumb_link": "https://secure.meetupstatic.com/photos/member/c/2/3/a/thumb_271369722.jpeg",
            "type": "member",
            "base_url": "https://secure.meetupstatic.com"
        },
        "event_context": {
            "host": false
        }
    }
}, 
```

Enter fullscreen mode Exit fullscreen mode

在 NativeScript 中使用它非常容易，只需一个简单的`http`调用:

```
function fetchAllPeople() {
  http
    .getJSON(
      'https://api.meetup.com/JS-NYC/events/246938509/rsvps?response=yes&only=member'
    )
    .then(
      function(u: any) {
        vm.set('items', u);
      },
      function(e) {
        console.log(e);
      }
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

### 人员列表

然后，我们可以在一个 [NativeScript ListView](https://docs.nativescript.org/ui/list-view) (这是对原生 iOS 和 Android listview 控件的跨平台抽象)中显示这些数据:

```
 <ListView items="{{ items }}" itemTap="fetchPerson" class="list-group">
        <ListView.itemTemplate>
            <GridLayout class="list-group-item" rows="auto" columns="auto, *">
                <Image row="0" col="0" src="{{ member.photo.thumb_link }}" class="thumb img-rounded" />
                <StackLayout row="0" col="1" >
                    <Label text="{{ member.name }}" class="list-group-item-heading" />
                    <Label text="{{ member.photo.type }}" class="list-group-item-text" />
                </StackLayout>
            </GridLayout>
        </ListView.itemTemplate>
    </ListView> 
```

Enter fullscreen mode Exit fullscreen mode

让我们加入一些 CSS 来清理我们的应用程序的外观:

```
.btn {
    font-size: 22;
    background-color: royalblue;
}

.action-bar {
    background-color: royalblue;
    color: white;
}

.lbl-bold {
    font-size: 20;
    font-weight: bold;
}

.lbl {
    font-size: 20;
} 
```

Enter fullscreen mode Exit fullscreen mode

> 是的，你在网络上使用的 CSS 同样被用来设计你的本地移动应用，跨平台！了解更多关于使用 CSS 和 NativeScript 的信息。

这应该会在 iOS 和 Android 上神奇地返回以下屏幕:

[![list of meetup folks](img/d6a4d08c88926b43ad39c6650ded161c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EIXnEZ3V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/azure-face/meetup-list.png)

> 现在是一个很好的时机来注意这个应用程序的完整源代码(用 TypeScript 构建)可以在[这个 GitHub 库](https://github.com/rdlauer/meetupfaces)中获得。如果你正在寻找 Angular 的类似版本，[看看这里](https://github.com/ignaciofuentes/meetupfaces)。

### 带有面部属性的照片细节

当我点击`ListView`中的一个人时，我可以将图像的 URI 发送到 Azure 和 Face API:

```
http.request({
  url:
    'https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,emotion&subscription-key=YOURAPIKEY',
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  content: JSON.stringify({
    url: vm.get('photo') // a reference to your photo
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

...此时，Azure 奇迹发生了，并返回以下内容:

```
{
  "faceId": "1cd4bd2e-c88c-491d-b6f0-7f20d75ae579",
  "faceRectangle": {
    "top": 87,
    "left": 61,
    "width": 107,
    "height": 107
  },
  "faceAttributes": {
    "gender": "male",
    "age": 21,
    "emotion": {
      "anger": 0,
      "contempt": 0.001,
      "disgust": 0,
      "fear": 0,
      "happiness": 0.762,
      "neutral": 0.237,
      "sadness": 0,
      "surprise": 0
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你会看到我们可以检索 Azure 在`age`、`gender`和`mood`(情绪是所有可能情绪中**最高的**数值)的最佳猜测。

假设我们没有错过任何东西(嘿，我有时候是个糟糕的程序员，你永远不知道！)我们应该会看到类似这样的屏幕:

[![meetup detail page](img/6f041ad3cd94c6677d7e63526e210011.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Bg1wDI2I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/azure-face/meetup-detail.png)

我们完成了！😀

## 结论

虽然这是一个相当简单的例子，但它确实让您对认知服务的威力有了一个简要的了解。无论你使用的是 Azure、AWS 还是 [Progress](https://www.progress.com/cognitive-business-apps) ，请放心，认知优先是跨所有设备和体验参与应用开发的未来。