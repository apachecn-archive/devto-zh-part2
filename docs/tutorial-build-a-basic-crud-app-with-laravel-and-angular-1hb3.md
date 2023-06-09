# 教程:用 Laravel 和 Angular 构建一个基本的 CRUD 应用程序

> 原文：<https://dev.to/oktadev/tutorial-build-a-basic-crud-app-with-laravel-and-angular-1hb3>

Laravel 是一个流行的用于 Web 应用程序开发的 PHP 框架，如果您今天开始一个新项目，它是一个非常好的选择，原因有很多:

Laravel 是一个架构良好的框架，易于学习和编写优雅的代码，但功能也很强大。它包含许多现成的高级特性:雄辩的 ORM、对单元/特性/浏览器测试的支持、作业队列等等。这里有丰富的学习资源，它拥有网上最大的社区之一，所以很容易找到熟悉它的开发人员。

Laravel 包括一个不错的模板引擎(通过在服务器端生成 HTML ),它还提供了一个很棒的前端构建工具(基于 webpack 的 Laravel Mix)和 Vue.js，用于构建单页应用程序。正因为如此，Laravel + Vue.js 的组合变成了事实上的标准。然而，Laravel 可能会选择 Vue.js，但这并不意味着你局限于它！

Laravel 最好的“隐藏”特性之一是，使用它可以非常容易地创建一个 REST-ful API，它可以驱动构建在您首选框架中的前端。当然，如果您需要一个开销最小、引导时间少于 40 ms 的高性能 API，您也可以使用轻量级版本的 Laravel，Lumen，但是对于大多数用途，功能齐全的 Laravel 就足够了。今天，我将向您展示建立一个由 Angular 6 应用程序使用的 Laravel API 是多么容易。我们将在我们的应用程序中使用 Okta 进行用户认证和授权，这将允许我们以正确的方式实现安全性，而不会有任何麻烦。

