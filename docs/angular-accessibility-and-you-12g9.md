# 棱角分明，无障碍，你呢

> 原文：<https://dev.to/mattnmoore/angular-accessibility-and-you-12g9>

这篇文章背后的动机是，实现可访问性技术的技术资源非常稀少，尤其是像 Angular 这样的现代 Javascript 框架。似乎还有一种误解，认为现代 Javascript 框架阻碍了您使 web 应用程序可访问的能力。虽然使用 Angular 这样的框架存在一些易访问性陷阱，但也有许多好处，我想回顾一下我在构建 Angular powered 平台时学到的一些经验，该平台力求尽可能地具有易访问性。

我还想指出，web 可访问性有很多方面，但本文主要关注键盘和屏幕阅读器的优化。

## 目录

1.  SPA 注意事项
    1.  路线变更
    2.  表单验证
    3.  延迟加载的内容
    4.  通知
    5.  页面标题
2.  UI 包陷阱
    1.  数据表
    2.  引导程序
    3.  树组件
    4.  拨动开关
3.  方法
    1.  跳过链接
    2.  焦点管理
    3.  页面标题
4.  测试
5.  资源

## SPA 注意事项

不管你使用什么技术，如果你正在构建一个单页面应用程序(SPA ),你必须做一些额外的考虑来确保它是可访问的。在本节中，我们将简要介绍其中的一些领域。稍后，在菜谱部分，我们将详细讨论如何使用 Angular 解决这些问题。

### 1。路线变更

传统 web 应用程序和 SPA 的最大区别是处理导航。在传统的 web 应用程序中，在每次页面更改和大多数用户交互(如表单提交)时，web 页面会重新加载，焦点会重置，页面标题也会更改(如果您使用了正确的页面标题)。如果你的用户使用的是屏幕阅读器，它会显示新的页面标题，让他们知道新页面已经加载。在 SPA 中，您必须手动管理它。

我见过一些实现这一点的方法，但最简单的方法是当页面的 H1 元素出现在屏幕上时，将焦点设置为它。稍后，我将介绍如何使用 Angular 实现这一点。

另一个选择是使用一个 div 来显示一个新的页面已经加载，它只出现在屏幕阅读器上。类似于“联系人页面现在在屏幕上”，并在每次路由更改时将焦点设置到该 div。

您还可以在每次路由更改时将焦点设置为 skip 链接或标题，以模拟传统 web 应用程序的行为。这些都是有效的选项，但是最佳实践将取决于您的特定用例。

### 2。表单验证

表单验证是构建可访问的 SPA 时要考虑的另一个重要方面。与路线变更类似，焦点管理是关键。为了确保良好的用户体验，请确保在提交后将焦点设置为任何错误消息。有用的错误消息清楚地链接到特定的输入字段，并带有修复错误的明确指示。

我注意到 SPA 表单的另一个常见问题是，在表单有效之前，提交按钮是禁用的。不要这样！您的用户应该能够在任何状态下提交您的表单，如果表单无效，它应该提供如何修复它的明确指导。这将极大地提高屏幕阅读器和非屏幕阅读器用户的可用性。

