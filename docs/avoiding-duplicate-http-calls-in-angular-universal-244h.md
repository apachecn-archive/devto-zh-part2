# 在 Angular Universal 中避免重复的 HTTP 调用

> 原文：<https://dev.to/cesarcodes/avoiding-duplicate-http-calls-in-angular-universal-244h>

使用 Angular Universal 可能会给开发人员带来一系列独特的挑战。其中一个事实是，HTTP 调用在服务器和客户机应用程序上都是重复的。
根据您的具体情况，这个问题可以有不同的解决方式。
如果您的应用程序使用 Angular 的 HttpClient 进行 HTTP 调用，那么解决方案相当简单。在这种情况下，您可以在 app.module 上使用 Angular Universal 的 TransferHttpCacheModule，在 app.server.module 上使用 ServerTransferStateModule。

将 TransferHttpCacheModule 导入到 AppModule:

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';
import { TransferHttpCacheModule } from '@nguniversal/common'; // HERE

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule.withServerTransition({appId: 'my-app'}),
    TransferHttpCacheModule // AND HERE
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

将 ServerTransferStateModule 导入到 AppServerModule:

```
import { NgModule } from '@angular/core';
import {
  ServerModule,
  ServerTransferStateModule // HERE
} from '@angular/platform-server';

import { AppModule } from './app.module';
import { AppComponent } from './app.component';

@NgModule({
  imports: [
    AppModule,
    ServerModule,
    ServerTransferStateModule, // AND HERE
  ],
  bootstrap: [AppComponent]
})
export class AppServerModule { } 
```

当你这么做的时候。当浏览器加载通用应用程序时，使用 HttpClient 生成的 HTTP 调用不会导致重复调用。太棒了！但是…如果您没有使用 HttpClient 呢？如果您使用另一个 HTTP 库，如 Restangular 或 GraphQL 库，或者有不同的场景，该怎么办？在这种情况下，解决方案就不那么简单了，TransferHttpCacheModule 不会成功😔。但是，您仍然可以通过利用 TransferState 来完成消除浏览器上重复调用的任务。这里的想法是，当通用应用程序在服务器上执行时，您将数据存储到使用 makeStateKey 生成的 StateKey 中，然后当应用程序在客户端上执行时，如果数据在那里(将会在那里)，重复的 HTTP 调用将被绕过。请参见以下示例。
首先，你还是需要把 ServerTransferStateModule 带入你的 app.server.module，如上图所示。但是，在 app.module 上，您引入了 BrowserTransferStateModule，这样就可以工作了。

将 BrowserTransferStateModule 导入到 AppModule:

```
import {
  BrowserModule,
  BrowserTransferStateModule // HERE
} from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule.withServerTransition({ appId: 'my-app' }),
    BrowserTransferStateModule // AND HERE
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

既然已经解决了，是时候做脏活了。在调用数据的地方，引入 TransferState 和 makeStateKey，如下面的步骤 1 和 3 所示。然后创建一个 StateKey，如步骤 2 所示。最后，在实际的 HTTP 调用中，您将检查您创建的 StateKey 是否已设置，如步骤 4 所示。当这在服务器端呈现期间第一次执行时，不会设置键，代码将进行 HTTP 调用，如步骤 6 所示。现在您已经有了结果，您可以设置 StateKey，如步骤 7 所示。现在，应用程序被移交给浏览器，当执行相同的 getMyData()函数时，StateKey 包含您之前设置的数据，并且避免了后续的/重复的 HTTP 调用。

```
import { Injectable } from '@angular/core';
import { Restangular } from 'ngx-restangular';
import { Observable } from 'rxjs';
// 1) BRING IN THE FOLLOWING:
import {
  TransferState,
  makeStateKey
} from '@angular/platform-browser';

// 2) CREATE A STATE KEY
const MY_DATA = makeStateKey('my_data');

@Injectable({
  providedIn: 'root'
})
export class MyDataService {

  // 3) INJECT
  constructor(
    private restangular: Restangular,
    private state: TransferState
  ) { }

  getMyData(): Observable<any> {
    // 4) CHECK TO SEE IF DATA EXISTS
    const store = this.state.get(MY_DATA, null);

    // 5) IF DATA EXISTS, RETURN DATA. THIS IS EXECUTED ON THE CLIENT
    if (store) {
      return store;
    }

    // 6) DATA DOES NOT EXIST. MAKE HTTP CALL. THIS IS EXECUTED ON THE SERVER
    const myData = this.restangular.one('api/mydata').get();
    // 7) SET STATE KEY SO THAT CLIENT CAN USE IT IN A SUBSEQUENT ACTION
    this.state.set(MY_DATA, myData);
    return myData;
  }
} 
```

仅此而已。您已经使用 Angular Universal 避免了重复的 HTTP 调用。我希望 NG Universal 团队继续做令人敬畏的工作，并希望程序员可以从这些手工工作中解脱出来。

感谢阅读。塞萨尔