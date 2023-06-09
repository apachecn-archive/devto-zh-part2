# 使用角度路由保护来限制角色的访问

> 原文：<https://dev.to/prestonjlamb/using-angular-route-guards-to-limit-access-by-roles-29pg>

有角度的路由保护非常常用于限制对应用程序部分的访问。也许路由守卫最常见的用途是检查用户是否登录。守卫将检查登录用户的授权状态，如果他们被批准，它将返回 true(或在某个点发出 true 值的可观察值)。之后，应用程序继续执行。

但是，如果他们已经被确认为通过了身份验证，但是他们在您的应用程序中没有某个必要的角色，那么会发生什么呢？例如，假设有一个管理部分，只有一些人应该看到，但每个使用该应用程序的人都需要登录才能看到它。在这种情况下，简单的身份验证是不够的。我们还需要一个守卫，在激活路由之前检查用户的一个或多个角色的帐户。

我最近需要在一个项目中完成这个，所以让我们来看看如何去做。首先，这里是路线守卫:

```
export class RouteCanActivateGuard implements CanActivate {

  constructor(private _permissions: PermissionsStorageService) {}

  canActivate( next: ActivatedRouteSnapshot, state: RouterStateSnapshot): any {
    const roles = next.data['roles'] as Array;

    return this._permissions.checkForAllRolesInArray(roles); // This goes and checks the server for the roles and returns an observable
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

本质上，这个保护从`PermissionsStorageService`函数`checkForAllRolesInArray`返回一个可观察值。那个可观测的最终会发出一个布尔。如果是真的，那么组件就激活了。如果为 false，那么组件将不会被激活，并将停留在用户试图导航时所在的路线上。

不过，让我们看看`canActivate`函数中的第一行。我们看到我们从`next`快照中得到一个字符串数组，我们称之为`roles`。这就是我们要寻找的角色。那么数据集在哪里呢？我们来看看:

```
const routes: Routes = [
  {
    path: '',
    component: LayoutComponent,
    children: [
      {
        path: 'home', 
        component: DashboardComponent, 
        canActivate: [AuthorizationGuard, RouteCanActivateGuard], 
        data: { roles: [ROLES.access]} 
      },
      { 
        path: 'some-path', 
        loadChildren: '../path/to/some-path.module#SomePathModule', 
        canActivate: [AuthorizationGuard, RouteCanActivateGuard], 
        data: { roles: [ROLES.access, ROLES.someRole]} 
      },
    },
]; 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们可以看到`canActivate`数组，在这里我们决定使用哪些路由保护。显然，这就是我们要列出`RouteCanActivateGuard`的地方，这样我们就可以检查我们想要的角色。在这个数组的正下方是`data`对象，它可以提供给每个组件，也可以在警卫中使用。作为`data`对象的一个属性，我们有一个`roles`数组，它包含了激活组件所需的角色列表。上面我使用了一个枚举来确定应该使用的角色，但是如果你愿意，你可以在这里传入字符串。

如您所见，为路由守卫提供角色是一项非常简单的任务，在路由激活之前需要检查这些角色。不需要太多的努力，我们就可以改变守卫，只需要数组中的一个角色，或者你能想到的任何其他变化。