这里有一个关于使用 Angular 实现可访问表单的很棒的帖子:[https://simply accessible . com/article/accessible-forms-Angular/](https://simplyaccessible.com/article/accessible-forms-angular/)

### 3。延迟加载的内容

这是一个很大的问题，对于传统的 web 应用程序来说，你通常不必考虑这个问题。有各种各样的解决方案可以解决这个问题，这是我在 Dinolytics 上仍在完善的一个方案。

对 SPA 来说，延迟加载所有内容是一种非常流行的方法，因为它极大地改善了初始页面加载。如果你有很多不同的内容区域，你可以把一个大的 API 调用分成几个小的。问题是这些内容不会呈现给屏幕阅读器用户。如果他们的连接速度较慢，他们可能会在内容加载之前浏览页面，并得出结论认为这是一个空白页面。

我发现的最好的解决方案是使用 aria-live 区域。您可以使用 aria-live 区域来通知屏幕阅读器用户何时加载了某些内容。但是，不要将所有内容都放入 aria live 区域，这将导致整个页面在加载时被读取。坚持使用简短的描述性信息，如“内容正在加载”和“内容已在屏幕上”。如果有疑问，用屏幕阅读器测试！

如果你想了解更多关于 aria-live 区域和可用选项的信息，请查看本文:[http://juice studio . com/article/wai-aria _ live-regions _ updated . PHP](http://juicystudio.com/article/wai-aria_live-regions_updated.php)

### 4。通知

有两种主要方法来处理应用程序范围内的通知，例如键盘和屏幕阅读器用户的 toast 通知。

第一种是使用咏叹调现场区域。您可以将 aria-live 属性赋予您的 toasts 容器，任何通知都会呈现给屏幕阅读器用户。

另一个选择是使用焦点。您可以在通知出现时设置焦点，它也会显示给屏幕阅读器用户。您可能希望您的通知保持在屏幕上，直到它不再接收焦点，这将防止通知被意外取消。

### 5。页面标题

出于几个原因，页面标题对于可访问性很重要。一般来说，当页面加载时，它是屏幕阅读器读取的第一个东西，它应该让用户知道页面上会有什么。

在往返网站或应用程序中，页面标题是在浏览器加载页面之前确定的，因为内容是由后端提供的。但是，如果您正在构建一个 SPA，您必须基于前端的路由动态设置页面标题。

此外，屏幕阅读器不会在随后的页面更改中读取页面标题，因为页面实际上并没有更改，Javascript 只是交换了同一页面上的内容。

管理标题仍然很重要，因为如果用户切换到不同的选项卡并返回，如果他们移动到不同的窗口，以及在第一页加载时，都会读取标题。

如果您已经处理好了页面标题的管理，那么如果您出于 SEO 目的切换到服务器端呈现，您也会看到好处，因为页面标题将被正确设置。

## UI 包陷阱

UI 包有自己的部分，因为在我第一次对 Dinolytics 进行可访问性审计时，它们是我遇到的最大问题。在 MVP 阶段，我处于快速原型模式，我只是抓住任何看起来不错的包，做我需要的。这包括数据表、树组件、开关切换、组合框和角度引导包。在最好的情况下，这些包中的一些具有较小的可访问性问题，在最坏的情况下，它们完全不可访问。测试您带入项目的任何第三方包的可访问性是非常重要的。

减轻未来问题的一个选择是将所有第三方组件包装在您自己的组件中，并在整个应用程序中使用。这样，如果你因为任何原因不得不切换组件，可访问性或其他原因，你只需要改变你的包装组件的内部，这些改变将在应用范围内免费进行。在我看来，这是使用 Angular 这样的东西最大的好处之一。

### 1。数据表

这是到目前为止最大的问题领域，当谈到不可访问的角包。我可能遗漏了一些包，如果是的话，请在 Twitter 帖子的[中告诉我，但是在我几次搜索中，我只找到了一个满足我们所有要求的包，甚至可以远程访问。这个包仍然有一些小的可访问性和响应问题，我已经对它进行了一些改进。《叉子》还没有在 NPM 上发行，但我打算很快出版。同时，您可以查看原始包，因为就可访问性而言，它是迄今为止最好的 Angular Datatable 包。](https://twitter.com/dinolytics/status/1055564328982597632)

原文:[https://github.com/brunano21/angular-4-data-table#readme](https://github.com/brunano21/angular-4-data-table#readme)
T3】叉:[https://github.com/pope-tech/ngx-table](https://github.com/pope-tech/ngx-table)

### 2。引导程序

Dinolytics 使用具有强大可访问性支持的 Bootstrap，但是有多个 Angular 包实现了基于 Javascript 的组件，比如 toggletips 和 modals。直到我已经实现了大部分组件，我才注意到我选择的组件是完全不可访问的。幸运的是，有一种替代方法内置了强大的可访问性支持。

[https://ng-bootstrap.github.io/#/home](https://ng-bootstrap.github.io/#/home)

### 3。树组件

Dinolytics 提供了一个树组件来帮助您管理您的组层次结构。有一些 Angular 的树组件，但是我只找到一个支持键盘导航的。这是为什么您应该使用多种形式的可访问性测试的一个很好的例子。

我认为这棵树可以用键盘导航的事实足以让我认为它是可访问的。结果是没有办法只通过键盘导航到树，我应该在测试中发现这一点。此外，即使在被聚焦后，它也不会向屏幕阅读器呈现任何内容。这是完全不可访问的，我甚至不知道，直到我用屏幕阅读器测试。

因为没有任何其他的包(甚至有角的材质树组件都是完全不可访问的！)，我决定分叉这个包，添加 aria 支持。这个分叉版本是我们目前在 Dinolytics 上使用的，虽然它还不完美，但至少是可用的。我计划提交一份 PR，在不久的将来让每个人都可以看到这些变化。

[https://github.com/500tech/angular-tree-component](https://github.com/500tech/angular-tree-component)

### 4。拨动开关

我喜欢使用[拨动开关](https://github.com/uncommonconcept/ngx-toggle-switch)而不是复选框。我认为它们看起来更好，也更容易被用户理解。也就是说，我能找到的所有 Angular 的拨动开关包都是不可访问的。虽然您可以使用 CSS 和 Javascript 实现相同的外观和感觉，我们将来可能会这样做，但我决定将所有的切换开关都改为复选框。有时，仅使用本机 HTML 元素是补救您在覆盖本机浏览器行为时遇到的一些问题的最佳选择。

## 菜谱

让我们深入一些代码。我想回顾一些常见的可访问性技术，以及如何在 Angular 中实现它们。

### 1。跳过链接

WebAIM 有一篇关于跳转链接的很棒的文章，所以我不会在这里解释它的好处。我最初认为你可以使用 Angular 的 routerLink 和 fragment 指令来实现它，比如:

```
<a [routerLink]="['./']" fragment="main-content">Skip to content</a> 
```

Enter fullscreen mode Exit fullscreen mode

这个不行！如果你激活了那个链接，什么也不会发生。我尝试了很多使用原生属性的组合，但都没有效果。最终让它工作的是下面的代码:

```
<div id="skip-link">
    <a [href]="skipLinkPath">Skip to content</a>
</div>

<main id="main-content" class="content">
    <!--Main content goes here-->
</main>

// in constructor or ngOnInit()
router.events.pipe(filter(event => event instanceof NavigationEnd)).subscribe(() => {
   if( ! this.router.url.endsWith('#main-content')) {
       this.skipLinkPath = `${this.router.url}#main-content`;
   }
}); 
```

Enter fullscreen mode Exit fullscreen mode

我还发现了一个替代方法的博客帖子。我个人更喜欢上面的方法，因为它尽可能接近本地的跳转链接，但是下面的方法也可以，它使用一个按钮并调用 HTML 元素本身的 focus 方法。

[http://markleblog.com/tl-dr/](http://markleblog.com/tl-dr/)

### 2。焦点管理

焦点管理是网页可访问性的一个重要部分，尤其是在 SPA 方面。有许多交互会改变屏幕上的内容，你需要确保你的应用恰当地关注这些元素。有一些技巧你可以使用角度来管理焦点。

#### 场景一:

假设您有一个内容或表单部分，它最初是隐藏的，当用户按下按钮时就会出现。

以 Dinolytics 中组屏幕上的网站小部件为例。初始视图显示网站列表。

[![](img/87038108bfae850745b07e9050b2e1df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6lCL_0Ua--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.dinolytics.com/wp-content/uploads/2018/10/websites-table.png)

当用户激活“添加网站”按钮时，屏幕变为网站形式。当这样的交互发生时，您需要确保将焦点设置在适当的元素上，在本例中是表单中的第一个也是唯一一个字段。

[![](img/07402e6a6832fabdcd721f3ff08a8130.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QVLLDA_B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.dinolytics.com/wp-content/uploads/2018/10/websites-form.png)

你可以用下面的方法在 Angular 中完成这个:

```
<div>
   <button type="button" (click)="toggleForm()">
       <ng-container *ngIf=" ! showForm">Show form</ng-container>
       <ng-container *ngIf="showForm">Back</ng-container>
   </button>

   <ng-container *ngIf=" ! showForm">
       <table id="mytable"></table>
   </ng-container>

   <ng-container *ngIf="showForm">
       <form>
           <label>Website</label>
           <input type="text" id="website" name="website" />
       </form>
   </ng-container>

</div>

public toggleForm() {
   this.showForm = ! this.showForm;

   let focus = this.showForm ? 'website' : 'mytable';

   setTimeout(() => {
       document.getElementById(focus).focus();
   });
} 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了一些事情。[首先，如果你不知道 ng-container，那就去看看吧！](https://blog.angular-university.io/angular-ng-template-ng-container-ngtemplateoutlet/)它提供了一种使用结构化指令的便捷方式，而无需添加不必要的 div 和 spans。

其次，您可能已经注意到 focus()调用被包装在一个 setTimeout()函数中。这是因为浏览器需要在调用 focus 方法之前执行更新，否则您试图聚焦的元素将不会出现在屏幕上，并且会失败。

#### 场景二:

您正在使用动态表单输入，用户可以通过激活按钮来添加或删除输入。以 Dinolytics 中报告屏幕上的外部电子邮件地址字段为例。

[![](img/3d663985f505c3c443d1b5e32c9ae5d6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_vgjy6_e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.dinolytics.com/wp-content/uploads/2018/10/external-emails-1.png)

该界面以“发送到外部电子邮件地址”按钮开始。当您激活该按钮时，它会为电子邮件地址添加一个新的文本输入。

[![](img/ff48234ed628fde5ee3257f5fd46d477.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ogNFF8MC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.dinolytics.com/wp-content/uploads/2018/10/external-emails-2.png)

当添加新字段时，它应该获得焦点。

[![](img/46c58b40fc9b932de6873bc0e268c9dd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ViwRsXEw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.dinolytics.com/wp-content/uploads/2018/10/external-emails-3.png)

当删除一个字段时，前一个字段应该获得焦点，当删除所有字段时，“发送到外部电子邮件地址”按钮应该获得焦点。您可以在动态输入字段上使用自定义指令来完成大部分工作。

```
<input focus-input formControlName="address" id="emails-{{ ii }}" class="form-control" type="email" name="emails[]" /> 
```

Enter fullscreen mode Exit fullscreen mode

```
import { AfterViewInit, Directive, ElementRef } from "@angular/core";

@Directive({
   selector: '[focus-input]'
})
export class FocusInput implements AfterViewInit {

   constructor(public elem: ElementRef) {}

   ngAfterViewInit() {
       this.elem.nativeElement.focus();
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

这负责在添加输入时设置焦点。现在，我们必须确保当输入被移除时，焦点返回到前一个元素。我们可以获取被删除的输入的索引，减去 1，然后聚焦到那个 id。现在添加一个调用来关注主按钮，当所有的电子邮件被删除，你就大功告成了！

```
public deleteEmail(index) {

   // remove email field

   // set focus
   document.getElementById('emails-' + (index - 1)).focus();
}

public removeAllExternalEmails() {

   // remove all emails and hide container

   // set focus
   document.getElementById('show-external-emails').focus();
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 场景三:

路线改变了，你想把注意力集中在 H1 标签上。我们可以再次挂钩路由器事件，以确保每次路由更改时都会自动发生这种情况。

通过设置 tabindex="-1 "确保您的 H1 是可聚焦的，然后订阅路由器事件。

```
constructor(private router: Router) {
    router.events.pipe(filter(event => event instanceof NavigationEnd)).subscribe(() => {
        document.getElementsByTagName('h1')[0].focus();
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 场景四:

您希望将焦点设置到角度组件，但不能使用标记名、id 或类。当我试图使用 [ng-select 包](https://github.com/ng-select/ng-select)关注 combobox 组件时，我遇到了这个问题。我们可以使用 Angular 的视图子装饰器来完成。我建议只在无法使用像 document.getElementById 这样的本地方法时才使用这种方法。

```
 <ng-select #websiteSelect
          [items]="selectWebsites$ | async"
          formControlName="website"
          placeholder="Select a website"
          bindLabel="name"
          labelForId="website">
</ng-select> 
```

Enter fullscreen mode Exit fullscreen mode

```
@ViewChild('websiteSelect') websiteSelect;

public toggleForm() {
   setTimeout(() => {
       this.mySelect.focus();
   });
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以将组件类或字符串标识符传递到 ViewChild 中，view child 是通过在前面带有“#”的组件上声明它来定义的，如上例所示。然后，您可以直接访问该组件。请注意，只有在 toggle 调用之前组件不在屏幕上时，才需要 setTimeout()调用。

### 3。页面标题

Angular 附带了一个类来帮助你管理你的页面标题。我连接到路由定义，这样它就可以根据路由自动定义。

可以在路由上定义页面标题，然后通过路由器订阅导航变化事件。

```
constructor(private router: Router,
                   private route: ActivatedRoute
                   private titleService: Title) {

   router.events.pipe(filter(event => event instanceof NavigationEnd)).subscribe(() => {
       let title = this.getTitleFromRoute(route.root) + ' - Dinolytics';
       this.titleService.setTitle(title);
   });
}

private getTitleFromRoute(route) {
   if (route.snapshot.data.hasOwnProperty('title')) {
       return route.snapshot.data.title;
   }

   let children: ActivatedRoute[] = route.children;

   // iterate over route children
   for (let child of children) {
       // verify that we have the correct router outlet
       if (child.outlet !== PRIMARY_OUTLET) {
           continue;
       }

       if ( ! child.snapshot.data.hasOwnProperty('title')) {
           return this.getTitleFromRoute(child);
       }

       return child.snapshot.data.title;
   }

   return false;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
export const routes = [{
   path: ':id',
   component: GroupsEditComponent,
   data: {
       title: 'Edit Group'
   },
}] 
```

Enter fullscreen mode Exit fullscreen mode

因为路由可以嵌套，所以我们必须递归地遍历路由来找到标题。一旦我们有了它，我们就可以使用 Angular 的 TitleService 来设置正确的标题。

## 测试

现在你已经知道了一些帮助你的 Angular 应用程序更易访问的技巧和诀窍，你需要知道如何测试它们。

### 波

第一个也是最容易使用的方法是 WAVE 浏览器扩展。

[WAVE Chrome 扩展](https://chrome.google.com/webstore/detail/wave-evaluation-tool/jbbplnpkjmmeebjpijfedlgcdilocofh?hl=en-US)

[WAVE Firefox 扩展](https://addons.mozilla.org/en-US/firefox/addon/wave-accessibility-tool/)

您可以使用这个扩展在本地评估每个页面，以快速找到故障点并修复它们。使用这种方法你不会发现你的应用程序的所有问题，但是它会帮助你开始。

### **轴**

Axe 也有免费的浏览器扩展来帮助你开始初始的可访问性评估。

[Axe Chrome 扩展](https://chrome.google.com/webstore/detail/axe/lhdoppojpmngadmnindnejefpokejbdd)

[Axe Firefox 扩展](https://addons.mozilla.org/en-US/firefox/addon/axe-devtools/)

### 键盘测试

下一个方法是只使用键盘测试应用程序。WebAIM 有一篇很棒的文章可以帮助你开始使用这种技术。如果没有鼠标你不能有效地使用你的应用程序，它是不可访问的！确保使用正确的焦点管理和指示器来帮助改进键盘导航。

### 屏幕阅读器测试

我推荐的最后一种方法是屏幕阅读器测试。[同样，WebAIM 拥有强大的资源来帮助您完成这个](https://webaim.org/articles/screenreader_testing)。一旦你熟悉了屏幕阅读器的工作方式，关掉你的显示器，试着使用你的应用程序。你会很快发现哪些地方需要改进。

#### 窗口

Windows 用户只需很少的设置就可以使用 NVDA 和火狐、Chrome 或 IE。

#### Mac

Mac 已经安装了 VoiceOver，随时可以使用。

#### Linux

如果您像我一样是 Linux 用户，那么有几个选项可供您选择。

##### **Chromevox**

链接到谷歌应用商店的 Chromevox。我不推荐这个选项，因为 ChromeVox 似乎有很多问题，而且到目前为止它的采用率还不是很高。

##### **Linux 屏幕阅读器如 Orca**

我还没有测试过这些，但是它们在我的清单上。

##### **NVDA 使用 VirtualBox**

最后，我使用的方法采用了运行 Firefox、Chrome 和 NVDA 的 Windows VirtualBox 映像。我将简要地带您完成设置，因为有几个问题。

1.  前往[https://developer . Microsoft . com/en-us/Microsoft-edge/tools/VMs/](https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/)并通过选择 VirtualBox 平台上的“MSEdge on Win10”机器获得 Windows 10 映像。

2.  如果你还没有 VirtualBox，去[https://www.virtualbox.org/](https://www.virtualbox.org/)下载适合你机器的软件包并安装。

3.  一旦您的映像完成下载并安装了 VirtualBox，请提取 windows 映像。压缩并导入。双击 ova 进入 VirtualBox 并启动它。

4.  如果您的 Angular 应用程序与本地托管的外部 API 对话，请确保在 C:\ Windows \ System32 \ drivers \ etc \ hosts 中添加适当的 hostfile 条目

5.  如果您使用的是 Angular CLI，请确保在 ng serve 命令中添加了一个主机选项，以便 VirtualBox 可以与之对话。您的内部主机 IP 或 0.0.0.0 都可以。

6.  除了安装 NVDA 和火狐，你可能不需要做任何其他事情，但是如果你的声音像我一样被禁用，进入机器设置，找到“音频”部分。

你可能不得不玩设置，因为我的印象是它是硬件特定的，但这里是我必须使用的设置，让我的工作正常。

[![](img/3b645d518877bc36cfde2b77b56e84c2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qf7aT4o1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.dinolytics.com/wp-content/uploads/2018/10/vbox-settings.png)

主机音频驱动:PulseAudio

音频控制器:英特尔高清音频

启用音频输出:选中

保存并重启你的机器，你应该准备好了！一旦一切就绪，就拍一张快照，这样，如果出现问题(或达到 90 天的窗口限制)，您就可以随时返回到这一点。

### 自动化测试

用前端测试工具如 Jasmine 和 Karma 测试可访问性是我还没有检验过的，但是我真的很喜欢这个想法。我认为将可访问性检查集成到您的测试套件中是一个有价值的实践，可以防止回归。如果你想深入这个话题，这里有一些资源。

[使用 Angular](https://marcysutton.github.io/a11y-testing-with-angular/#/)

[进行可访问性测试使用 Selenium Webdrive 和 AxeCore 进行自动化可访问性测试](https://marmelab.com/blog/2018/02/22/accessibililty-testing-e2e.html)

[测试可访问性并帮助数百万人](https://blog.angularindepth.com/test-for-accessibility-and-help-millions-of-people-97d86f72e2c4)

## 结论

在这篇文章中，我仅仅触及了网页可访问性的皮毛，还有很多我没有涉及到。如果您想更深入地了解这个主题，我已经提供了一个额外资源的小列表，这些资源是特定于角度的。[如果您对其他话题有任何建议，如果您有任何问题，或者如果您只是想打声招呼，请加入 Twitter 上的对话](https://twitter.com/dinolytics/status/1055564328982597632)！感谢阅读。

## 资源

[有角 2 训练书](https://angular-2-training-book.rangle.io/handout/a11y/)

[有角无障碍小技巧和窍门](https://coryrylan.com/blog/angular-accessibility-tips-and-tricks)

[有角无障碍](https://marcysutton.com/slides/angular-a11y-ng-europe/#/)

[有角 CDK 无障碍](https://codeburst.io/angular-cdk-getting-started-accessibility-a11y-1b6143b961c)

帖子 [Angular，Accessibility，and You](https://blog.dinolytics.com/2018/10/25/angular-accessibility-and-you/) 最先出现在 [Dinolytics 博客](https://blog.dinolytics.com)上。