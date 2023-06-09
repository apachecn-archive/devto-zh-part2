# 简单的角度“加载？”服务程序库

> 原文：<https://dev.to/johncarroll/simple-angular-isloading-service-33d0>

[IsLoadingService](https://gitlab.com/service-work/is-loading) 是一个简单的角度服务，用于跟踪你的应用或部分应用是否正在加载。通过订阅它的`isLoading$()`方法，您可以很容易地知道何时显示加载指示器。

最基本的是，您可以将服务导入到根组件中，并使用`ngIf` + the `AsyncPipe`在页面导航期间显示加载指示器。

示例:

```
import { IsLoadingService } from '@service-work/is-loading';

@Component({
  selector: 'app-root',
  template: `
    <mat-progress-bar
      *ngIf="isLoading | async"
      mode="indeterminate"
      color="warn"
      style="position: absolute; top: 0; z-index: 100;"
    >
    </mat-progress-bar>

    <router-outlet></router-outlet>
  `,
})
export class AppComponent {
  isLoading: Observable<boolean>;

  constructor(
    private isLoadingService: IsLoadingService,
    private router: Router,
  ) {}

  ngOnInit() {
    this.isLoading = this.isLoadingService.isLoading$();

    this.router.events
      .pipe(
        filter(
          event =>
            event instanceof NavigationStart ||
            event instanceof NavigationEnd ||
            event instanceof NavigationCancel ||
            event instanceof NavigationError,
        ),
      )
      .subscribe(event => {
        // if it's the start of navigation, `add()` a loading indicator
        if (event instanceof NavigationStart) {
          this.isLoadingService.add();
          return;
        }

        // else navigation has ended, so `remove()` a loading indicator
        this.isLoadingService.remove();
      });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果想手动指示某个东西正在加载，可以调用`loadingService.add()`方法，然后在加载已经停止时调用`loadingService.remove()`方法。

如果你多次调用`loadingService.add()`(因为正在加载多个东西)，`isLoading$()`将保持为真，直到你调用`remove()`相同的次数。

在内部，IsLoadingService 维护一个加载指示器数组。每当你调用`add()`时，它将一个指示器推到堆栈上，而`remove()`从堆栈中移除一个指示器。`isLoading$()`只要栈上有任何装载指示符，就为真。

您还可以将订阅或承诺参数传递给`loadingService.add(subscription)`。在这种情况下，加载服务会将一个加载指示符推送到堆栈上，然后在订阅关闭/承诺解决时自动移除它(即，您不需要手动调用`remove()`)。

### 高级用法

对于更高级的场景，您可以使用包含`key`属性的 options 对象来调用`add()`。钥匙可以让你分别跟踪不同物品的装载情况。任何真值都可以用作键。`add()`的按键选项旨在与`isLoading$()`和`remove()`的`key`选项配合使用。

示例:

```
class MyCustomComponent implements OnInit, AfterViewInit {
  constructor(
    private loadingService: IsLoadingService,
    private myCustomDataService: MyCustomDataService,
  ) {}

  ngOnInit() {
    this.loadingService.add({key: MyCustomComponent})

    const subscription = this.myCustomDataService.getData().subscribe()

    // Note, we don't need to call remove() when calling
    // add() with a subscription
    this.loadingService.add(subscription, {
      key: 'getting-data'
    })
  }

  ngAfterViewInit() {
    this.loadingService.remove({key: MyCustomComponent})
  }

  isDataLoading(): boolean {
    // the `isLoading()` method (without the `$`) returns a boolean
    // instead of an observable
    return this.loadingService.isLoading({key: 'getting-data'})
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 包装完毕

这项服务非常简单，但我发现自己经常在不同的应用程序中重新创建它，所以我决定将其打包并发布。它还有助于解决我遇到的一些角度问题，通过去抖动和消除`isLoading$`辐射，否则会扰乱 Angular 的变化检测。

就我个人而言，我将它连接到我的应用程序根组件中的全局负载指示器，并让它自动响应路由器导航。此外，当从 API 加载数据时，我通常希望显示相同的应用程序加载指示器，所以我将简单地添加带有`loadingService.add(subOne)` + `loadingService.add(subTwo)`的数据获取订阅。尽管如果我想为数据获取显示不同的加载指示器，我可以在调用`add()`和`isLoading$()`时使用可选的`key`参数。

## [可以在 GitLab 上查看](https://gitlab.com/service-work/is-loading)