# 为 Angular part 2 开发一个跨平台的 Angular 和 Ignite UI 应用程序

> 原文：<https://dev.to/kenakamu/develop-a-cross-platform-application-by-angular-and-ignite-ui-for-angular-part-2--483e>

在[上一篇文章](https://dev.to/kenakamu/develop-a-cross-platform-application-by-angular-and-ignite-ui-for-angular-part-1-22cc)中，我开发了一个具有 CRUD 功能的简单应用程序。在本文中，我将使用其他 Ignite UI 组件更新相同的应用程序。

# 添加菜单

目前，该应用程序只有列表功能。但是，如果我添加更多的功能，我希望有菜单来轻松导航它们。

## 使用导航抽屉

Ignite UI 具有 NavDrawer 控件，可让您实现飞出式菜单。

1.首先添加模块。模块的名字是 NavigationDrawerModule，而且不是以“Igx”开头。这应该会在未来的版本中改变，因为在 GitHub 中有[个问题被跟踪](https://github.com/IgniteUI/igniteui-angular/issues/512)

src/app/app.module.ts

```
import { BrowserModule } from '@angular/platform-browser';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import {
  IgxNavbarModule, IgxIconModule, IgxScrollModule, IgxAvatarModule,
  IgxLabelModule, IgxInput, IgxDatePickerModule, IgxButtonModule, IgxRippleModule,
  IgxToastModule, IgxDialogModule, IgxCheckboxModule, IgxSwitchModule, IgxSliderModule,
  IgxRadioModule, NavigationDrawerModule } from 'igniteui-angular/main';
import "hammerjs";
import { ListComponent } from './list/list.component';
import { DetailComponent } from './detail/detail.component';
import { UserService } from './user/user.service';
import { NewComponent } from './new/new.component';

@NgModule({
  declarations: [
    AppComponent,
    ListComponent,
    DetailComponent,
    NewComponent
  ],
  imports: [
    BrowserModule,
    BrowserAnimationsModule,
    AppRoutingModule,
    FormsModule,
    IgxNavbarModule, 
    IgxIconModule,
    IgxScrollModule, 
    IgxAvatarModule, 
    IgxLabelModule, 
    IgxInput,
    IgxDatePickerModule,
    IgxButtonModule, 
    IgxRippleModule, 
    IgxToastModule,     
    IgxDialogModule,　
    IgxCheckboxModule, 
    IgxSwitchModule, 
    IgxSliderModule,
    IgxRadioModule,　
    NavigationDrawerModule 
  ],
  providers: [UserService],
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

Enter fullscreen mode Exit fullscreen mode

2.我将抽屉添加到应用程序模块。我将 pinThreashold 设置为 false 的原因是，默认情况下，如果屏幕宽度大于 1024，它的行为会有所不同。

src/app/app.component.html

```
<igx-navbar [title]="title" actionButtonIcon="menu" (onAction)="drawer.toggle()">
  <igx-icon name="add" (click)="onClickAdd()"></igx-icon>
</igx-navbar>
<!-- Add Drawer -->
<igx-nav-drawer #drawer [pinThreshold]="false" width="280px">
  <div class="ig-drawer-content">
    <nav class="nav">
      <!-- Drawer Header -->
      <span class="nav-item header">menus</span>
      <!-- Add menus -->
      <span class="nav-item" [routerLinkActive]="'active'" routerLink="/">
        <igx-icon fontSet="material" name="list"></igx-icon>
        <span>List</span>
      </span>
      <span class="nav-item" [routerLinkActive]="'active'" routerLink="grid">
        <igx-icon fontSet="material" name="grid_on"></igx-icon>
        <span>Grid</span>
      </span>
    </nav>
  </div>
</igx-nav-drawer>

<router-outlet></router-outlet> 
```

Enter fullscreen mode Exit fullscreen mode

3.用 css 让菜单看起来很漂亮。我从 [GitHub](https://github.com/IgniteUI/igniteui-angular/blob/master/demos/app/app.component.css) 中获取了样本 css。有趣的是，NavDrawer 项放在 app.component 中，而不是放在 NavDrawer 示例文件夹中。

src/app/app.component.css

```
.ig-drawer-content {
    background: #fff;
}

.ig-drawer-content ::-webkit-scrollbar {
    width: 3px;
    background: #e4e4e4;
}

.ig-drawer-content ::-webkit-scrollbar-thumb {
    background: #ec6f74;
    border-radius: 0;
}

.nav {
    position: absolute;
    width: 100%;
    height: 100%;
    border-right: 1px solid #e4e4e4;
    overflow-y: scroll;
    overflow-x: hidden;
}

.nav-item {
    display: flex;
    flex: 1 0 100%;
    flex-flow: row nowrap;
    color: #383838;
    max-height: 48px;
    padding: 12px 16px;
    cursor: pointer;
    font-size: 14px;
    align-items: center;
    font-weight: 600;
    user-select: none;
    outline: transparent;
    white-space: nowrap;
}

.nav-item.active {
    background-color: #fcc5de;
}

.nav-item:hover {
    background-color: #f0f0f0;
}

.nav-item > * + * {
    margin-left: 32px;
}

.nav-item.header {
    font-size: 16px;
    color: #9c9c9c;
    font-weight: 400;
    cursor: initial;
}

.nav-item.header:hover {
    background-color: transparent;
} 
```

Enter fullscreen mode Exit fullscreen mode

4.把它们都存起来，检查一下效果如何。

[![Capture.PNG](img/451ff1d6fbf595ea7352e2fa74ff67fd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7NkWV6s1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/214116/a745d433-a000-5d88-237d-4adb0fcf00b1.png)

5.如您所见，menu 按预期工作，但它隐藏了菜单栏。我更喜欢
显示在菜单栏下面，所以添加额外的 css 元素。

src/app/app.component.css

```
/* Add this at the end of existing css */
igx-nav-drawer >>> .ig-nav-drawer-overlay,
igx-nav-drawer >>> .ig-nav-drawer {
 margin-top: 56px;
} 
```

Enter fullscreen mode Exit fullscreen mode

6.现在它如我所愿。
[![Capture.PNG](img/24db31c8412ac6365a7be4535cf3d67f.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--YCdMNA6I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/214116/337d6dd0-cbb1-6260-e32a-0614f58103dc.png)

## 将列表组件转换为角度模块

列表功能目前在应用程序模块内实现。在添加其他功能之前，我会将其转换为 Angular 模块，并与 app 模块分开。

1.将新文件夹和详细信息文件夹移到列表文件夹下，因为它们是列表模块的一部分。这并不是必需的，但这样维护起来很容易。它类似于 app.module.ts，但也包含路由信息。通过这种传送方式，新页面和详细页面现在可以作为列表/新页面和列表/详细页面进行访问。

[![Capture.PNG](img/db283d46e688a08ebedc9b47f9899fa7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nvVNV9L7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/214116/448811f8-9c9f-cc25-aa9b-a8324c53825d.png)

2.在 src/app/list 下添加 list.module.ts，添加组件信息。这使得列表模块。

src/app/list/list.module.ts

```
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';
import { Routes, RouterModule } from '@angular/router';

import { ListComponent } from './list.component';
import { DetailComponent } from './detail/detail.component';
import { NewComponent } from './new/new.component';
import {
    IgxIconModule, IgxScrollModule, IgxAvatarModule,
    IgxLabelModule, IgxInput, IgxDatePickerModule, IgxButtonModule, IgxRippleModule,
    IgxToastModule, IgxDialogModule, IgxCheckboxModule, IgxSwitchModule, IgxSliderModule,
    IgxRadioModule
} from 'igniteui-angular/main';

// Specify routes including children.
const listRoutes: Routes = [
    {
        path: 'list', component: ListComponent, children: [
            { path: 'detail/:id', component: DetailComponent },
            { path: 'new', component: NewComponent }
        ]
    }
];

@NgModule({
    declarations: [
        ListComponent,
        DetailComponent,
        NewComponent
    ],
    imports: [
        // Register root module including child modules.
        RouterModule.forChild(listRoutes),
        CommonModule,
        FormsModule,
        IgxScrollModule, 
        IgxAvatarModule,
        IgxLabelModule, 
        IgxInput, 
        IgxDatePickerModule, 
        IgxButtonModule,
        IgxRippleModule,
        IgxToastModule,     
        IgxDialogModule,
        IgxCheckboxModule,
        IgxSwitchModule,
        IgxSliderModule,　
        IgxRadioModule,
    ],
    exports: [RouterModule]
})
export class ListModule { } 
```

Enter fullscreen mode Exit fullscreen mode

3.将列表模块添加到 app.module.ts 中，并从仅在列表模块中使用的 app 模块中删除 Ignite UI 模块。*如果我稍后需要在应用模块中使用它们，我可能需要将它们添加回去。

src/app/app.module.ts

```
import { BrowserModule } from '@angular/platform-browser';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { AppRoutingModule } from './app-routing.module';
import { ListModule } from './list/list.module';
import { AppComponent } from './app.component';
import {
  IgxNavbarModule, IgxIconModule, NavigationDrawerModule } from 'igniteui-angular/main';
import "hammerjs";
import { UserService } from './user/user.service';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    BrowserAnimationsModule,
    AppRoutingModule,
    FormsModule,
    ListModule,
    IgxNavbarModule, 
    IgxIconModule,   
    NavigationDrawerModule
  ],
  providers: [UserService], 
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

Enter fullscreen mode Exit fullscreen mode

4.随着文件夹结构的变化，更新受影响文件的导入路径

src/app/list/new/new . component . ts

```
import { Component, OnInit, ViewChild } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { User, Gender } from '../../models/user';
import { UserService } from '../../user/user.service';
import { IgxLabel, IgxInput, IgxAvatar, IgxToast, IgxDialog, IgxCheckbox, IgxSwitch, IgxSlider, IgxRadio } from 'igniteui-angular/main';
import { Router } from '@angular/router';
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-new',
  templateUrl: './new.component.html',
  styleUrls: ['./new.component.css']
})
export class NewComponent implements OnInit {

  constructor(private userService: UserService, private router: Router) {   
  }

  @ViewChild('toast') toast: IgxToast;
  public user: User;
  public gender: string[];

  ngOnInit() {
    this.user = new User("", "", 0, null, Gender.Other, 0, true);
    let genderValues = Object.keys(Gender);
    this.gender = genderValues.slice(genderValues.length / 2);
  }

  public loadImage(input: HTMLInputElement): void {
    if (!input.value) {
      return;
    }

    let reader = new FileReader();
    // Callback when file read.
    reader.onload = () => {
      input.value = "";
      this.user.image = reader.result;
    }

    reader.readAsDataURL(input.files[0]);
  }

  public create() {
    this.userService.add(this.user).subscribe(() => {
      this.toast.show();
      this.router.navigate([`/`]);
    });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

src/app/list/detail/detail . component . ts

```
import { Component, OnInit, ViewChild } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { ActivatedRoute } from '@angular/router';
import { IgxLabel, IgxInput, IgxAvatar, IgxToast, IgxDialog } from 'igniteui-angular/main';
import { UserService } from '../../user/user.service';
import { User } from '../../models/user';
import { Router } from '@angular/router';

@Component({
  selector: 'app-detail',
  templateUrl: './detail.component.html',
  styleUrls: ['./detail.component.css']
})

export class DetailComponent implements OnInit {

  @ViewChild('toast') toast: IgxToast;
  public user: User;
  constructor( private route: ActivatedRoute, private router: Router,private userService: UserService ) {
  }

  ngOnInit() {
    this.route.params.subscribe(params => {
      this.userService.getUser(params.id).subscribe(
        (user) => {this.user = user;}
      );
    });
  }

  public save(){
    this.userService.save(this.user).subscribe(()=>{
      this.toast.show();
    });    
  }

  public delete(){
    this.userService.delete(this.user).subscribe(()=>{
      this.toast.message = "deleted";
      this.toast.show();
      this.router.navigate([`/`]);
    })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

5.此外，新页面和详细信息页面的传送方式已更改，更新受影响的文件。

src/app/list/list . component . ts

```
import { Component, OnInit, EventEmitter } from '@angular/core';
import { IgxScroll, IgxScrollEvent, IgxAvatar } from "igniteui-angular/main";
import { User } from '../models/user';
import { UserService } from '../user/user.service';
import { Router } from '@angular/router';

@Component({
  selector: 'app-list',
  templateUrl: './list.component.html',
  styleUrls: ['./list.component.css']
})
export class ListComponent implements OnInit {

  constructor(private userService: UserService, private router: Router) {
    this.load();
  }

  public users: User[] = new Array<User>();
  public visibleUsers: User[];
  public visibleUsersCount: number = 8;

  ngOnInit() {
    this.userService.userUpdateSource$.subscribe(
      (user)=>{this.load();}
    )
  }

  public load():void{    
    this.userService.getUsers().subscribe(
      (users) => {
        this.users = users;
        this.visibleUsers = this.users.slice(0, this.visibleUsersCount);
      }
    );
  }

  private onItemSelect(user: User): void {
    this.router.navigate([`/list/detail/${user.id}`]);
  }

  private updateList($event: IgxScrollEvent): void {    
    this.visibleUsers = this.users.slice($event.currentTop, $event.currentTop + this.visibleUsersCount);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

src/app/app.component.ts

```
import { Component, ViewChild, ElementRef } from '@angular/core';
import { User } from './models/user';
import { Router } from '@angular/router';
import { IgxNavbar, IgxIcon, NavigationDrawer } from 'igniteui-angular/main';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})

export class AppComponent {

  selectedUser: User;

  title = 'Ignite Ui App';

  constructor(private router: Router) {
  }

  onClickAdd(){
    this.router.navigate(['/list/new']);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

6.为 list.component.html 添加路由器出口。还要更新 css 以适应变化。

src/app/list/list . component . html

```
<igx-scroll #scroll (onScroll)="updateList($event)" 
    [visibleItemsCount]="visibleUsersCount" 
    [itemHeight]="70" 
    [totalItemsCount]="users.length">
    <ul class="list">
        <li class="list-item" *ngFor="let user of visibleUsers" (click)="onItemSelect(user)">
            <igx-avatar class="list-item-image" roundShape="true" src="{{user.image}}"></igx-avatar>
            <h5 class="list-item-value">{{user.name}}</h5>
        </li>
    </ul>
</igx-scroll>

<router-outlet></router-outlet> 
```

Enter fullscreen mode Exit fullscreen mode

src/app/list/list . component . CSS

```
.list {
    width: 250px;
}

.list-item {
    list-style: none;
    height: 64px;
    display: flex;
    flex-direction: row;
    justify-content: flex-start;
    align-items: center;    
}

.list-item-image, .list-item-value {
    flex-grow: 1;
    flex-shrink: 1;
    flex-basis: auto;
}

igx-scroll, router-outlet {
    float: left;
} 
```

Enter fullscreen mode Exit fullscreen mode

7.最后，更新应用程序路由。

src/app/app-routing . module . ts

```
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ListComponent } from './list/list.component';

const routes: Routes = [
  // Display List by default.
  { path: '', component: ListComponent }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { } 
```

Enter fullscreen mode Exit fullscreen mode

8.将它们全部保存，并确认它按预期工作。嗯，制作模块花了一些时间，但值得一做:)

# 数据网格控制

Ignite UI 提供了数据网格控件，该控件具有许多处理表格格式数据的功能。让我们试一试。

1.与我刚刚创建的列表模块不同，这次我添加了 grid 作为组件。运行以下命令来生成网格组件。

```
ng generate component grid 
```

Enter fullscreen mode Exit fullscreen mode

2.添加 Ignite UI 网格模块。它与我需要调用的其他模块略有不同。forRoot 函数。不过，这种情况在未来可能会有所改变。

src/app/app.module.ts

```
import { BrowserModule } from '@angular/platform-browser';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { AppRoutingModule } from './app-routing.module';
import { ListModule } from './list/list.module';
import { AppComponent } from './app.component';
import { GridComponent } from './grid/grid.component';
import { UserService } from './user/user.service';
import {
  IgxNavbarModule, IgxIconModule, NavigationDrawerModule,
  IgxGridModule, } from 'igniteui-angular/main';
import "hammerjs";

@NgModule({
  declarations: [
    AppComponent,
    GridComponent    
  ],
  imports: [
    BrowserModule,
    BrowserAnimationsModule,
    AppRoutingModule,
    FormsModule,
    ListModule,
    IgxNavbarModule, 
    IgxIconModule, 
    NavigationDrawerModule, 
    IgxGridModule.forRoot(), 
  ],
  providers: [UserService],
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

Enter fullscreen mode Exit fullscreen mode

3.将网格控件添加到网格组件。首先，我尝试[自动生成]功能。

src/app/grid/grid . component . html

```
<igx-grid [data]="users" [autoGenerate]="true" [paging]="true" [perPage]="8">
</igx-grid> 
```

Enter fullscreen mode Exit fullscreen mode

src/app/grid/grid . component . ts

```
import { Component, OnInit } from '@angular/core';
import { User } from '../models/user';
import { UserService } from '../user/user.service';

@Component({
  selector: 'app-grid',
  templateUrl: './grid.component.html',
  styleUrls: ['./grid.component.css']
})

export class GridComponent implements OnInit {

  private users: User[] = new Array<User>();
  constructor(private userService: UserService) { }

  ngOnInit() {
    this.userService.getUsers().subscribe((users) => this.users = users);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

4.添加路由，以便我可以访问网格组件。

src/app/app-routing . module . ts

```
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ListComponent } from './list/list.component';
import { GridComponent } from './grid/grid.component';

const routes: Routes = [
  { path: '', component: ListComponent },
  { path: 'grid', component: GridComponent }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { } 
```

Enter fullscreen mode Exit fullscreen mode

5.全部保存，进入[http://localhost:4200/grid](http://localhost:4200/grid)。所有字段都会自动添加。不错:)

[![Capture.PNG](img/c0b6a9134ca71b7b9e4fbbefc42a6fdc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YukNLCa---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/214116/024fbc7b-078f-bf76-eca8-ea197d899449.png)

6.嗯，自动生成功能很好，但我想控制显示哪些列。接下来，添加每个单独的列，并通过使用其他控件(如 Avatar)使用模板来显示值。首先添加必要的模块。

src/app/app.module.ts

```
import { BrowserModule } from '@angular/platform-browser';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { AppRoutingModule } from './app-routing.module';
import { ListModule } from './list/list.module';
import { AppComponent } from './app.component';
import { GridComponent } from './grid/grid.component';
import { UserService } from './user/user.service';
import {
  IgxNavbarModule, IgxIconModule, NavigationDrawerModule,
  IgxGridModule, IgxAvatarModule } from 'igniteui-angular/main';
import "hammerjs";

@NgModule({
  declarations: [
    AppComponent,
    GridComponent    
  ],
  imports: [
    BrowserModule,
    BrowserAnimationsModule,
    AppRoutingModule,
    FormsModule,
    ListModule,
    IgxNavbarModule, 
    IgxIconModule, 
    NavigationDrawerModule,
    IgxGridModule.forRoot(), 
    IgxAvatarModule,
  ],
  providers: [UserService], 
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

Enter fullscreen mode Exit fullscreen mode

7.更新网格组件。使用 igx-column 和 ng-template 显示列。let-*是将属性转换为变量的 ng-template 特性。let-col 创建 col 变量，您可以使用它来显示实际值。

src/app/grid/grid . component . ts

```
import { Component, OnInit } from '@angular/core';
import { User } from '../models/user';
import { UserService } from '../user/user.service';
import { IgxIcon, IgxAvatar } from 'igniteui-angular/main';

@Component({
  selector: 'app-grid',
  templateUrl: './grid.component.html',
  styleUrls: ['./grid.component.css']
})

export class GridComponent implements OnInit {

  private users: User[] = new Array<User>();
  constructor(private userService: UserService) { }

  ngOnInit() {
    this.userService.getUsers().subscribe((users) => this.users = users);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

src/app/grid/grid . component . html

```
<igx-grid #grid [data]="users" [autoGenerate]="false" [paging]="true" [perPage]="6">
  <igx-column width="80px" field="userRank" header="Rank" dataType="number" sortable="true"></igx-column>
  <igx-column width="120px" field="image">
    <ng-template igxCell let-col>
      <igx-avatar size="medium" roundShape="true" src="{{col}}"></igx-avatar>
    </ng-template>
  </igx-column>
  <igx-column field="name" header="User Name" dataType="string" editable="true"></igx-column>
  <igx-column field="birthdate" header="BirthDate" dataType="Date">
    <ng-template igxCell let-col>
      {{col.toDateString()}}
    </ng-template>
  </igx-column>
  <igx-column width="120px" field="gender" header="Gender" dataType="Gender"></igx-column>
  <igx-column width="120px" field="isAdmin" header="Is Admin" dataType="boolean">
    <ng-template igxCell let-col>
      <igx-icon *ngIf="col" fontSet="material" name="star" color="red"></igx-icon>
      <igx-icon *ngIf="!col" fontSet="material" name="star_border"></igx-icon>
    </ng-template>
  </igx-column>
</igx-grid> 
```

Enter fullscreen mode Exit fullscreen mode

8.保存它们并检查网格是否按预期显示数据。

[![Capture.PNG](img/dfd0ae874a9c1f521549b88157d51df0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IdvgrV6R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/214116/892d9578-8b43-0879-5993-0428068311f7.png)

9.如果您单击等级标题，它会更改顺序，因为它被指定为可排序的。如果您双击每一行的名称，您可以编辑它，因为它被指定为可编辑的，尽管我没有实现任何方法来接受更改。

数据网格也提供搜索特性，但是我没有在这里实现它。

# 使用卡片创建平铺页面

数据网格以表格格式显示数据。如果希望数据以平铺格式显示，则可以使用 Ignite UI 卡控件。

1.为卡添加组件。

```
ng generate component card 
```

Enter fullscreen mode Exit fullscreen mode

2.然后添加 Ignite UI 卡模块。

src/app/app.module.ts

```
import { BrowserModule } from '@angular/platform-browser';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { AppRoutingModule } from './app-routing.module';
import { ListModule } from './list/list.module';
import { AppComponent } from './app.component';
import { GridComponent } from './grid/grid.component';
import { CardComponent } from './card/card.component';
import { UserService } from './user/user.service';
import {
  IgxNavbarModule, IgxIconModule, NavigationDrawerModule,
  IgxGridModule, IgxAvatarModule, IgxCardModule
} from 'igniteui-angular/main';
import "hammerjs";

@NgModule({
  declarations: [
    AppComponent,
    GridComponent,
    CardComponent
  ],
  imports: [
    BrowserModule,
    BrowserAnimationsModule,
    AppRoutingModule,
    FormsModule,
    ListModule,
    IgxNavbarModule, 
    IgxIconModule, 
    NavigationDrawerModule,
    IgxGridModule.forRoot(), 
    IgxAvatarModule, 
    IgxCardModule, 
  ],
  providers: [UserService],
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

Enter fullscreen mode Exit fullscreen mode

3.更新路由，以便我可以访问卡片页面。我还为列表页面添加了显式路径。

src/app/app-routing . module . ts

```
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ListComponent } from './list/list.component';
import { GridComponent } from './grid/grid.component';
import { CardComponent } from './card/card.component';

const routes: Routes = [
  { path: '', component: ListComponent },
  { path: 'list', component: ListComponent },
  { path: 'grid', component: GridComponent },
  { path: 'card', component: CardComponent }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { } 
```

Enter fullscreen mode Exit fullscreen mode

4.然后展开用户模型，使其具有存储背景图像 url 的属性。还要更新用户服务以存储每个用户的图像 url。我从 Infragistics 演示页面借用了图片 url，还是:D

src/modes/user.ts

```
export class User {
    public image: string
    public name: string
    public id: number
    public birthdate: Date
    public gender: Gender
    public userRank: number
    public isAdmin: boolean
    public backgroundImage: string

    constructor(image: string, name: string, id: number, birthdate: Date,
    gender: Gender, userRank: number, isAdmin: boolean, backgroundImage: string ) {
        this.image = image;
        this.name = name;
        this.id = id;
        this.birthdate = birthdate;
        this.gender = gender;
        this.userRank = userRank;
        this.isAdmin = isAdmin;
        this.backgroundImage = backgroundImage;
    }
}

export enum Gender {
    Male = 1,
    Female,
    Other,
} 
```

Enter fullscreen mode Exit fullscreen mode

src/user/user.service.ts

```
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs/Observable';
import { of } from 'rxjs/observable/of';
import { User, Gender } from '../models/user'
import { Subject } from 'rxjs/Subject';

@Injectable()
export class UserService {

  private users: Array<User>;
  private userUpdate = new Subject<string>()
  public userUpdateSource$ = this.userUpdate.asObservable();

  constructor() {
    this.users = new Array<User>();
    for (let i = 1; i <= 22; i++) {
      let birthdate = new Date(2018, 0, i);
      let backgrounImage = "http://www.infragistics.com/angular-demos/asseimg/card/media/ny.jpg";
      if( i % 4 == 1){
        backgrounImage = "http://www.infragistics.com/angular-demos/asseimg/card/media/yosemite.jpg";
      }
      else if (i%4 == 2){
        backgrounImage = "http://www.infragistics.com/angular-demos/asseimg/card/media/monuments.jpg";
      }
      else if (i%4 == 3){
        backgrounImage = "http://www.infragistics.com/angular-demos/asseimg/card/media/the_red_ice_forest.jpg";
      }
      this.users.push(new User(
        `http://www.infragistics.com/angular-demos/asseimg/avatar/${i}.jpg`,
        "User: " + i,
        i,
        birthdate,
        Gender.Other,
        i,
        true,
        backgrounImage
      ));
    }
  }

  getUsers(): Observable<User[]> {
    return of(this.users)
  }

  getUser(id: number): Observable<User> {
    return of(this.users.find(x => x.id === +id));
  }

  add(user: User): Observable<boolean> {
    this.users.push(user);
    this.userUpdate.next("updated");
    return of(true);
  }

  save(user: User): Observable<boolean> {
    let index = this.users.indexOf(user);
    if (index !== -1) {
      this.users[index] = user;
      return of(true);
    }
    else {
      return of(false);
    }
  }

  delete(user: User): Observable<boolean> {
    let index = this.users.indexOf(user);
    if (index !== -1) {
      this.users.splice(index, 1);
      this.userUpdate.next("updated");
      return of(true);
    }
    else {
      return of(false);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

5.更新卡组件以显示卡控件。我也更新 css 来改变它的外观。

src/app/card/card . component . ts

```
import { Component, OnInit } from '@angular/core';
import { User } from '../models/user';
import { UserService } from '../user/user.service';
import { IgxAvatar } from 'igniteui-angular/main';
@Component({
  selector: 'app-card',
  templateUrl: './card.component.html',
  styleUrls: ['./card.component.css']
})
export class CardComponent implements OnInit {

  private users: User[] = new Array<User>();
  constructor(private userService: UserService) { }

  ngOnInit() {
    this.userService.getUsers().subscribe((users) => this.users = users);
  }
  private openUrl(url: string): void {
    window.location.href = url;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

src/app/card/card . component . html

```
<div class="card">
  <div class="card-item" *ngFor="let user of users">
    <igx-card>
      <div style="overflow: hidden">
        <img width="100%" height="100%" src="{{user.backgroundImage}}">
      </div>
      <igx-card-header>
        <igx-avatar class="list-item-image" roundShape="true" src="{{user.image}}"></igx-avatar>
        <h2 class="list-item-value">{{user.name}}</h2>
      </igx-card-header>
      <igx-card-content>
        Rank:{{user.userRank}}
        <br/> Birthday: {{user.birthdate.toDateString()}}

      </igx-card-content>
      <igx-card-actions>
        <button igxButton igxRipple>Like</button>
        <button igxButton igxRipple>Share</button>
      </igx-card-actions>
    </igx-card>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

src/app/card/card . component . CSS

```
.card {
    display: flex;
    flex-flow: row wrap;
}

.card-item{
    max-width:400px;
    padding:10px;
}

igx-card-header {
    background: #f8f8ff;
}

igx-card-content {
    background: gray;
    color:white;
} 
```

Enter fullscreen mode Exit fullscreen mode

6.最后，更新菜单，让卡片链接。我还更新了它，使它在点击菜单项时关闭。

src/app/app.component.html

```
<igx-navbar [title]="title" actionButtonIcon="menu" (onAction)="drawer.toggle()">
  <igx-icon name="add" (click)="onClickAdd()"></igx-icon>
</igx-navbar>
<igx-nav-drawer #drawer [pinThreshold]="false" width="280px">
  <div class="ig-drawer-content">
    <nav class="nav">
      <span class="nav-item header">menus</span>
      <span class="nav-item" [routerLinkActive]="'active'" routerLink="list" (click)="drawer.close();">
        <igx-icon fontSet="material" name="list"></igx-icon>
        <span>List</span>
      </span>
      <span class="nav-item" [routerLinkActive]="'active'" routerLink="grid" (click)="drawer.close();">
        <igx-icon fontSet="material" name="grid_on"></igx-icon>
        <span>Grid</span>
      </span>
      <span class="nav-item" [routerLinkActive]="'active'" routerLink="card" (click)="drawer.close();">
        <igx-icon fontSet="material" name="view_module"></igx-icon>
        <span>Card</span>
      </span>
    </nav>
  </div>
</igx-nav-drawer>

<router-outlet></router-outlet> 
```

Enter fullscreen mode Exit fullscreen mode

7.全部保存后进入[http://localhost:4200/Card](http://localhost:4200/card)，或者使用菜单进入卡片页面。

[![Capture.PNG](img/b510db5979f8164ac44fc8ba850b1cea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DV-ggjAk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/214116/d29287ce-e9ea-cb12-a412-eb0948fd4adf.png)

# 通过移动设备查看应用

Ignite UI 控件具有足够的响应能力，因此可以通过较小的屏幕尺寸很好地使用该应用程序。也有适合手机的控件，如[标签栏](https://www.infragistics.com/products/ignite-ui-angular/angular/components/tabbar.html)。

如果我开发本机应用程序，我可能会使用 Ignite UI 和 Ionic，但我会在以后的某个时间尝试这一点。

# 总结

到目前为止，我很喜欢 Ignite UI。Infragistics 还为 JavaScript、Xamarin、MVC、WPF、iOS、Android 等提供 UI 组件。我将尝试它们，看看我如何在不同的平台上重复使用我的技能。

## 引用

[田径终极赛:所有工具箱概述](https://jp.infragistics.com/products/ultimate)
[田径赛事](https://jp.infragistics.com/events/)
[田径赛事](https://www.infragistics.com/events)