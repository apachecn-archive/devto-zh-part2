# 将角度组件嵌入传统 Web 应用程序

> 原文：<https://dev.to/bobnadler/embedding-angular-components-into-a-legacy-web-app-2ff5>

原始帖子:[将 Angular 组件嵌入传统 Web 应用](http://bobonmedicaldevicesoftware.com/blog/2018/07/25/embedding-angular-components-into-a-legacy-web-app)

*注*:本版本代码样本不包含行号。

[![](img/a81c586a8fbe6742824706085e44ad7d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7HpNaHbz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://bobonmedicaldevicesoftware.com/blog/wp-content/uploads/2018/07/embedded-angular-150x150.png) 在一个完美的世界里，你可以从头开始创建一个全新的水疗中心。在现实世界中，通常不是这样。这种遗留的 web 应用程序有太多的负担，很难一蹴而就地将其转换为 SPA。如果您目前正在使用 JSP 或 Rails 技术的服务器端渲染，这一点尤其正确。

唯一真正的解决方案是**逐渐地**移动/升级 UI 逻辑和数据访问模式(例如，转换到 REST 接口)。如果你计划迁移到 Angular ***** ，一个好的起点是首先将 Angular 实现的逻辑嵌入到你现有的应用程序中。这种方法还允许新的 Angular 组件共享 CSS 样式，以实现无缝的视觉集成。

NgInterop 是一个简单的类型脚本类，它允许遗留的 Web 应用程序通过嵌入的角度组件进行**双向通信**(通过[发布/订阅](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern))。底层的 **MessagingSerivce** 类是 Angular 2 和 TypeScript 中[消息总线模式的实现。](http://www.processinginfinity.com/weblog/2016/08/18/MessageBus-Pattern-in-Angular2-TypeScript)

演示项目的源代码在这里:[嵌入式角度](https://github.com/rnadler/embedded-angular)

**T2**

```
import {Injectable, NgZone} from '@angular/core';
import {AngularEvent, EventCallbackFunction, HtmlEvent, LogEvent} from './event.types';
import {MessagingService} from './messaging.service';

@Injectable({
  providedIn: 'root'
})
export class NgInterop {
  public static readonly ANGULAR_EVENT: string = 'AngularEvent';
  public static readonly HTML_EVENT: string = 'HtmlEvent';
  public static readonly LOG_EVENT: string = 'LogEvent';
  private typeClassMap: any = {};
  private readonly initCallback: any;

  constructor(private ngZone: NgZone, private messagingService: MessagingService) {
    this.typeClassMap[NgInterop.ANGULAR_EVENT] =  AngularEvent;
    this.typeClassMap[NgInterop.HTML_EVENT] =  HtmlEvent;
    this.typeClassMap[NgInterop.LOG_EVENT] =  LogEvent;
    this.initCallback = window['NgInteropInitCallback'];
    window['ngInterop'] = this;
    this.init();
  }
  private init() {
    if (!this.initCallback) {
      console.warn('NgInterop.init: No NgInteropInitCallback found!');
      return;
    }
    this.initCallback();
  }
  public subscribeToClass(className: string, callBack: EventCallbackFunction): any {
    const self = this;
    this.ngZone.run(() => {
      self.messagingService.of(self.typeClassMap[className]).subscribe(callBack);
    });
  }
  public publishToClass(className: string, source: string, value: string): any {
    const self = this;
    this.ngZone.run(() => {
      self.messagingService.publish(new self.typeClassMap[className](source, value));
    });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

亮点:

*   **6** :以语法提供的新款 Angular 6 **侧记。这样就不必将每个服务都添加到 app . module . ts @ ng module providers 列表中。非常得心应手！**
*   **19** :保存原生 JavaScript 初始化回调函数(见下面 index.html)。这个例子只有一个回调函数，但是很容易扩展这个功能来支持多个初始化回调。
*   **20** :将 NgInterop 实例添加到`window`对象中，这样外部 JavaScript 就可以简单地调用`window.ngInterop`上的方法(再次参见下面的 index.html)。
*   **32 和 38** :在 [NgZone.run()](https://angular.io/api/core/NgZone#run) 调用中封装消息服务订阅/发布。这允许外部 JavaScript 在角度区域中执行这些功能。

其他注意事项:

*   对象将一个 BaseEvent 类名(字符串)映射到一个真实的类。公共 static *_EVENT 名称提供了从 Angular 代码对 NgInterop 函数的更安全的访问。
*   无法在原生 JavaScript 端进行类型或参数检查，但对 BaseEvent 派生类进行强类型化仍然是一个好的做法。这提供了很好的文档，并在 TypeScript 的早期发现了问题。

这是一个精简的**index.html**，展示了外部 JavaScript 代码如何与 NgInterop 交互。

**index.html(片段)**

```
<script>
  function subscribeToEvents() {
    ...
    window.ngInterop.subscribeToClass('AngularEvent', function (event) {
     ...
    });
  }
  ...
  function clickMe() {
    window.ngInterop.publishToClass('HtmlEvent', 'clickMe', getRandomString());
  }
  ...
  window['NgInteropInitCallback'] = subscribeToEvents;
</script> 
```

Enter fullscreen mode Exit fullscreen mode

亮点:

*   **4**:NgInterop 构造函数调用 **subscribeToEvents()** 后，该函数订阅 AngularEvent 消息。当点击 **AppComponent** 类中的 Angular“切换移除按钮”时，AngularEvent 消息被发布。
*   在一个 HTML 点击事件中，一个 HtmlEvent 消息被发布。HtmlEvent 消息的订阅者也在 AppComponent 类中。
*   **13** :回调函数被添加到`window`对象中。这在 Angular 启动之前执行。
*   所有日志记录都是通过发布 LogEvent 消息来完成的。这些由 **LogComponent** 类显示。

示例应用程序有两个 Angular 组件，它们与原生 JavaScript 交互，也与 NgInterop 交互。代码的其余部分应该是不言自明的。

示例应用程序的屏幕截图:

[![](img/53e272f6c75a20a388e26994e2eb2a2e.png)](https://github.com/rnadler/embedded-angular) 
本工程采用如下:

*   [Angular CLI](https://github.com/angular/angular-cli)——当然。
*   [RxJS](https://rxjs-dev.firebaseapp.com/) -由消息服务使用。
*   [Bootstrap 4](https://getbootstrap.com/) -漂亮的按钮和“卡片”布局。
*   [Moment.js](https://momentjs.com/) -更容易格式化日志时间戳。
*   [量角器](https://www.protractortest.org/#/) -用于运行角度 e2e 测试。

尽情享受吧！
*****[React](https://reactjs.org/)和 [Vue](https://vuejs.org/) 大概有类似的集成方式。我只是不知道它们是什么。