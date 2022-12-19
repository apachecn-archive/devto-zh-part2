# 用 OpenID Connect 和 Okta 在 20 分钟内完成 Angular 6 认证

> 原文：<https://dev.to/oktadev/angular-authentication-with-openid-connect-and-okta-in-20-minutes-33a7>

Angular 是构建现代单页面应用程序的最强大的方法之一。Angular 的核心优势是专注于构建可重用的组件，这有助于您分离应用程序中的各种问题。以身份验证为例:构建身份验证可能很痛苦，但是一旦将身份验证封装在一个组件中，就可以在整个应用程序中重用身份验证逻辑。

Angular CLI 使得搭建新组件甚至整个项目变得容易。如果您还没有使用 Angular CLI 来快速生成 Angular 代码，那么您就等着瞧吧！

在这个例子中，您将使用 Angular CLI 构建一个简单的 web 应用程序，这是一个用于 Angular 开发的工具。您将创建一个具有搜索和编辑功能的应用程序，然后添加身份验证。

## 创建一个角度应用

*提示:如果您想跳过构建 Angular 应用程序，直接添加身份验证，您可以克隆我的`ng-demo`项目，然后跳到 Okta 部分的[创建 OpenID Connect 应用程序。](#create-an-openid-connect-app-in-okta)*

```
git clone https://github.com/mraible/ng-demo.git 
```

Enter fullscreen mode Exit fullscreen mode

### 你需要什么

*   大约 20 分钟
*   最喜欢的文本编辑器或 IDE。我推荐 [IntelliJ IDEA](https://www.jetbrains.com/idea/)
*   [Node.js](https://nodejs.org) 和 npm 已安装。我推荐使用 [nvm](https://github.com/creationix/nvm)
*   安装了[角度指示器](https://cli.angular.io/)。如果您没有安装 Angular CLI，请使用`npm install -g @angular/cli@1.7.3`进行安装

使用`ng new`命令创建一个新项目:

```
ng new ng-demo 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个`ng-demo`项目并在其中运行`npm install`。这应该需要大约一分钟的时间来完成，但这可能会因您的连接速度而异。

```
$ ng new ng-demo
CREATE ng-demo/README.md (1023 bytes)
CREATE ng-demo/angular.json (3408 bytes)
CREATE ng-demo/package.json (1311 bytes)
CREATE ng-demo/tsconfig.json (384 bytes)
CREATE ng-demo/tslint.json (2805 bytes)
CREATE ng-demo/.editorconfig (245 bytes)
CREATE ng-demo/.gitignore (503 bytes)
CREATE ng-demo/src/environments/environment.prod.ts (51 bytes)
CREATE ng-demo/src/environments/environment.ts (631 bytes)
CREATE ng-demo/src/favicon.ico (5430 bytes)
CREATE ng-demo/src/index.html (293 bytes)
CREATE ng-demo/src/main.ts (370 bytes)
CREATE ng-demo/src/polyfills.ts (3194 bytes)
CREATE ng-demo/src/test.ts (642 bytes)
CREATE ng-demo/src/assets/.gitkeep (0 bytes)
CREATE ng-demo/src/styles.css (80 bytes)
CREATE ng-demo/src/browserslist (375 bytes)
CREATE ng-demo/src/karma.conf.js (964 bytes)
CREATE ng-demo/src/tsconfig.app.json (194 bytes)
CREATE ng-demo/src/tsconfig.spec.json (282 bytes)
CREATE ng-demo/src/tslint.json (314 bytes)
CREATE ng-demo/src/app/app.module.ts (314 bytes)
CREATE ng-demo/src/app/app.component.css (0 bytes)
CREATE ng-demo/src/app/app.component.html (1141 bytes)
CREATE ng-demo/src/app/app.component.spec.ts (986 bytes)
CREATE ng-demo/src/app/app.component.ts (207 bytes)
CREATE ng-demo/e2e/protractor.conf.js (752 bytes)
CREATE ng-demo/e2e/src/app.e2e-spec.ts (299 bytes)
CREATE ng-demo/e2e/src/app.po.ts (208 bytes)
CREATE ng-demo/e2e/tsconfig.e2e.json (213 bytes)
...
added 1147 packages from 1269 contributors in 43.48s
    Successfully initialized git.
Execution time: 48 sec 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过`ng --version`查看您正在使用的 Angular CLI 的版本。

```
$ ng --version

     _                      _                 ____ _     ___
    / \   _ __   __ _ _   _| | __ _ _ __     / ___| |   |_ _|
   / △ \ | '_ \ / _` | | | | |/ _` | '__|   | |   | |    | |
  / ___ \| | | | (_| | |_| | | (_| | |      | |___| |___ | |
 /_/   \_\_| |_|\__, |\__,_|_|\__,_|_|       \____|_____|___|
                |___/

Angular CLI: 6.0.0
Node: 9.8.0
OS: darwin x64 
```

Enter fullscreen mode Exit fullscreen mode

## 运行您的角度应用程序

该项目配置有 [webpack 开发服务器](https://github.com/webpack/webpack-dev-server)。要启动它，请确保您在`ng-demo`目录中，然后运行:

```
ng serve 
```

Enter fullscreen mode Exit fullscreen mode

您应该会在`http://localhost:4200`看到如下所示的屏幕。

[![Default Homepage](../Images/396d66d9bedec575d80d4b4f946d4257.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pD0TA3X1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/angular-oidc/default-homepage-073f9f7599c154d38e19f7358a85622bcab268bde6623d640d31349957113934.png)

你可以确保你的新项目测试通过，运行`ng test` :

```
$ ng test
...
Chrome 66.0.3359 (Mac OS X 10.13.4): Executed 3 of 3 SUCCESS (0.205 secs / 0.184 secs) 
```

Enter fullscreen mode Exit fullscreen mode

## 添加搜索功能

要添加搜索功能，请在 IDE 或您喜欢的文本编辑器中打开项目。对于 IntelliJ IDEA，使用文件>新建项目>静态 Web 并指向`ng-demo`目录。

在终端窗口中，cd 进入您的项目目录并运行以下命令。这将创建一个搜索组件。

```
$ ng g component search
CREATE src/app/search/search.component.css (0 bytes)
CREATE src/app/search/search.component.html (25 bytes)
CREATE src/app/search/search.component.spec.ts (628 bytes)
CREATE src/app/search/search.component.ts (269 bytes)
UPDATE src/app/app.module.ts (396 bytes) 
```

Enter fullscreen mode Exit fullscreen mode

打开`src/app/search/search.component.html`并用以下内容替换它的默认 HTML:

```
<h2>Search</h2>
<form>
  <input type="search" name="query" [(ngModel)]="query" (keyup.enter)="search()">
  <button type="button" (click)="search()">Search</button>
</form>
<pre>{{searchResults | json}}</pre> 
```

Enter fullscreen mode Exit fullscreen mode

Angular 的[路由器文档](https://angular.io/guide/router)提供了您设置到您刚刚生成的`SearchComponent`的路由所需的信息。这里有一个简短的总结:

在`src/app/app.module.ts`中，添加一个`appRoutes`常量，并将其导入`@NgModule` :

```
import { Routes, RouterModule } from '@angular/router';

const appRoutes: Routes = [
  {path: 'search', component: SearchComponent},
  {path: '', redirectTo: '/search', pathMatch: 'full'}
];

@NgModule({
  ...
  imports: [
    ...
    RouterModule.forRoot(appRoutes)
  ]
  ...
})
export class AppModule { } 
```

Enter fullscreen mode Exit fullscreen mode

在`src/app/app.component.html`中，调整占位符内容并添加一个`<router-outlet>`标签来显示路线。

```
<h1>Welcome to {{title}}!</h1>
<!-- Routed views go here -->
<router-outlet></router-outlet> 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经设置了路由，您可以继续编写搜索特性。

如果你还在运行`ng serve`，你的浏览器应该会自动刷新。如果没有，导航至`http://localhost:4200`。您可能会看到一个空白屏幕。打开您的 JavaScript 控制台，您就会看到问题。

[![ngModel error](../Images/e91410270ca8842d331db35b40979d68.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A4fk-ku7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/angular-oidc/ngmodel-error-b52e91e6cbf9cd3ce3da38d98782ba85b170db2428c01a1033c29cc2d16a0156.png)

要解决这个问题，打开`src/app/app.module.ts`，在`@NgModule` :
中添加`FormsModule`作为导入

```
import { FormsModule } from '@angular/forms';

@NgModule({
  ...
  imports: [
    ...
    FormsModule
  ]
  ...
})
export class AppModule { } 
```

Enter fullscreen mode Exit fullscreen mode

现在您应该会看到搜索表单。

[![Search component](../Images/97b40e2989f061a503c82e67205d4fb8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--64UNKlLV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/angular-oidc/search-without-css-3add5390962f7e49a1396554acd0028db32a55cef92e3a769d1de70039557e33.png)

如果你想为这个组件添加 CSS，打开`src/app/search/search.component.css`并添加一些 CSS。比如:

```
:host {
  display: block;
  padding: 0 20px;
} 
```

Enter fullscreen mode Exit fullscreen mode

本节向您展示了如何使用 Angular CLI 为基本角度应用程序生成一个新组件。下一节将向您展示如何创建一个使用 JSON 文件和`localStorage`来创建一个假 API。

要获得搜索结果，创建一个向 JSON 文件发出 HTTP 请求的`SearchService`。从生成一个新服务开始。

```
$ ng g service search
CREATE src/app/search.service.spec.ts (374 bytes)
CREATE src/app/search.service.ts (135 bytes) 
```

Enter fullscreen mode Exit fullscreen mode

将生成的`search.service.ts`及其测试移动到`app/shared/search`。您需要创建这个目录。

```
mkdir -p src/app/shared/search
mv src/app/search.service.* src/app/shared/search/. 
```

Enter fullscreen mode Exit fullscreen mode

创建`src/assets/data/people.json`来保存您的数据。

```
[  {  "id":  1,  "name":  "Peyton Manning",  "phone":  "(303) 567-8910",  "address":  {  "street":  "1234 Main Street",  "city":  "Greenwood Village",  "state":  "CO",  "zip":  "80111"  }  },  {  "id":  2,  "name":  "Demaryius Thomas",  "phone":  "(720) 213-9876",  "address":  {  "street":  "5555 Marion Street",  "city":  "Denver",  "state":  "CO",  "zip":  "80202"  }  },  {  "id":  3,  "name":  "Von Miller",  "phone":  "(917) 323-2333",  "address":  {  "street":  "14 Mountain Way",  "city":  "Vail",  "state":  "CO",  "zip":  "81657"  }  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

修改`src/app/shared/search/search.service.ts`并在其构造函数中提供`HttpClient`作为依赖项。在这个相同的文件中，创建一个`getAll()`方法来聚集所有的人。另外，定义 JSON 将被编组到的`Address`和`Person`类。

```
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Injectable({
  providedIn: 'root'
})
export class SearchService {

  constructor(private http: HttpClient) { }

  getAll() {
    return this.http.get('assets/data/people.json');
  }
}

export class Address {
  street: string;
  city: string;
  state: string;
  zip: string;

  constructor(obj?: any) {
    this.street = obj && obj.street || null;
    this.city = obj && obj.city || null;
    this.state = obj && obj.state || null;
    this.zip = obj && obj.zip || null;
  }
}

export class Person {
  id: number;
  name: string;
  phone: string;
  address: Address;

  constructor(obj?: any) {
    this.id = obj && Number(obj.id) || null;
    this.name = obj && obj.name || null;
    this.phone = obj && obj.phone || null;
    this.address = obj && obj.address || null;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了使这些类可供您的组件使用，创建`src/app/shared/index.ts`并添加以下内容:

```
export * from './search/search.service'; 
```

Enter fullscreen mode Exit fullscreen mode

创建这个文件的原因是，这样您就可以在一行中导入多个类，而不必在单独的行中导入每个单独的类。

在`src/app/search/search.component.ts`中，为这些类添加导入。

```
import { Person, SearchService } from '../shared'; 
```

Enter fullscreen mode Exit fullscreen mode

您现在可以添加`query`和`searchResults`变量。在那里，修改构造函数来注入`SearchService`。

```
export class SearchComponent implements OnInit {
  query: string;
  searchResults: Array<Person>;

  constructor(private searchService: SearchService) { } 
```

Enter fullscreen mode Exit fullscreen mode

然后实现一个`search()`方法来调用服务的`getAll()`方法。

```
search(): void {
  this.searchService.getAll().subscribe(
    (data: any) => { this.searchResults = data; },
    error => console.log(error)
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

此时，您可能会在浏览器的控制台中看到以下消息。

```
NullInjectorError: No provider for HttpClient! 
```

Enter fullscreen mode Exit fullscreen mode

要修复上面的“无提供者”错误，请更新`src/app/app.module.ts`以导入`HttpClientModule`。

```
import { HttpClientModule } from '@angular/common/http';

@NgModule({
  ...
  imports: [
    ...
    HttpClientModule
  ],
  providers: [],
  bootstrap: [AppComponent]
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在，单击搜索按钮应该可以工作了。为了让结果看起来更好，移除`<pre>`标签并用`src/app/search/search.component.html`中的`<table>`替换它。

```
<table *ngIf="searchResults">
  <thead>
  <tr>
    <th>Name</th>
    <th>Phone</th>
    <th>Address</th>
  </tr>
  </thead>
  <tbody>
  <tr *ngFor="let person of searchResults; let i=index">
    <td>{{person.name}}</td>
    <td>{{person.phone}}</td>
    <td>{{person.address.street}}<br/>
      {{person.address.city}}, {{person.address.state}} {{person.address.zip}}
    </td>
  </tr>
  </tbody>
</table> 
```

Enter fullscreen mode Exit fullscreen mode

然后在`src/app/search/search.component.css`中添加一些额外的 CSS 来改善它的表格布局。

```
table {
  margin-top: 10px;
  border-collapse: collapse;
}

th {
  text-align: left;
  border-bottom: 2px solid #ddd;
  padding: 8px;
}

td {
  border-top: 1px solid #ddd;
  padding: 8px;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在搜索结果看起来更好了。

[![Search Results](../Images/9ba4171ce39db65ba7aacdc63d6ac5f5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NJGTKIlr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/angular-oidc/search-results-3114cf1b3c0183acf5f1167103a31fcdfdaa0e278e8a4bf6bc157c3e2ce83fd3.png)

但是等等，你还是没有搜索功能！要添加一个搜索特性，需要在`SearchService`中添加一个`search()`方法。

```
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';
...

  search(q: string): Observable<any> {
    if (!q || q === '*') {
      q = '';
    } else {
      q = q.toLowerCase();
    }
    return this.getAll().pipe(
      map((data: any) => data
        .filter(item => JSON.stringify(item).toLowerCase().includes(q)))
    );
  } 
```

Enter fullscreen mode Exit fullscreen mode

然后重构`SearchComponent`用它的`query`变量调用这个方法。

```
search(): void {
  this.searchService.search(this.query).subscribe(
    (data: any) => { this.searchResults = data; },
    error => console.log(error)
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，搜索结果将根据您输入的查询值进行过滤。

本节向您展示了如何获取和显示搜索结果。下一节以此为基础，展示如何编辑和保存记录。

## 添加一个编辑特征

修改`src/app/search/search.component.html`增加一个编辑人的链接。

```
<td><a [routerLink]="['/edit', person.id]">{{person.name}}</a></td> 
```

Enter fullscreen mode Exit fullscreen mode

运行以下命令生成一个`EditComponent`。

```
$ ng g component edit
CREATE src/app/edit/edit.component.css (0 bytes)
CREATE src/app/edit/edit.component.html (23 bytes)
CREATE src/app/edit/edit.component.spec.ts (614 bytes)
CREATE src/app/edit/edit.component.ts (261 bytes)
UPDATE src/app/app.module.ts (840 bytes) 
```

Enter fullscreen mode Exit fullscreen mode

在`src/app/app.module.ts` :
中为该组件添加一条路径

```
const appRoutes: Routes = [
  { path: 'search', component: SearchComponent },
  { path: 'edit/:id', component: EditComponent },
  { path: '', redirectTo: '/search', pathMatch: 'full' }
]; 
```

Enter fullscreen mode Exit fullscreen mode

更新`src/app/edit/edit.component.html`以显示一个可编辑的表单。您可能会注意到，我已经为大多数元素添加了`id`属性。这是为了在用量角器编写集成测试时使事情变得更容易。

```
<div *ngIf="person">
  <h3>{{editName}}</h3>
  <div>
    <label>Id:</label>
    {{person.id}}
  </div>
  <div>
    <label>Name:</label>
    <input [(ngModel)]="editName" name="name" id="name" placeholder="name"/>
  </div>
  <div>
    <label>Phone:</label>
    <input [(ngModel)]="editPhone" name="phone" id="phone" placeholder="Phone"/>
  </div>
  <fieldset>
    <legend>Address:</legend>
    <address>
      <input [(ngModel)]="editAddress.street" id="street"><br/>
      <input [(ngModel)]="editAddress.city" id="city">,
      <input [(ngModel)]="editAddress.state" id="state" size="2">
      <input [(ngModel)]="editAddress.zip" id="zip" size="5">
    </address>
  </fieldset>
  <button (click)="save()" id="save">Save</button>
  <button (click)="cancel()" id="cancel">Cancel</button>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

修改`EditComponent`来导入模型和服务类，并使用`SearchService`来获取数据。

```
import { Component, OnInit, OnDestroy } from '@angular/core';
import { Address, Person, SearchService } from '../shared';
import { Subscription } from 'rxjs';
import { ActivatedRoute, Router } from '@angular/router';

@Component({
  selector: 'app-edit',
  templateUrl: './edit.component.html',
  styleUrls: ['./edit.component.css']
})
export class EditComponent implements OnInit, OnDestroy {
  person: Person;
  editName: string;
  editPhone: string;
  editAddress: Address;

  sub: Subscription;

  constructor(private route: ActivatedRoute,
              private router: Router,
              private service: SearchService) {
  }

  ngOnInit() {
    this.sub = this.route.params.subscribe(params => {
      const id = + params['id']; // (+) converts string 'id' to a number
      this.service.get(id).subscribe(person => {
        if (person) {
          this.editName = person.name;
          this.editPhone = person.phone;
          this.editAddress = person.address;
          this.person = person;
        } else {
          this.gotoList();
        }
      });
    });
  }

  ngOnDestroy() {
    this.sub.unsubscribe();
  }

  cancel() {
    this.router.navigate(['/search']);
  }

  save() {
    this.person.name = this.editName;
    this.person.phone = this.editPhone;
    this.person.address = this.editAddress;
    this.service.save(this.person);
    this.gotoList();
  }

  gotoList() {
    if (this.person) {
      this.router.navigate(['/search', {term: this.person.name} ]);
    } else {
      this.router.navigate(['/search']);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

修改`SearchService`以包含通过 id 查找人员并保存他们的功能。当你在那里的时候，修改`search()`方法来感知`localStorage`中更新的对象。

```
search(q: string): Observable<any> {
  if (!q || q === '*') {
    q = '';
  } else {
    q = q.toLowerCase();
  }
  return this.getAll().pipe(
    map((data: any) => data
        .map(item => !!localStorage['person' + item.id] ?
          JSON.parse(localStorage['person' + item.id]) : item)
        .filter(item => JSON.stringify(item).toLowerCase().includes(q))
    ));
}

get(id: number) {
  return this.getAll().pipe(map((all: any) => {
    if (localStorage['person' + id]) {
      return JSON.parse(localStorage['person' + id]);
    }
    return all.find(e => e.id === id);
  }));
}

save(person: Person) {
  localStorage['person' + person.id] = JSON.stringify(person);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你想让表单看起来更好一点，你可以给`src/app/edit/edit.component.css`添加 CSS。

```
:host {
  display: block;
  padding: 0 20px;
}

button {
  margin-top: 10px;
} 
```

Enter fullscreen mode Exit fullscreen mode

此时，您应该能够搜索一个人并更新他们的信息。

[![Edit form](../Images/fcd14ebe542c5cdcf363ef5b114007af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eyy0sb1W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/angular-oidc/edit-form-10b0875bcaa67f5033d69190b1052321da3a905a75ed59e65c5a0822c3cec42e.png)

`src/app/edit/edit.component.html`中的`<form>`调用`save()`函数来更新一个人的数据。您已经在上面实现了这一点。
该函数调用一个`gotoList()`函数，当用户返回到搜索屏幕时，该函数将这个人的名字附加到 URL 上。

```
gotoList() {
  if (this.person) {
    this.router.navigate(['/search', {term: this.person.name} ]);
  } else {
    this.router.navigate(['/search']);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为当您执行这个 URL 时,`SearchComponent`不会自动执行搜索，所以在它的`ngOnInit`方法中添加以下逻辑来执行搜索。

```
import { ActivatedRoute } from '@angular/router';
import { Subscription } from 'rxjs';
...

sub: Subscription;

constructor(private searchService: SearchService, private route: ActivatedRoute) { }

ngOnInit() {
  this.sub = this.route.params.subscribe(params => {
    if (params['term']) {
      this.query = decodeURIComponent(params['term']);
      this.search();
    }
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

您需要实现`OnDestroy`并定义`ngOnDestroy`方法来清理这个订阅。

```
import { Component, OnInit, OnDestroy } from '@angular/core';

export class SearchComponent implements OnInit, OnDestroy {
...
  ngOnDestroy() {
    if (this.sub) {
      this.sub.unsubscribe();
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

完成所有这些更改后，您应该能够搜索/编辑/更新一个人的信息。如果成功了-干得好！

### 表单验证

您可能会注意到，您可以清除表单中的任何输入元素并保存它。至少，`name`字段应该是必需的。否则，搜索结果中就没有可点击的内容。

要使名称成为必需的，修改`edit.component.html`以给名称`<input>`添加一个`required`属性。

```
<input [(ngModel)]="editName" name="name" id="name" placeholder="name" required/> 
```

Enter fullscreen mode Exit fullscreen mode

您还需要将所有内容包装在一个`<form>`元素中。在`<h3>`标签后添加`<form>`，在最后一个`</div>`前关闭。您还需要向表单添加一个`(ngSubmit)`处理程序，并将保存按钮改为常规的提交按钮。

```
<h3>{{editName}}</h3>
<form (ngSubmit)="save()" ngNativeValidate>
  ...
  <button type="submit" id="save">Save</button>
  <button (click)="cancel()" id="cancel">Cancel</button>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

做出这些更改后，任何带有`required`属性的字段都将是必需的。

[![Edit form with validation](../Images/211420863e3d24834cfe832ecd7e4a8b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EEULLf5C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/angular-oidc/edit-form-validation-f082b5b4d30d382d746e72e8039354367bf250ab30dbca0912b5856596f7faac.png)

在这个屏幕截图中，您可能会注意到地址栏是空白的。这是由您的控制台中的错误解释的。

```
If ngModel is used within a form tag, either the name attribute must be set or the form
control must be defined as 'standalone' in ngModelOptions.

Example 1: <input [(ngModel)]="person.firstName" name="first">
Example 2: <input [(ngModel)]="person.firstName" [ngModelOptions]="{standalone: true}"> 
```

Enter fullscreen mode Exit fullscreen mode

要解决这个问题，需要在所有的地址字段中添加一个`name`属性。比如:

```
<address>
  <input [(ngModel)]="editAddress.street" name="street" id="street"><br/>
  <input [(ngModel)]="editAddress.city" name="city" id="city">,
  <input [(ngModel)]="editAddress.state" name="state" id="state" size="2">
  <input [(ngModel)]="editAddress.zip" name="zip" id="zip" size="5">
</address> 
```

Enter fullscreen mode Exit fullscreen mode

现在，值应该显示在所有字段中，并且`name`应该是必需的。

[![Edit form with names and validation](../Images/86bc2883f01ddb5f25a1d5ff300909a1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J71UTV7p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/angular-oidc/edit-form-names-cb13cfc33184a14c341f67e1e833e5491e83dac660e218b650096208d03304c2.png)

如果您想提供自己的验证消息，而不是依赖于浏览器的，请完成以下步骤:

1.  移除`ngNativeValidate`并将`#editForm="ngForm"`添加到`<form>`元素中。
2.  将`#name="ngModel"`添加到`<input id="name">`元素中。
3.  将`[disabled]="!editForm.form.valid"`添加到*保存*按钮。
4.  在`name`字段下添加以下内容，以显示验证错误。

```
<div [hidden]="name.valid || name.pristine" style="color: red">
  Name is required
</div> 
```

Enter fullscreen mode Exit fullscreen mode

要了解更多关于表单和验证的信息，请参见[角度表单文档](https://angular.io/guide/forms)。

## 在 Okta 创建一个 OpenID Connect App

OpenID Connect (OIDC)建立在 OAuth 2.0 协议之上。它允许客户端验证用户的身份，并获取他们的基本配置文件信息。要了解更多信息，请参见 https://openid.net/connect。

要集成 [Okta](https://developer.okta.com) 进行用户认证，您首先需要[注册](https://developer.okta.com/signup/)并创建一个 OIDC 应用程序。

登录你的 Okta 账户，或者[创建一个](https://developer.okta.com/signup/)，如果你没有的话。导航至**应用程序**并点击**添加应用程序**按钮。选择 **SPA** 并点击下一个的**。在下一页中，将`http://localhost:4200`指定为基本 URI，登录重定向 URI，注销重定向 URI。点击**完成**，您应该会看到如下设置。**

[![OIDC App Settings](../Images/298bbdce8d83237bf95964ddc089631f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3lIuDtnY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/angular-oidc/oidc-settings-c85c854f381bdca5d4d8835918918ecc20f5721de75db8797daa4e427c741941.png)

使用 npm 将 [Manfred Steyer 的](https://github.com/manfredsteyer)项目安装到[添加 OAuth 2 和 OpenID 连接支持](https://github.com/manfredsteyer/angular-oauth2-oidc)。你还需要 [rxjs-compat](https://www.npmjs.com/package/rxjs-compat) ，因为这个库还没有升级到 RxJS 6。

```
npm install angular-oauth2-oidc rxjs-compat 
```

Enter fullscreen mode Exit fullscreen mode

修改`src/app/app.component.ts`以导入`OAuthService`，并配置您的应用程序以使用您的 Okta 应用程序的设置。

```
import { OAuthService, JwksValidationHandler } from 'angular-oauth2-oidc';

...

  constructor(private oauthService: OAuthService) {
    this.oauthService.redirectUri = window.location.origin;
    this.oauthService.clientId = '{clientId}';
    this.oauthService.scope = 'openid profile email';
    this.oauthService.issuer = 'https://{yourOktaDomain}.com/oauth2/default';
    this.oauthService.tokenValidationHandler = new JwksValidationHandler();

    // Load Discovery Document and then try to login the user
    this.oauthService.loadDiscoveryDocumentAndTryLogin();
  }
... 
```

Enter fullscreen mode Exit fullscreen mode

创建`src/app/home/home.component.ts`并将其配置为拥有**登录**和**注销**按钮。

```
import { Component } from '@angular/core';
import { OAuthService } from 'angular-oauth2-oidc';

@Component({
  template: `
    <div *ngIf="givenName">
      <h2>Welcome, {{givenName}}!</h2>
      <button (click)="logout()">Logout</button>
      <p><a routerLink="/search" routerLinkActive="active">Search</a></p>
    </div>

    <div *ngIf="!givenName">
      <button (click)="login()">Login</button>
    </div>`
})
export class HomeComponent {

  constructor(private oauthService: OAuthService) {
  }

  login() {
    this.oauthService.initImplicitFlow();
  }

  logout() {
    this.oauthService.logOut();
  }

  get givenName() {
    const claims = this.oauthService.getIdentityClaims();
    if (!claims) {
      return null;
    }
    return claims['name'];
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果用户未通过身份验证，创建`src/app/shared/auth/auth.guard.service.ts`以导航到`HomeComponent`。

```
import { Injectable } from '@angular/core';
import { ActivatedRouteSnapshot, CanActivate, Router, RouterStateSnapshot } from '@angular/router';
import { OAuthService } from 'angular-oauth2-oidc';

@Injectable({
  providedIn: 'root'
})
export class AuthGuard implements CanActivate {

  constructor(private oauthService: OAuthService, private router: Router) {}

  canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): boolean {
    if (this.oauthService.hasValidIdToken()) {
      return true;
    }

    this.router.navigate(['/home']);
    return false;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在`src/shared/index.ts` :
中导出`AuthGuard`

```
export * from './auth/auth.guard.service'; 
```

Enter fullscreen mode Exit fullscreen mode

导入`src/app/app.module.ts`中的`OAuthModule`，配置新的`HomeComponent`，用`AuthGuard`锁定`/search`和`/edit`路线。

```
import { OAuthModule } from 'angular-oauth2-oidc';
import { HomeComponent } from './home/home.component';
import { AuthGuard } from './shared';

const appRoutes: Routes = [
  { path: 'search', component: SearchComponent, canActivate: [AuthGuard] },
  { path: 'edit/:id', component: EditComponent, canActivate: [AuthGuard] },
  { path: 'home', component: HomeComponent },
  { path: '', redirectTo: 'home', pathMatch: 'full' },
  { path: '**', redirectTo: 'home' }
];

@NgModule({
  declarations: [
    ...
    HomeComponent
  ],
  imports: [
    ...
    OAuthModule.forRoot()
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

Enter fullscreen mode Exit fullscreen mode

完成这些更改后，您应该能够运行`ng serve`并看到一个登录按钮。

[![Login button](../Images/8c4831f4ccf0ebe522bd6f9058c956d0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Bp2Xk2QA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/angular-oidc/okta-login-button-2ee8674edc8ecbf88bbffac2b40d223797622773da3f8def4135480b3e4923b9.png)

点击**登录**按钮，用 Okta 应用程序中配置的一个人登录。

[![Okta Login form](../Images/28720af1acf69fd3cee6647af37c0e3f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kufFDk6G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/angular-oidc/okta-login-form-d1905a5f3128aedff316a1730e5f7f54076c0fa739cc8b3ac7101144ddf28980.png)

登录后，您可以点击*搜索*并查看联系人信息。

[![View after login](../Images/e50146376f9adee60f00cfdf33a3000e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1CwoXOLj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/angular-oidc/okta-post-login-2c35ca453a4755d0ad3b0ce4717885a735bb43e1c1dfdb26b7aba3af74de8460.png)

如果成功了-太好了！如果你想在你的应用中构建自己的登录表单，继续阅读了解如何使用 [Okta Auth SDK](https://github.com/okta/okta-auth-js) 和`OAuthService`。

### 用 Okta Auth SDK 认证

Okta Auth SDK 建立在 Okta 的[认证 API](https://dev.to/docs/api/resources/authn) 和 [OAuth 2.0 API](https://dev.to/docs/api/resources/oidc) 之上，使您能够使用 JavaScript 创建一个完全品牌化的登录体验。

使用 npm 安装它:

```
npm install @okta/okta-auth-js 
```

Enter fullscreen mode Exit fullscreen mode

本节中的组件使用引导 CSS 类。安装引导程序 4。

```
npm install bootstrap 
```

Enter fullscreen mode Exit fullscreen mode

修改`src/styles.css`以添加对 Bootstrap 的 CSS 文件的引用。

```
@import "~bootstrap/dist/css/bootstrap.css"; 
```

Enter fullscreen mode Exit fullscreen mode

更新`src/app/app.component.html`为它的导航条和网格系统使用引导类。

```
<nav class="navbar navbar-light bg-secondary">
  <a class="navbar-brand text-light" href="#">Welcome to {{title}}!</a>
</nav>
<div class="container-fluid">
  <router-outlet></router-outlet>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

创建`src/app/shared/auth/okta.auth.wrapper.ts`包装 Okta Auth SDK 并与`OAuthService`集成。它的
`login()`方法使用`OktaAuth`获得一个会话令牌，并将其交换为 ID 和访问令牌。

```
import { OAuthService } from 'angular-oauth2-oidc';
import { Injectable } from '@angular/core';
import * as OktaAuth from '@okta/okta-auth-js';

@Injectable({
  providedIn: 'root'
})
export class OktaAuthWrapper {

  private authClient: any;

  constructor(private oauthService: OAuthService) {
    this.authClient = new OktaAuth({
      url: 'https://{yourOktaDomain}.com',
      issuer: 'default'
    });
  }

  login(username: string, password: string): Promise<any> {
    return this.oauthService.createAndSaveNonce().then(nonce => {
      return this.authClient.signIn({
        username: username,
        password: password
      }).then((response) => {
        if (response.status === 'SUCCESS') {
          return this.authClient.token.getWithoutPrompt({
            clientId: this.oauthService.clientId,
            responseType: ['id_token', 'token'],
            scopes: ['openid', 'profile', 'email'],
            sessionToken: response.sessionToken,
            nonce: nonce,
            redirectUri: window.location.origin
          })
            .then((tokens) => {
              const idToken = tokens[0].idToken;
              const accessToken = tokens[1].accessToken;
              const keyValuePair = `#id_token=${encodeURIComponent(idToken)}&access_token=${encodeURIComponent(accessToken)}`;
              return this.oauthService.tryLogin({
                customHashFragment: keyValuePair,
                disableOAuth2StateCheck: true
              });
            });
        } else {
          return Promise.reject('We cannot handle the ' + response.status + ' status');
        }
      });
    });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，`oauthService.tryLogin()`解析并存储了`idToken`和`accessToken`，因此可以使用`OAuthService.getIdToken()`和`OAuthService.getAccessToken()`来检索
。

在`src/shared/index.ts` :
中导出`OktaAuthWrapper`

```
export * from './auth/okta.auth.wrapper'; 
```

Enter fullscreen mode Exit fullscreen mode

更改`HomeComponent`来修改它的`template`,这样它就有了一个登录按钮和一个登录表单。

```
@Component({
  template: `
    <div *ngIf="givenName" class="col-12 mt-2">
      <button (click)="logout()" class="btn btn-sm btn-outline-primary float-right">Logout</button>
      <h2>Welcome, {{givenName}}!</h2>
      <p><a routerLink="/search" routerLinkActive="active">Search</a></p>
    </div>

    <div class="card mt-2" *ngIf="!givenName">
      <div class="card-body">
        <h4 class="card-title">Login with Authorization Server</h4>
        <button class="btn btn-primary" (click)="login()">Login</button>
      </div>
    </div>

    <div class="card mt-2" *ngIf="!givenName">
      <div class="card-body">
        <h4 class="card-title">Login with Username/Password</h4>

        <p class="alert alert-error" *ngIf="loginFailed">
          Login wasn't successful.
        </p>

        <div class="form-group">
          <label>Username</label>
          <input class="form-control" [(ngModel)]="username">
        </div>
        <div class="form-group">
          <label>Password</label>
          <input class="form-control" type="password" [(ngModel)]="password">
        </div>
        <div class="form-group">
          <button class="btn btn-primary" (click)="loginWithPassword()">Login</button>
        </div>
      </div>
    </div>`
}) 
```

Enter fullscreen mode Exit fullscreen mode

做出这些更改后，`HomeComponent`应该呈现如下。

[![Custom sign-in form](../Images/e765222c8edf0c8e543fc9b3cf3000a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bkSKc70k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/angular-oidc/sign-in-form-f5e01d92405613c4a90eb61ed8974d98e290034eca19dbbe1efbd89a05f8fe56.png)

为用户名和密码字段添加局部变量，导入`OktaAuthWrapper`，并在`HomeComponent`中实现一个`loginWithPassword()`方法。

```
import { OktaAuthWrapper } from '../shared';
...

username;
password;

constructor(private oauthService: OAuthService,
            private oktaAuthWrapper: OktaAuthWrapper) {
}

loginWithPassword() {
  this.oktaAuthWrapper.login(this.username, this.password)
    .catch(err => console.error('error logging in', err));
} 
```

Enter fullscreen mode Exit fullscreen mode

您应该能够使用应用程序的一个注册用户使用该表单登录。登录后，您可以点击**搜索**链接，查看人们的信息。

[![View after sign-in](../Images/9bb854bdb51f670aac2fbc79cff43e74.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C8mysX2S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/angular-oidc/sign-in-form-success-c2d36d789b09ab7078b25115bf5b7e980843e6cad8759996b9c69286a4b458de.png)

## 角+八度

如果一切正常，恭喜你！如果你遇到了问题，请用 [okta 标签](http://stackoverflow.com/questions/tagged/okta)向 Stack Overflow 提出问题，或者在 Twitter [@mraible](https://twitter.com/mraible) 上联系我。

你可以在 GitHub 上找到这篇博文[中创建的应用程序的完整版本。要了解 Angular 的更多安全信息，请参见](https://github.com/oktadeveloper/okta-angular-openid-connect-example) [Angular 的安全文档](https://angular.io/guide/security)。