在开始之前，您需要用 PHP 7 和 Node.js 8+/npm 设置一个开发环境。你还需要一个 [Okta 开发者账户](https://developer.okta.com/)。

## 为什么要用 Okta 进行认证？

好吧，我们可能有偏见，但我们认为 Okta 使[身份管理](https://developer.okta.com/product/user-management/)比你习惯的更容易、更安全、更可扩展。Okta 是一个 API 服务，允许您创建、编辑和安全地存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。我们的 API 使您能够:

*   [认证](https://developer.okta.com/product/authentication/)和[授权](https://developer.okta.com/product/authorization/)您的用户
*   存储用户数据
*   执行基于密码的[社交登录](https://developer.okta.com/authentication-guide/social-login/)
*   通过[多重身份验证](https://developer.okta.com/use_cases/mfa/)保护您的应用
*   还有更多！查看我们的[产品文档](https://developer.okta.com/documentation/)了解更多信息

[注册一个永远免费的开发者账户](https://developer.okta.com/signup/)，当你完成后，回来了解更多关于用 Laravel 和 Angular 构建一个安全的 CRUD 应用的信息。

## 角度和角度积垢应用

今天，我们将建立一个简单的琐事游戏界面，允许你为你的朋友运行琐事游戏。这是完成后的应用程序的外观:

[![The finished application](img/dbe4d6135318bba36b83d3d43b7941d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VfvMAw3F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/php-laravel-angular-crud-app/image1-116459b8b78e6ef7858f9b7f6ec24b415b6b1b142570ddf844ac3ccd65089760.png)

以下是作为主机运行游戏的一种可能方式:

*   设置玩家名单
*   向玩家朗读问题
*   玩家可以尝试回答(一个接一个)，在这种情况下，你可以将每个答案标记为正确(+1 分)或错误(-1 分)，或者他们可以跳过问题
*   一旦有人得到正确答案(或者他们全部放弃)，点击“刷新问题”继续下一个问题

当然，你也可以制定自己的规则。

## 创建免费的 Okta 开发者账户

让我们设置我们的 Okta 帐户，以便在我们需要时随时可用。

在您继续之前，您需要登录您的 Okta 帐户(或[免费创建一个新帐户](https://developer.okta.com/signup/))并设置一个新的 OIDC 应用程序。您将主要使用默认设置。请务必记下您的 Okta 域和为应用程序生成的客户端 ID。

以下是逐步说明:

转到“应用程序”菜单项，单击“添加应用程序”按钮:

[![Add Application](img/67092463057a4a8b0ae8ecb0c021399e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gtsoZelx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/php-laravel-angular-crud-app/image2-6c7f7a13ae7fdc6491fef895e8884fbe753933b59dbad9f7ef61d6ae31145ab0.png)

选择“单页应用程序”，然后单击“下一步”。

[![Choose the application type](img/6de09345ca348d68181049fa22f61304.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oDusUWPw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/php-laravel-angular-crud-app/image3-1a23df7a1064c4659963a8354bc65f2ebb4a5ad5cd54efdecab458406c89af13.png)

设置一个描述性的应用名称，添加[http://localhost:3000/log in](http://localhost:3000/login)作为登录重定向 URI，点击完成。您可以保留其余的设置。

## 安装并配置 Laravel 应用程序

我们将遵循官方 Laravel 文档([https://laravel.com/docs/5.7/installation](https://laravel.com/docs/5.7/installation))中的安装说明

首先，让我们通过 composer 在我们的系统上全局安装“laravel”命令。然后我们将创建一个新的 Laravel 项目，导航到它并启动开发 PHP 服务器:

```
composer global require laravel/installer
laravel new trivia-web-service
cd trivia-web-service
php artisan serve 
```

接下来，我们将为我们的应用程序创建一个 MySQL 数据库和用户(如果您愿意，可以自由使用不同的数据库引擎):

```
mysql -uroot -p
CREATE DATABASE trivia CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'trivia'@'localhost' identified by 'trivia';
GRANT ALL on trivia.* to 'trivia'@'localhost';
quit 
```

现在让我们编辑我们的。env 文件并输入我们的数据库凭证:

`.env`

```
DB_DATABASE=trivia
DB_USERNAME=trivia
DB_PASSWORD=trivia 
```

## 用 Laravel 创建后端 API

我们将首先为我们的玩家实体创建一个模型和一个迁移。

```
php artisan make:model Player -m
Model created successfully.
Created Migration: 2018_10_08_094351_create_players_table 
```

-m 选项是–migration 的缩写，它告诉 Artisan 为我们的模型创建一个。

编辑迁移文件并更新 up()方法:

`database/migrations/2018_10_08_094351_create_players_table.php`

```
public function up()
{
    Schema::create('players', function (Blueprint $table) {
        $table->increments('id');
        $table->string('name');
        $table->integer('answers')->default(0);
        $table->integer('points')->default(0);
        $table->timestamps();
    });
} 
```

然后运行迁移来创建表:

```
php artisan migrate 
```

编辑模型文件，并向该类添加一个$fillable 属性，这样我们就可以定义在模型的 create()和 update()操作中可以批量分配哪些字段:

`app/Player.php`

```
class Player extends Model
{
    protected $fillable = ['name', 'answers', 'points'];
} 
```

现在我们将创建两个 API 资源:`Player`(处理单个玩家)和`PlayerCollection`(处理玩家集合)。

```
php artisan make:resource Player
php artisan make:resource PlayerCollection 
```

修改资源数组()的转换函数:

`app/Http/Resources/Player.php`

```
public function toArray($request)
{
    return [
        'id' => $this->id,
        'name' => $this->name,
        'answers' => (int) $this->answers,
        'points' => (int) $this->points,
        'created_at' => $this->created_at,
        'updated_at' => $this->updated_at,
    ];
} 
```

`app/Http/Resources/PlayerCollection.php`

```
public function toArray($request)
{
    return [
        'data' => $this->collection
    ];
} 
```

我们现在可以为 REST API 创建路由和控制器了。

```
php artisan make:controller PlayerController 
```

`routes/api.php`

```
Route::get('/players', 'PlayerController@index');
Route::get('/players/{id}', 'PlayerController@show');
Route::post('/players', 'PlayerController@store');
Route::post('/players/{id}/answers', 'PlayerController@answer');
Route::delete('/players/{id}', 'PlayerController@delete');
Route::delete('/players/{id}/answers', 'PlayerController@resetAnswers'); 
```

`app/Http/Controllers/PlayerController.php`

```
...
use App\Player;
use App\Http\Resources\Player as PlayerResource;
use App\Http\Resources\PlayerCollection;
...

class PlayerController extends Controller
{
    public function index()
    {
        return new PlayerCollection(Player::all());
    }

    public function show($id)
    {
        return new PlayerResource(Player::findOrFail($id));
    }

    public function store(Request $request)
    {
        $request->validate([
            'name' => 'required|max:255',
        ]);

        $player = Player::create($request->all());

        return (new PlayerResource($player))
                ->response()
                ->setStatusCode(201);
    }

    public function answer($id, Request $request)
    {
        $request->merge(['correct' => (bool) json_decode($request->get('correct'))]);
        $request->validate([
            'correct' => 'required|boolean'
        ]);

        $player = Player::findOrFail($id);
        $player->answers++;
        $player->points = ($request->get('correct')
                           ? $player->points + 1
                           : $player->points - 1);
        $player->save();

        return new PlayerResource($player);
    }

    public function delete($id)
    {
        $player = Player::findOrFail($id);
        $player->delete();

        return response()->json(null, 204);
    }

    public function resetAnswers($id)
    {
        $player = Player::findOrFail($id);
        $player->answers = 0;
        $player->points = 0;

        return new PlayerResource($player);
    }
} 
```

API 允许我们获取所有玩家的列表或单个玩家的数据，添加/删除玩家，标记玩家的正确/错误答案，以及重置玩家的答案和分数数据。对请求进行验证，代码生成带有适当状态代码的 JSON 响应——对于这么少的代码来说，这一点也不错。

为了测试 get 方法，向数据库中添加一些虚拟数据(当然，您可以使用带有 Faker 库的 Laravel 工厂来生成数据)，然后访问这些 URL:

*   [http://127 . 0 . 0 . 1:8000/API/players](http://127.0.0.1:8000/api/players)
*   [http://127 . 0 . 0 . 1:8000/API/players/1](http://127.0.0.1:8000/api/players/1)

如果您想要测试 post/put/delete 请求(例如使用 Postman)，请确保为每个请求设置以下标题:

`Accept: "application/json"`

因此验证错误将作为 JSON 返回。

## 安装 Angular 并创建前端应用程序

我们将遵循官方的 Angular 文档([https://angular.io/guide/quickstart](https://angular.io/guide/quickstart))并在全球范围内安装 Angular CLI，然后我们将创建一个新项目并启动服务器。

```
sudo npm install -g @angular/cli@^6.1
ng new trivia-web-client-angular
cd trivia-web-client-angular
ng --version
Angular CLI: 6.1.5
Angular: 6.1.9
ng serve 
```

导航到 [http://127.0.0.1:4200/](http://127.0.0.1:4200/) 现在打开默认的角度应用程序。

接下来，我们将添加布尔玛框架(因为其他人都在使用 Bootstrap，但是我们喜欢混合一些东西，对吧):

```
npm install --save bulma 
```

添加到`.angular.json` :

```
"styles": [
  ...,
  "node_modules/bulma/css/bulma.min.css"
] 
```

再次运行服务器，因为对 angular.json 的更改不会自动生效:

```
ng serve 
```

## 自定义主布局中的棱角

替换默认 HTML 布局:

`src/app/app.component.html`

```
<div style="text-align:center">
    <section class="section">
        <div class="container">
            <nav class="navbar" role="navigation" aria-label="main navigation">
                <div class="navbar-menu is-active buttons">
                    <button class="button is-link">Home</button>
                    <button class="button is-link">Trivia Game</button>
                </div>
            </nav>
        </div>
    </section>
</div> 
```

让我们创建我们的两个主要组件:

```
ng generate component Home
ng generate component TriviaGame 
```

添加路由:

`src/app/app.module.ts`

```
import { Routes, RouterModule } from '@angular/router';

const routes: Routes = [
    { path: '', component: HomeComponent, pathMatch: 'full' },
    { path: 'trivia', component: TriviaGameComponent },
    { path: '**', redirectTo: '', pathMatch: 'full' }
]; 
```

在导入部分:

```
imports: [
    BrowserModule,
    RouterModule.forRoot(routes)
], 
```

添加路由链路和路由器出口(替换`<nav class="navbar">`部分):

`src/app/app.component.html`

```
<nav class="navbar" role="navigation" aria-label="main navigation">
    <div class="navbar-menu is-active buttons">
        <button class="button is-link" [routerLink]="['']">Home</button>
        <button class="button is-link" [routerLink]="['/trivia']">Trivia Game</button>
    </div>
</nav>
<router-outlet></router-outlet> 
```

## 用 Okta 添加认证

我们需要先安装 Okta 角包:

```
npm install @okta/okta-angular rxjs-compat@6 --save 
```

`src/app/app.module.ts`

```
import { OktaAuthModule, OktaCallbackComponent } from '@okta/okta-angular';

const oktaConfig = {
  issuer: '{YourIssuerURL}',
  redirectUri: 'http://localhost:4200/implicit/callback',
  clientId: '{yourClientId}'
}; 
```

不要忘记更换你的网址和客户 ID！

`src/app/app.module.ts`

```
imports: [
    BrowserModule,
    RouterModule.forRoot(routes),
    OktaAuthModule.initAuth(oktaConfig)
], 
```

也更新路线:

```
const routes: Routes = [
    { path: '', component: HomeComponent, pathMatch: 'full' },
    { path: 'trivia', component: TriviaGameComponent },
    { path: 'implicit/callback', component: OktaCallbackComponent },
    { path: '**', redirectTo: '', pathMatch: 'full' },
]; 
```

添加身份验证代码:

`src/app/app.component.ts`

```
import { Component } from '@angular/core';
import { OktaAuthService } from '@okta/okta-angular';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})

export class AppComponent {

    isAuthenticated: boolean;

    constructor(public oktaAuth: OktaAuthService) {
        this.oktaAuth.$authenticationState.subscribe(
            (isAuthenticated: boolean) => (this.isAuthenticated = isAuthenticated)
        );
    }

    async ngOnInit() {
        this.isAuthenticated = await this.oktaAuth.isAuthenticated();
    }

    login() {
        this.oktaAuth.loginRedirect('/');
    }

    logout() {
        this.oktaAuth.logout('/');
    }
} 
```

修改导航栏中的链接:

`src/app/app.component.html`

```
<button class="button is-link" *ngIf="isAuthenticated" [routerLink]="['/trivia']">Trivia Game</button>
<button class="button is-link" *ngIf="!isAuthenticated" (click)="login()"> Login </button>
<button class="button is-link" *ngIf="isAuthenticated" (click)="logout()"> Logout </button> 
```

`src/app/app.module.ts`

```
import { HttpModule } from '@angular/http';
...
imports: [
    BrowserModule,
    HttpModule,
    RouterModule.forRoot(routes),
    OktaAuthModule.initAuth(oktaConfig)
], 
```

## 在 Angular 中创建服务

我们将使用 CLI 工具来创建服务:

```
ng generate service player 
```

`src/app/player.service.ts`

```
import { Injectable } from '@angular/core';
import { OktaAuthService } from '@okta/okta-angular';
import { Http, Headers, RequestOptions } from '@angular/http';
import { Observable } from 'rxjs';

export interface Player {
    id: Number,
    name: String,
    answers: Number,
  points: number
}

const API_URL: string = 'http://localhost:8000';

@Injectable({
  providedIn: 'root'
})
export class PlayerService {

    private accessToken;
    private headers;

    constructor(private oktaAuth: OktaAuthService, private http: Http) {
        this.init();
    }

    async init() {
        this.accessToken = await this.oktaAuth.getAccessToken();
        this.headers = new Headers({
            Authorization: 'Bearer ' + this.accessToken
        });
    }

    getPlayers(): Observable<Player[]> {
        return this.http.get(API_URL + '/players',
            new RequestOptions({ headers: this.headers })
        )
        .map(res => res.json().data);
    }
} 
```

现在我们可以添加代码来获取`Players`数据到`TriviaGameComponent`的`OnInit`生命周期钩子中:

`src/app/trivia-game/trivia-game.component.ts`

```
import { Component, OnInit } from '@angular/core';
import { Player, PlayerService } from '../player.service';
import 'rxjs/Rx';

@Component({
  selector: 'app-trivia-game',
  templateUrl: './trivia-game.component.html',
  styleUrls: ['./trivia-game.component.css']
})
export class TriviaGameComponent implements OnInit {

  players: Player[];
  errorMessage: string;

  constructor(private playerService: PlayerService) { }

    ngOnInit() {
        this.getPlayers();
    }

    getPlayers() {
        this.playerService
            .getPlayers()
            .subscribe(
                players => this.players = players,
                error => this.errorMessage = <any>error
            );
    }
} 
```

我们现在将得到一个 CORS 错误，所以让我们为我们的 API 启用 CORS(切换回我们的后端 API 目录以执行下一个命令):

```
composer require barryvdh/laravel-cors 
```

`app/Http/Kernel.php`

```
protected $middlewareGroups = [
    'web' => [
    ...
    \Barryvdh\Cors\HandleCors::class,
    ],

    'api' => [
        ...
    \Barryvdh\Cors\HandleCors::class,
    ],
]; 
```

## 以角度显示玩家列表

`src/app/trivia-game/trivia-game.component.html`

```
<div>
    <span class="help is-info" *ngIf="isLoading">Loading...</span>
    <span class="help is-error" *ngIf="errorMessage">{{ errorMessage }}</span>
    <table class="table" *ngIf="!isLoading && !errorMessage">
        <thead>
            <tr>
                <th>ID</th>
                <th>Name</th>
                <th>Answers</th>
                <th>Points</th>
                <th>Actions</th>
            </tr>
        </thead>
        <tbody>
            <tr *ngFor="let player of players">
                <td>{{ player.id }}</td>
                <td>{{ player.name }}</td>
                <td>{{ player.answers }}</td>
                <td>{{ player.points }}</td>
                <td>
                    <button class="button is-primary">Delete Player</button>
                </td>
            </tr>
        </tbody>
    </table>
</div> 
```

我们还将修改我们的`TriviaGameComponent`类以包括`isLoading`属性(初始化为`true`并在从服务器检索到玩家列表后设置为`false`)，以及我们的`Player`模型和服务以包括每个玩家的`isUpdating`标志(初始化为 false):

`src/app/trivia-game/trivia-game.component.ts`

```
...
import { OktaAuthService } from '@okta/okta-angular';
import { Player, PlayerService } from '../player.service';
...

export class TriviaGameComponent implements OnInit {

    players: Player[];
    errorMessage: string;
    isLoading: boolean = true;

    constructor(private playerService: PlayerService,
                private oktaAuth: OktaAuthService) { }

    async ngOnInit() {
        await this.oktaAuth.getAccessToken();
        this.getPlayers();
    }

    getPlayers() {
        this.playerService
            .getPlayers()
            .subscribe(
                players => {
                    this.players = players
                    this.isLoading = false
                },
                error => {
                    this.errorMessage = <any>error
                    this.isLoading = false
                }
            );
    } 
```

`src/app/player.service.ts`

```
export interface Player {
  ...
    isUpdating: boolean,
}
...

    getPlayers(): Observable<Player[]> {
        return this.http.get(API_URL + '/api/players',
            new RequestOptions({ headers: this.headers })
        )
        .map(res => {
          let modifiedResult = res.json().data
                modifiedResult = modifiedResult.map(function(player) {
            player.isUpdating = false;
            return player;
          });
          return modifiedResult;
        });
    } 
```

`src/app/trivia-game/trivia-game.component.ts`

```
findPlayer(id): Player {
    return this.players.find(player => player.id === id);
}

isUpdating(id): boolean {
    return this.findPlayer(id).isUpdating;
} 
```

## 用 Okta 保护后端 API

我们需要保护我们的后端 API，以便它验证 Okta 令牌，并且只允许授权的请求。让我们安装验证令牌和创建定制中间件所需的包:

```
composer require okta/jwt-verifier spomky-labs/jose guzzlehttp/psr7
php artisan make:middleware AuthenticateWithOkta 
```

`app/Http/Middleware/AuthenticateWithOkta.php`

```
<?php
namespace App\Http\Middleware;

use Closure;

class AuthenticateWithOkta
{
    /**
     * Handle an incoming request.
     *
     * @param \Illuminate\Http\Request $request
     * @param \Closure $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        if ($this->isAuthorized($request)) {
            return $next($request);
        } else {
            return response('Unauthorized.', 401);
        }
    }

    public function isAuthorized($request)
    {
        if (! $request->header('Authorization')) {
            return false;
        }

        $authType = null;
        $authData = null;

        // Extract the auth type and the data from the Authorization header.
        @list($authType, $authData) = explode(" ", $request->header('Authorization'), 2);

        // If the Authorization Header is not a bearer type, return a 401.
        if ($authType != 'Bearer') {
            return false;
        }

        // Attempt authorization with the provided token
        try {

            // Setup the JWT Verifier
            $jwtVerifier = (new \Okta\JwtVerifier\JwtVerifierBuilder())
                            ->setAdaptor(new \Okta\JwtVerifier\Adaptors\SpomkyLabsJose())
                            ->setAudience('api://default')
                            ->setClientId('{YOUR_CLIENT_ID}')
                            ->setIssuer('{YOUR_ISSUER_URL}')
                            ->build();

            // Verify the JWT from the Authorization Header.
            $jwt = $jwtVerifier->verify($authData);
        } catch (\Exception $e) {

            // We encountered an error, return a 401.
            return false;
        }

        return true;
    }

} 
```

`app/Http/Kernel.php`

```
 protected $middlewareGroups = [
        'web' => [
      ...
        ],

        'api' => [
            ...
      \App\Http\Middleware\AuthenticateWithOkta::class,
        ],
    ]; 
```

不要忘记填写您自己的客户 ID 和发行商 URL！将这些变量提取到`.env`文件中也不会有什么坏处。

## 在角形中创建新的玩家形态

我们将创建一个新组件(`PlayerForm`)并将其显示在玩家列表下方。

```
ng generate component PlayerForm 
```

`src/app/trivia-game/trivia-game.component.html`

```
...
<app-player-form (playerAdded)="appendPlayer($event)"></app-player-form> 
```

`src/app/player.service.ts`

```
...
addPlayer(player): Observable<Player> {
    return this.http.post(API_URL + '/api/players', player,
        new RequestOptions({ headers: this.headers })
    ).map(res => res.json().data);
} 
```

`src/app/player-form/player-form.component.html`

```
<span class="help is-danger">{{ errors }}</span>
<div class="field">
    <div class="control">
        <input class="input" #playerName (keydown)="errors = ''">
    </div>
</div>
<button type="button" class="button is-primary" [class.is-loading]="isLoading" (click)="addPlayer(playerName.value)">Add Player</button> 
```

`src/app/player-form/player-form.component.ts`

```
import { Component, OnInit, EventEmitter, Output } from '@angular/core';
import { Player, PlayerService } from '../player.service';
import 'rxjs/Rx';

@Component({
    selector: 'app-player-form',
    templateUrl: './player-form.component.html',
    styleUrls: ['./player-form.component.css']
})

export class PlayerFormComponent implements OnInit {

    errors: string = '';
    isLoading: boolean = false;

    constructor(private playerService: PlayerService) { }

    @Output()
    playerAdded: EventEmitter<Player> = new EventEmitter<Player>();

    ngOnInit() {
    }

    addPlayer(name) {
        this.isLoading = true;
        this.playerService
            .addPlayer({
                name: name
            })
            .subscribe(
                player => {
                    this.isLoading = false;
                    player.isUpdating = false;
                    this.playerAdded.emit(player);
                },
                error => {
                    this.errors = error.json().errors;
                    this.isLoading = false;
                }
            );
    }
} 
```

`src/app/trivia-game/trivia-game.component.ts`

```
...
appendPlayer(player: Player) {
    this.players.push(player);
} 
```

## 添加删除球员角度功能

我们将在列表中的每个玩家行上添加一个按钮:

`src/app/trivia-game/trivia-game.component.html`

```
...
<button class="button is-primary" [class.is-loading]="isUpdating(player.id)" (click)="deletePlayer(player.id)">Delete Player</button>
... 
```

`src/app/player.service.ts`

```
...
deletePlayer(id): Observable<Player> {
    return this.http.delete(API_URL + '/api/players/' + id,
        new RequestOptions({ headers: this.headers })
    );
}
... 
```

`src/app/trivia-game/trivia-game.component.ts`

```
...
deletePlayer(id) {
    let player = this.findPlayer(id)
    player.isUpdating = true
    this.playerService
        .deletePlayer(id)
        .subscribe(
            response => {
                let index = this.players.findIndex(player => player.id === id)
                this.players.splice(index, 1)
                player.isUpdating = false
            },
            error => {
                this.errorMessage = <any>error
                player.isUpdating = false
            }
        );
}
... 
```

## 在棱角分明中打造琐事服务

我们将创建一个新的琐事服务，并将其链接到琐事问题的公共 API。

```
ng generate service trivia 
```

`src/app/trivia.service.ts`

```
import { Injectable } from '@angular/core';
import { Http } from '@angular/http';

const TRIVIA_ENDPOINT: string = 'http://localhost:8000/question';

@Injectable({
    providedIn: 'root'
})
export class TriviaService {

    constructor(private http: Http) { }

    getQuestion() {
        return this.http.get(TRIVIA_ENDPOINT)
        .map(res => res.json()[0]);
    }
} 
```

修改琐事游戏组件 html，使其包含一张带有问题和答案的卡片，以及为每个玩家指示正确和错误答案的按钮:

`src/app/trivia-game/trivia-game.component.html`

```
<div class="columns">
    <div class="column">
    <span class="help is-info" *ngIf="isLoading">Loading...</span>
    <span class="help is-error" *ngIf="errorMessage">{{ errorMessage }}</span>
    <table class="table" *ngIf="!isLoading && !errorMessage">
        <thead>
            <tr>
                <th>ID</th>
                <th>Name</th>
                <th>Answers</th>
                <th>Points</th>
                <th>Actions</th>
            </tr>
        </thead>
        <tbody>
            <tr *ngFor="let player of players">
                <td>{{ player.id }}</td>
                <td>{{ player.name }}</td>
                <td>{{ player.answers }}</td>
                <td>{{ player.points }}</td>
                <td>
                    <button class="button is-primary" [class.is-loading]="isUpdating(player.id)" (click)="rightAnswer(player.id)">Right (+1)</button> 
                    <button class="button is-primary" [class.is-loading]="isUpdating(player.id)" (click)="wrongAnswer(player.id)">Wrong (-1)</button> 
                    <button class="button is-primary" [class.is-loading]="isUpdating(player.id)" (click)="deletePlayer(player.id)">Delete Player</button>
                </td>
            </tr>
        </tbody>
    </table>
    <app-player-form (playerAdded)="appendPlayer($event)"></app-player-form>
    </div>
    <div class="column">
        <div class="card" *ngIf="question?.question">
          <div class="card-content">
            <button class="button is-primary" (click)="getQuestion()">Refresh Question</button>
            <p class="title">
              {{ question.question }}
            </p>
            <p class="subtitle">
              {{ question.category.title }}
            </p>
          </div>
          <footer class="card-footer">
            <p class="card-footer-item">
                <span>
                    Correct answer: {{ question.answer }}
                </span>
            </p>
          </footer>
        </div>
    </div>
</div> 
```

`src/app/trivia-game/trivia-game.component.ts`

```
...
import { TriviaService } from '../trivia.service';
...
export class TriviaGameComponent implements OnInit {
    ...
    question: any;
    ...
    constructor(private playerService: PlayerService,
            private triviaService: TriviaService,
            private oktaAuth: OktaAuthService) { }

    async ngOnInit() {
        await this.oktaAuth.getAccessToken();
        this.getPlayers();
        this.getQuestion();
    }
    ...
    getQuestion() {
        this.triviaService
            .getQuestion()
            .subscribe(
                question => this.question = question,
                error => this.errorMessage = <any>error
            );
    }
... 
```

我们将在 API 中添加一个快速的未认证端点来返回一个随机的小问题。在现实中，你可能想连接一个真正的琐事数据库到你的应用程序，但这将做这个快速演示。

在服务器项目中，添加以下路由:

`routes/web.php`

```
Route::get('/question', function(Request $request) {
    return response()->json(json_decode(file_get_contents('http://jservice.io/api/random?count=1')));
}); 
```

## 添加按钮标记答案对错

`src/app/player.service.ts`

```
...
    answer(id, data): Observable<Player> {
        return this.http.post(API_URL + '/api/players/' + id + '/answers', data,
            new RequestOptions({ headers: this.headers })
        ).map(res => res.json().data);
    }
... 
```

`src/app/trivia-game/trivia-game.component.ts`

```
...
    rightAnswer(id) {
        let data = {
            correct: true
        }
        this.answer(id, data)
    }

    wrongAnswer(id) {
        let data = {
            correct: false
        }
        this.answer(id, data)
    }

    answer(id, data) {
        let player = this.findPlayer(id)
        player.isUpdating = true
        this.playerService
            .answer(id, data)
            .subscribe(
                response => {
                    player.answers = response.answers
                    player.points = response.points
                    player.isUpdating = false
                },
                error => {
                    this.errorMessage = <any>error
                    player.isUpdating = false
                }
            );
    }
... 
```

就是这样！你准备好玩一个小游戏了。

希望本文对您有所帮助，并且您现在意识到向 Laravel APIs 和 Angular 应用程序添加身份验证是多么容易。

你可以在 https://github.com/oktadeveloper/okta-php-trivia-angular 找到完整应用程序的源代码。

如果你想了解更多关于 Okta、Angular 或 Laravel 的信息，请查看 Okta Dev 博客。

以下是一些值得一读的好文章:

*   [Angular 6:有什么新功能，为什么要升级？](https://dev.to/blog/2018/05/09/upgrade-to-angular-6)
*   [用 Symfony 和 Angular 构建你的第一个 CRUD 应用](https://dev.to/blog/2018/08/14/php-crud-app-symfony-angular)

一如既往，我们希望收到您的来信。欢迎在评论中或 Twitter 上提出问题或反馈给我们！