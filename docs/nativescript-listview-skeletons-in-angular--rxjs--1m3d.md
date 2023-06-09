# 带角度和 RxJS ☠️的本地脚本 ListView 框架屏幕

> 原文：<https://dev.to/_joshsommer/nativescript-listview-skeletons-in-angular--rxjs--1m3d>

万圣节就要到了，所以现在似乎是讨论我最近开始更多使用的一项技术的最佳时机:骨架屏幕。

### 骷髅屏幕

如果你在过去几年里使用过任何一个主要的网站，你可能会看到骷髅屏幕。我特别喜欢它们，因为它们让您在数据加载之前知道会发生什么。对于移动设备，它们对我来说更有意义，因为有时用户的数据连接可能特别慢。

<figure>[![Skeletons being used on Slack](img/2d8fb77170fa7825d2b7fbf05ee84532.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yih_GP3P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t82y10c5vl7w5l2vk16j.jpg) 

<figcaption>枯骨被用在懈怠上。</figcaption>

</figure>

### NativeScript

如果你不熟悉 NativeScript，你真的应该去看看。NativeScript 是我最喜欢的移动开发框架，介于 100%访问原生 API、Angular 支持和他们的在线平台之间，你可以在浏览器中编写代码，并在手机上进行测试。这些特性和更多的特性无疑是开发多平台应用程序的最佳方式。

### ListView 骨架中的 NativeScript

通常我们可以用一个`ngIf`和一个`else`来表示，比如:

```
<StackLayout *ngIf="data$ | async; else skeletons">
  <!---View of data after Loaded -->
</StackLayout>

<ng-template #skeletons>
  <!--- Skeletons Here -->
</ng-template> 
```

这样，在我们的 observable with my response 对象发出一个值之前，它将显示一个不同的模板。这种模式对于 NativeScript 和 Web 都非常有效(用`div`替换`StackLayout`)。

然而，对于这个用例，我们有一个很长的条目列表，所以出于性能原因，最好使用 ListView。因为我们希望我们的骨架尽可能地模仿用户界面，所以我们也希望它们出现在列表视图中。

<figure>

[![Screenshot of my ListView](img/7e50787ecdc5c77792713eb80f2df9c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--18LBHUTB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hr8882a826pfgu3zbtvo.png)

<figcaption>Screenshot of my ListView</figcaption>

</figure>

如果我们愿意，我们可以使用与上面相同的模式，在我的页面上有两个单独的列表视图。一个是内容，一个是我的骨架。这不是一个非常有效的使用布局的方法，而且会使用户界面的改变更加困难，因为它会使最终的结果与骨架分离。

### ListView 模板选择器

NativeScript 的 ListView 有一个简单的方法来在模板之间进行切换。通过定义一个`itemTemplateSelector`回调函数并给每个模板一个带有`nsTemplateKey`属性的键，我们能够交换布局。每次 ListView 加载向用户显示的项目时，都会触发模板选择器函数，并确定该项目的模板。我们需要做的最后一件事是通过输入将模板选择器回调传递给 ListView。

```
<ListView [items]="genres$ | async" [itemTemplateSelector]="templateSelector">
    <ng-template nsTemplateKey="template" let-item="item" let-index="index">
        <GridLayout class="music-genre" rows="*" columns="55,*">
        <Label row="0" col="0" [text]="index + 1" class="number"></Label>
        <Label row="0" col="1" [text]="item" class="genre"></Label>
        </GridLayout>
    </ng-template>
    <ng-template nsTemplateKey="skeleton">
        <GridLayout class="music-genre skeleton" rows="*" columns="55,*">
            <Label row="0" col="0" text=" " class="number number-skeleton"></Label>
            <Label row="0" col="1" text=" " class="genre genre-skeleton"></Label>
        </GridLayout>
    </ng-template>
</ListView> 
```

*我们的 NativeScript Angular 模板带有一个 ListView，可以在骨架和普通模板之间切换*

一个`itemTemplateSelector`回调函数有 3 个参数，当前条目、条目索引和当前列表中的所有条目。

```
 templateSelector(item: any, index: number, items: any[]) {
    return 'template';
}; 
```

*模板选择器函数，总是返回`template`* 的类型

### RxJS 开始符

使用上面的模板选择器，我们仍然只能看到流派项目。我们需要一种方法来首先加载一个“骨架”数组，然后当我们从服务器获得响应时，用结果数据数组替换骨架数组。这就是 RxJS 的`startWith`操作符发挥作用的地方。我们需要做的是将一个对象数组传递给管道操作符`startsWith`。然后，当我们在组件`ngOnInit`函数中设置`genres$`可观察属性时，我们“管道化”这个操作符。现在，当我们订阅`genres$`可观测值时，它会发出两个值。首先是骨架数组，然后是 HTTP 请求的结果。

我喜欢做的是声明一个`ISkeleton`接口，它是一个具有布尔属性的对象，名为`skeleton`。然后创建一个对象数组，每个对象的`skeleton`属性都设置为 true。这是传递给`startsWith`操作者的数组，由`genres$`观测者首先发出。

```
 interface ISkeleton{
    skeleton: boolean;
}

const SKELETONS: ISkeleton[]  = [
    { skeleton: true },
    { skeleton: true }
    ... // since this is for a listView I have quite a few of these. 
];

@Component({...})
export class HomeComponent implements OnInit {

    genres$: Observable<ISkeleton[] | string[]>;

    constructor(private genreService: GenreService) {
    }

    ngOnInit() {
        // Get a list of genre's from the genre service. but first, start by 
        // emmiting the SKELETONS array.
        this.genres$ = this.genreService.getGenres()
            .pipe(startWith(SKELETONS));
    } 
```

现在一切都准备好了。然而，如果我们运行我们的应用程序，我们仍然看不到我们的骨架模板。最后一步是更新我们的模板选择器函数。我们需要做的就是检查传递给它的`item`对象是否有 skeletons 属性。如果返回 truthy，那么让我们的模板选择器函数返回‘skeleton’的键。

```
templateSelector(item) {
    if (item.skeleton) {
        return 'skeleton';
    }
    return 'template';
}; 
```

### 成品

[![All tied together](img/fe1f9539179d4894aec2de34291139e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LM24PD_0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/79291ev5eya6zt8o8egj.gif)

正如你在上面的 gif 中看到的，当用户第一次进入这个视图时，他们会立即看到一个骨骼列表。然后，一旦 HTTP 请求完成，视图就会切换到音乐流派列表。我们已经准备好了骨架的布局。

以这种方式设置我们的骨架屏幕只是一个开始，你可以用这种模式做更多的事情，尤其是在动画方面。由于 NativeScript 的 ListView、Angular 和 RxJS 的强大功能，我们有一个非常简洁的代码基础来构建。

你可以在 [NativeScript Playground 这里](https://play.nativescript.org/?template=play-ng&id=1xU1Fw&v=18)查看一个工作样本。*只是一个免责声明，它目前在 iOS 上看起来很好，因为我目前没有 Android 手机可以测试。*

感谢阅读，我希望你喜欢我对这个模式的看法。如果你有，请与你的朋友和同事分享。我真的很喜欢你在下面评论中的任何反馈。或者，如果你对 NativeScript 中的框架屏幕有自己的看法，让我们也看看吧！