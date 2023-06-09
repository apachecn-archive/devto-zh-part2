# 具有 Go 后端的 Angular 应用程序中的 JWT 认证

> 原文：<https://dev.to/nikola/jwt-authentication-in-an-angular-application-with-a-go-backend--13cg>

[![](img/e7cc6508cbdad581190ec1fdc0929602.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--O5Px3b5J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mk6if274wu7uk9otmyu5.jpg)

*最初发表于[我的博客](http://www.nikola-breznjak.com/blog/go/jwt-authentication-angular-application-go-backend/)。*

## TL；速度三角形定位法(dead reckoning)

在本教程中，我将向您展示如何构建一个简单的 web 应用程序，使用 JWT 来处理身份验证。前端会用 Angular 5 写，后端会用 Go。我还会介绍一些理论概念。

你可以在 Github 上查看最终的源代码

## JWT

JWT 代表`JSON Web Token`，它是一个编码字符串，例如，看起来像这样:

`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2lkIjoiYTFiMmMzIiwidXNlcm5hbWUiOiJuaWtvbGEifQ==.mKIuU0V0Bo99JU5XbeMe6g-Hrd3ZxJRlmdHFrEkz0Wk`

如果你用`.`分割这个字符串，你会得到三个独立的字符串:

*   **报头** -包含关于令牌的编码信息
*   **有效载荷** -包含在双方之间传输的编码数据
*   **验证签名** -用于验证数据未被更改

官网是这样说 JWTs 的:

> JSON Web 令牌是一种开放的行业标准 RFC 7519 方法，用于安全地表示双方之间的声明。

如果你喜欢

[![](img/42afe7892d970e9c979700041a1af96e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kalX7sVS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/hzZHo9S.jpg)

那我不怪你。让我们更详细地定义一下。

JSON Web 令牌是双方安全交流信息的一种方式。一个`claim`是和令牌一起发送的一些数据，像`user_id`。

**安全通信**在这里指的是我们可以确定信息**没有被篡改**的事实，但这并不意味着它对潜在的攻击者是隐藏的。实际上，一个潜在的攻击者可以读取 JWT 的内容(所以请**不要像声称的那样发送任何密码**)，但是他不能修改它并以那种形式发回。

基于 JWT 不可篡改的前提下，对于**认证**非常有用。我们可以给用户一个包含他们的`userid`的 JWT，它可以存储在本地，用于验证请求是否来自经过身份验证的用户。

jwt 很短，所以您可以很容易地将它们作为 POST 参数、HTTP 头发送，或者作为查询字符串添加到 URL。您可以将它们存储在本地存储中，然后将它们与每个请求一起发送到服务器，确保用户得到授权。

似乎很多人喜欢并使用它们。然而，我必须指出，很多安全研究人员[不赞成这种做法](https://www.rdegges.com/2018/please-stop-using-local-storage/)。

## 边做边学

没有必要知道 jwt 如何工作的复杂细节，能够使用它们，但是当你走得更远时，它有时会给你这种了不起的感觉。

因此，本着这种精神，我们现在要创建我们自己的 JSON Web 令牌💪

### 有效载荷

例如，假设我想将以下数据安全地发送给某人:

```
{
    "user_id": "a1b2c3",
    "username": "nikola"
} 
```

Enter fullscreen mode Exit fullscreen mode

这是我的`payload`。为了将它添加到我们的 JWT 中，我们首先需要对它进行`base64`编码。通过使用`btoa`功能:
，您可以在浏览器的开发者工具(控制台窗口)中使用 JavaScript 轻松实现这一点

```
btoa(JSON.stringify({
    "user_id": "a1b2c3",
    "username": "nikola"
})); 
```

Enter fullscreen mode Exit fullscreen mode

或者，用曾经稍微流行的围棋，你会这样做:

```
package main

import (
    "encoding/base64"
    "fmt"
)

func main() {
    data := `{"user_id":"a1b2c3","username":"nikola"}`
    uEnc := base64.URLEncoding.EncodeToString([]byte(data))
    fmt.Println(uEnc)
} 
```

Enter fullscreen mode Exit fullscreen mode

从而得到:
`eyJ1c2VyX2lkIjoiYTFiMmMzIiwidXNlcm5hbWUiOiJuaWtvbGEifQ==`

> ⚠️在 JavaScript 示例中，我们必须首先使用`JSON.stringify`函数，否则得到的解码字符串将只是`[object Object]`。

我们可以通过使用 JavaScript:
中的`atob`函数来解码 base64 编码的字符串

```
atob('eyJ1c2VyX2lkIjoiYTFiMmMzIiwidXNlcm5hbWUiOiJuaWtvbGEifQ==') 
```

Enter fullscreen mode Exit fullscreen mode

还是在围棋:

```
uDec, _ := base64.URLEncoding.DecodeString("eyJ1c2VyX2lkIjoiYTFiMmMzIiwidXNlcm5hbWUiOiJuaWtvbGEifQ==") 
```

Enter fullscreen mode Exit fullscreen mode

在这两种情况下，结果都是`{"user_id":"a1b2c3","username":"nikola"}`

### 表头

接下来，我们需要对报头进行编码:

```
{
    "alg": "HS256",
    "typ": "JWT"
} 
```

Enter fullscreen mode Exit fullscreen mode

在 JWT，报头实际上在有效载荷之前。在头中，我们指定我们创建了一个 JWT，并且我们使用了某种散列算法`HS256`。这个特殊的算法将允许我们使用一个秘密密码。您可以使用`RSA SHA256`算法来使用私有的&公钥对。[这里有一个关于 jwt 中使用的不同散列算法的很好的教程。](https://blog.angular-university.io/angular-jwt/)

base64 编码的头看起来像这样:`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9`。

### 验证签名

作为最后一步，我们需要创建验证签名。我们通过连接编码的头和有效载荷字符串，用`.`将它们分开来实现这一点。之后，我们需要应用`HS256`算法以及只有发送者知道的秘密密码。

我们的编码头和有效载荷如下所示:

`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2lkIjoiYTFiMmMzIiwidXNlcm5hbWUiOiJuaWtvbGEifQ==`

我们将使用`42isTheAnswer`作为密码。

我们在浏览器中没有这个散列函数，所以我们需要使用 Node 来完成它。首先，通过运行:`npm install base64url`安装`base64url`。如果你是 Node 新手，我推荐[这个教程](https://hackhands.com/how-to-get-started-on-the-mean-stack/)。

用以下内容创建一个新的 JavaScript 文件:

```
var base64url = require('base64url');

var crypto    = require('crypto');
var message     = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2lkIjoiYTFiMmMzIiwidXNlcm5hbWUiOiJuaWtvbGEifQ==';
var key       = '42isTheAnswer';
var algorithm = 'sha256';
var hash, hmac;

hmac = crypto.createHmac(algorithm, key);
hmac.setEncoding('base64');
hmac.write(message);
hmac.end();
hash = hmac.read();

var final = base64url.fromBase64(hash);
console.log(final); 
```

Enter fullscreen mode Exit fullscreen mode

或者，在 Go 中，您可以使用:

```
package main

import (
    "crypto/hmac"
    "crypto/sha256"
    "encoding/base64"
    "fmt"
)

func main() {
    message := "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2lkIjoiYTFiMmMzIiwidXNlcm5hbWUiOiJuaWtvbGEifQ=="
    sKey := "42isTheAnswer"

    key := []byte(sKey)
    h := hmac.New(sha256.New, key)
    h.Write([]byte(message))
    b := base64.URLEncoding.EncodeToString(h.Sum(nil))
    fmt.Println(string(b))
} 
```

Enter fullscreen mode Exit fullscreen mode

在您执行上面的任何脚本之后，您应该得到这个字符串:

`mKIuU0V0Bo99JU5XbeMe6g-Hrd3ZxJRlmdHFrEkz0Wk`

现在我们将这个字符串添加到之前的令牌中(也用`.`分隔)，我们得到:

`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2lkIjoiYTFiMmMzIiwidXNlcm5hbWUiOiJuaWtvbGEifQ==.mKIuU0V0Bo99JU5XbeMe6g-Hrd3ZxJRlmdHFrEkz0Wk`

我们可以在 jwt.io 上测试这个 JWT:

[![](img/f3482840e33a4a707960a57da0755bc2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SeZdLWuT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/OOsBnYy.png)

## 安全

我们的`payload`和`header`是 base64 编码的，也可以很容易地被 base64 解码。那么，这到底有多安全呢？

重要的一点是，它**不能被更改**，因为验证签名是使用报头和有效载荷数据构建的，如果其中任何一个发生更改，我们将无法验证签名——因此，如果有人篡改 JWT，我们会知道。

由于有效载荷数据已经改变，验证签名将不再匹配，并且没有办法伪造签名，除非您知道用于散列它的秘密。当这个 JWT 攻击服务器时，它将知道它已经被篡改。

## 总述

如果 jwt 用于认证，它们将至少包含一个用户 ID 和一个`expiration timestamp`。
这种类型的令牌被称为`Bearer Token`。它标识拥有它的用户并定义用户会话。

一个`Bearer Token`是用户名/密码组合的签名临时替代。实现基于 JWT 的身份验证的第一步是颁发一个承载令牌，并通过登录过程将其交给用户。

jwt 的关键属性是，要确认它们是否有效，我们只需要查看令牌本身。

## 演示应用程序

我们将构建一个简单的全栈应用，它将具有:

*   登录页面
*   登录页面
*   成员页面
*   用于验证的后端

JWTs 的认证是如何工作的:

[![](img/e038062df8a1899a5a05b7ef5969a750.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IX-y468A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/hthWzSx.png)

*   用户通过登录页面向服务器提交用户名和密码
*   服务器验证发送的数据，并创建一个 JWT 令牌，其有效负载包含用户的 id 和过期时间戳
*   服务器用一个秘密密码对报头和有效载荷进行签名，并将其发送回用户的浏览器
*   浏览器获取签名的 JWT，并开始将它与每个 HTTP 请求一起发送回服务器
*   签名的 JWT 充当临时用户凭据，替代永久凭据(用户名和密码)

下面是服务器在收到 JWT 令牌时所做的事情:

*   服务器检查 JWT 签名并确认它是有效的
*   有效载荷通过用户 id 识别特定用户
*   只有服务器有秘密密码，并且服务器只向提交正确密码的用户分发令牌。因此，服务器可以确定该令牌确实是由服务器给予该特定用户的
*   服务器使用该用户的凭证继续处理 HTTP 请求

### 棱角分明的 CLI 与布尔玛

Angular CLI 对于 [Angular](https://angular.io/) 来说是一个很棒的工具，而[布尔玛](https://bulma.io/)是一个简单的 CSS 框架，使用起来纯粹是一种享受。

让我们从用 Angular CLI 生成一个新项目开始(安装它，以防您还没有它):

`ng new jwt-auth`

这个过程完成后，运行`jwt-auth`文件夹内的`ng serve`，你就会有一个 app 运行在 [http://localhost:4200/](http://localhost:4200/) :

[![](img/63ea27cffedba7eab2e8d8a4d4049cb2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M9ZFWKoM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/WtR7ukv.png)

#### 添加布尔玛

将此添加到`index.html`文件:

```
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bulma/0.6.2/css/bulma.min.css">
  <script defer src="https://use.fontawesome.com/releases/v5.0.0/js/all.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

将`app.component.html`更新为:

```
<nav class="navbar">
  <div class="container">
    <div class="navbar-brand">
      <a class="navbar-item">
        JWT Angular Login
      </a>
    </div>

    <div id="navbarMenuHeroA" class="navbar-menu">
      <div class="navbar-end">
        <a class="navbar-item">
          Home
        </a>
        <a class="navbar-item">
          Login
        </a>
        <a class="navbar-item">
          Members
        </a>
        <a class="navbar-item">
          Logout
        </a>
      </div>
    </div>
  </div>
</nav>

<router-outlet></router-outlet>

<footer class="footer">
  <div class="container has-text-centered">
    <div class="content">
      From Croatia with ❤️
    </div>
  </div>
</footer> 
```

Enter fullscreen mode Exit fullscreen mode

如果你现在看一下这个页面，你会看到:

[![](img/47017197c1073c9a253be36d10596047.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D3LCWnz2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/jiXVEYT.png)

因此，我们有一个带有链接的页眉和一个带有简单文本的页脚。

元素将用于服务其他页面。

现在，让我们使用 Angular CLI 创建三个新组件:

```
ng g component home
ng g component login
ng g component members 
```

Enter fullscreen mode Exit fullscreen mode

Angular CLI 有用的一个原因是，通过生成组件，它为我们创建了 3 个文件，并在`app.module.ts`文件中导入组件:

```
create src/app/members/members.component.css (0 bytes)
create src/app/members/members.component.html (26 bytes)
create src/app/members/members.component.spec.ts (635 bytes)
create src/app/members/members.component.ts (273 bytes)
update src/app/app.module.ts (1124 bytes) 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们连接`app.module.ts` :
中的路线

```
const routes = [
    { path: 'login', component: LoginComponent },
    { path: 'members', component: MembersComponent },
    { path: '', component: HomeComponent },
    { path: '**', redirectTo: '' }
];

...
imports: [
    BrowserModule,
    RouterModule.forRoot(routes)
], 
```

Enter fullscreen mode Exit fullscreen mode

像这样使用`routerLink`设置`app.component.html`中的链接:

```
<a class="navbar-item" [routerLink]="['']">Home</a>
<a class="navbar-item" [routerLink]="['/login']">Login</a>
<a class="navbar-item" [routerLink]="['/members']">Members</a> 
```

Enter fullscreen mode Exit fullscreen mode

如果一切正常，您应该会在浏览器中看到以下内容:

[![](img/65da593172b3150f009b831a5e2fad6e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Jbd8xiad--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/rkIbHca.gif)

#### 登录

将`login.component.html`的内容替换为:

```
<section class="hero">
  <div class="hero-body has-text-centered">
    <form [formGroup]="form">
      <div class="columns">
        <div class="column"></div>

        <div class="column is-3">
          <div class="field">
            <label class="label is-pulled-left">Email</label>
            <div class="control">
              <input class="input" type="text" placeholder="john@gmail.com" formControlName="email" name="email">
            </div>
          </div>
        </div>

        <div class="column"></div>
      </div>

      <div class="columns">
        <div class="column"></div>

        <div class="column is-3">
          <div class="field">
            <label class="label is-pulled-left">Password:</label>
            <div class="control">
              <input class="input" type="password" formControlName="password" name="password">
            </div>

            <br>
            <br>
            <a class="button is-primary is-medium is-fullwidth" (click)='login()'>Login</a>
          </div>
        </div>

        <div class="column"></div>
      </div>
    </form>
  </div>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

并将`login.component.ts`加上:

```
import { Component, OnInit } from '@angular/core';
import { FormGroup, FormBuilder, Validators, FormsModule, ReactiveFormsModule } from '@angular/forms';

@Component({
    selector: 'app-login',
    templateUrl: './login.component.html',
    styleUrls: ['./login.component.css']
})
export class LoginComponent implements OnInit {
    form: FormGroup;

    constructor(private fb: FormBuilder) {
        this.form = this.fb.group({
            email: ['', Validators.required],
            password: ['', Validators.required]
        });
    }

    ngOnInit() {
    }

    login() {
        console.log('Clicked the Login button');
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可能注意到我们使用了一堆来自`@angular/forms`的导入，所以我们也需要将它添加到`imports`数组中的`app.module.ts`中:

```
...
imports: [
    BrowserModule,
    FormsModule,
    ReactiveFormsModule,
    RouterModule.forRoot(routes)
],
... 
```

Enter fullscreen mode Exit fullscreen mode

在我们进入实际的身份验证部分之前，让我们稍微修改一下 Home 和 Members 区域。

#### 首页和成员

将 HTML 文件更新为以下内容:

`home.component.html` :

```
<section class="hero" id="hero">
  <div class="hero-head"></div>
  <div class="hero-body">
    <div class="container has-text-centered">
      <h1 class="is-1 title">
        Welcome to JWT Angular Auth!
      </h1>
    </div>
  </div>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

`members.component.html` :

```
<section class="hero" id="hero">
  <div class="hero-head"></div>
  <div class="hero-body">
    <div class="container has-text-centered">
      <h1 class="is-1 title">
        Members area
      </h1>
    </div>
  </div>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

### 去吧

我们的后端是用 Golang 写的，看起来是这样的:

```
package main

import (
    "encoding/json"
    "errors"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "strings"
    "time"

    jwt "github.com/dgrijalva/jwt-go"
    "github.com/rs/cors"
)

const (
    PORT   = "1337"
    SECRET = "42isTheAnswer"
)

type JWTData struct {
    // Standard claims are the standard jwt claims from the IETF standard
    // https://tools.ietf.org/html/rfc7519
    jwt.StandardClaims
    CustomClaims map[string]string `json:"custom,omitempty"`
}

type Account struct {
    Email    string  `json:"email"`
    Balance  float64 `json:"balance"`
    Currency string  `json:"currency"`
}

func main() {
    mux := http.NewServeMux()
    mux.HandleFunc("/", hello)
    mux.HandleFunc("/login", login)
    mux.HandleFunc("/account", account)

    handler := cors.Default().Handler(mux)

    log.Println("Listening for connections on port: ", PORT)
    log.Fatal(http.ListenAndServe(":"+PORT, handler))
}

func hello(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello from Go!")
}

func login(w http.ResponseWriter, r *http.Request) {
    body, err := ioutil.ReadAll(r.Body)
    if err != nil {
        log.Println(err)
        http.Error(w, "Login failed!", http.StatusUnauthorized)
    }

    var userData map[string]string
    json.Unmarshal(body, &userData)

    // Demo - in real case scenario you'd check this against your database
    if userData["email"] == "admin@gmail.com" && userData["password"] == "admin123" {
        claims := JWTData{
            StandardClaims: jwt.StandardClaims{
                ExpiresAt: time.Now().Add(time.Hour).Unix(),
            },

            CustomClaims: map[string]string{
                "userid": "u1",
            },
        }

        token := jwt.NewWithClaims(jwt.SigningMethodHS256, claims)
        tokenString, err := token.SignedString([]byte(SECRET))
        if err != nil {
            log.Println(err)
            http.Error(w, "Login failed!", http.StatusUnauthorized)
        }

        json, err := json.Marshal(struct {
            Token string `json:"token"`
        }{
            tokenString,
        })

        if err != nil {
            log.Println(err)
            http.Error(w, "Login failed!", http.StatusUnauthorized)
        }

        w.Write(json)
    } else {
        http.Error(w, "Login failed!", http.StatusUnauthorized)
    }
}

func account(w http.ResponseWriter, r *http.Request) {
    authToken := r.Header.Get("Authorization")
    authArr := strings.Split(authToken, " ")

    if len(authArr) != 2 {
        log.Println("Authentication header is invalid: " + authToken)
        http.Error(w, "Request failed!", http.StatusUnauthorized)
    }

    jwtToken := authArr[1]

    claims, err := jwt.ParseWithClaims(jwtToken, &JWTData{}, func(token *jwt.Token) (interface{}, error) {
        if jwt.SigningMethodHS256 != token.Method {
            return nil, errors.New("Invalid signing algorithm")
        }
        return []byte(SECRET), nil
    })

    if err != nil {
        log.Println(err)
        http.Error(w, "Request failed!", http.StatusUnauthorized)
    }

    data := claims.Claims.(*JWTData)

    userID := data.CustomClaims["userid"]

    // fetch some data based on the userID and then send that data back to the user in JSON format
    jsonData, err := getAccountData(userID)
    if err != nil {
        log.Println(err)
        http.Error(w, "Request failed!", http.StatusUnauthorized)
    }

    w.Write(jsonData)
}

func getAccountData(userID string) ([]byte, error) {
    output := Account{"nikola.breznjak@gmail.com", 3.14, "BTC"}
    json, err := json.Marshal(output)
    if err != nil {
        return nil, err
    }

    return json, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

> ⚠️:我还不是围棋专家，所以使用这门语言时间长的人会写得更地道。但是，当你自己面对这种想法时，记住这一点:“完美是好的敌人”，通过“在外面”做东西和获得反馈来学习比“等 x 个月直到你掌握一门语言”好得多。

因此，我将尽最大努力从上到下解释这段代码的作用:

#### 包

```
package main 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们有`package`语句。每个 Go 程序都必须有一个`main`包。

#### 进口

```
import (
    "encoding/json"
    "errors"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "strings"
    "time"

    jwt "github.com/dgrijalva/jwt-go"
    "github.com/rs/cors"
) 
```

Enter fullscreen mode Exit fullscreen mode

然后我们有进口货。除了 jwt 和 cors 之外，所有的导入都来自标准 Go 库。如果您使用的是 VS Code 之类的编辑器，或者 GoLand 之类的 IDE，那么这些导入会在您保存代码时自动添加。

我喜欢 Go 的一点是它的自动代码格式，所以最终，这种语言不再需要争论括号是在同一行还是在下一行。一致性 FTW！

#### 常数

```
const (
    PORT   = "1337"
    SECRET = "42isTheAnswer"
) 
```

Enter fullscreen mode Exit fullscreen mode

那么我们有两个常数:`PORT`和`SECRET`。让所有的[大写字母代表常量](https://stackoverflow.com/questions/22688906/go-naming-conventions-for-const)不是而是**围棋中的惯例，但我似乎在盲目地坚持这个习惯。**

#### 结构

```
type JWTData struct {
    // Standard claims are the standard jwt claims from the IETF standard
    // https://tools.ietf.org/html/rfc7519
    jwt.StandardClaims
    CustomClaims map[string]string `json:"custom,omitempty"`
}

type Account struct {
    Email    string  `json:"email"`
    Balance  float64 `json:"balance"`
    Currency string  `json:"currency"`
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们有两个结构:`JWTData`和`Account`。`JWTData`结构和一些标准字段(声明)有一个额外的`CustomClaims`映射，可以保存`string`类型的键值对。我们将使用这个数据类型来添加我们自己的定制声明(`userid`)。

`Account`结构被用作一个示例结构，用于在用户登录并进入成员页面后对其做出响应。它包含`Email`、`Balance`和`Currency`字段。

#### 主

```
func main() {
    mux := http.NewServeMux()
    mux.HandleFunc("/", hello)
    mux.HandleFunc("/login", login)
    mux.HandleFunc("/account", account)

    handler := cors.Default().Handler(mux)

    log.Println("Listening for connections on port: ", PORT)
    log.Fatal(http.ListenAndServe(":"+PORT, handler))
} 
```

Enter fullscreen mode Exit fullscreen mode

在`main`函数中，我们为 API“注册”了处理程序。如果我们假设这个 Go 程序将在一个域`http://api.boringcompany.com`上运行，那么对这个 URL 的请求将由下面显示的`hello`函数处理。如果请求被发送到`http://api.boringcompany.com/login` URL，它将被我们将在下面展示的`login`函数处理，等等。最后，我们通过`log`打印消息，并用`http.ListenAndServe`函数启动服务器。

只有在本地开发时，才需要 CORS 处理程序。如果你会这样做，那么我也推荐你正在使用的浏览器的 CORS 插件([这里是我用于 Chrome 的一个](https://chrome.google.com/webstore/detail/allow-control-allow-origi/nlfbmbojpeacfghkpbjhddihlkkiljbi?hl=en))。

#### 您好处理程序

```
func hello(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello from Go!")
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个简单的函数，当用户点击我们的主 API URL 时，它会将`Hello from Go!`输出给用户。

#### 登录处理程序

```
func login(w http.ResponseWriter, r *http.Request) {
    body, err := ioutil.ReadAll(r.Body)
    if err != nil {
        log.Println(err)
        http.Error(w, "Login failed!", http.StatusUnauthorized)
    }

    var userData map[string]string
    json.Unmarshal(body, &userData)

    // Demo - in real case scenario you'd check this against your database
    if userData["email"] == "admin@gmail.com" && userData["password"] == "admin123" {
        claims := JWTData{
            StandardClaims: jwt.StandardClaims{
                ExpiresAt: time.Now().Add(time.Hour).Unix(),
            },

            CustomClaims: map[string]string{
                "userid": "u1",
            },
        }

        token := jwt.NewWithClaims(jwt.SigningMethodHS256, claims)
        tokenString, err := token.SignedString([]byte(SECRET))
        if err != nil {
            log.Println(err)
            http.Error(w, "Login failed!", http.StatusUnauthorized)
        }

        json, err := json.Marshal(struct {
            Token string `json:"token"`
        }{
            tokenString,
        })

        if err != nil {
            log.Println(err)
            http.Error(w, "Login failed!", http.StatusUnauthorized)
        }

        w.Write(json)
    } else {
        http.Error(w, "Login failed!", http.StatusUnauthorized)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在`login`函数中，我们首先读取请求的主体，解析出`email`和`password`参数。然后我们检查这个电子邮件/密码组合，确保它是正确的。当然，出于演示的目的，代码中是这样做的；在真实情况下，您很可能会根据您的数据库检查这一点。

如果用户凭证是正确的，我们准备声明，其中我们使用标准的`ExpiresAt`声明，并且我们还添加我们自己的自定义声明`userid`，值为`u1`。

接下来，我们使用`jwt.NewWithClaims`函数和`HS256`哈希算法对报头和有效载荷进行签名，并使用`SECRET`作为密钥。最后，我们将这个令牌以 JSON 格式返回给用户。

否则，如果发生任何错误，我们将发送回带有失败消息的未授权状态。

#### 账户处理人

```
func account(w http.ResponseWriter, r *http.Request) {
    authToken := r.Header.Get("Authorization")
    authArr := strings.Split(authToken, " ")

    if len(authArr) != 2 {
        log.Println("Authentication header is invalid: " + authToken)
        http.Error(w, "Request failed!", http.StatusUnauthorized)
    }

    jwtToken := authArr[1]

    claims, err := jwt.ParseWithClaims(jwtToken, &JWTData{}, func(token *jwt.Token) (interface{}, error) {
        if jwt.SigningMethodHS256 != token.Method {
            return nil, errors.New("Invalid signing algorithm")
        }
        return []byte(SECRET), nil
    })

    if err != nil {
        log.Println(err)
        http.Error(w, "Request failed!", http.StatusUnauthorized)
    }

    data := claims.Claims.(*JWTData)

    userID := data.CustomClaims["userid"]

    // fetch some data based on the userID and then send that data back to the user in JSON format
    jsonData, err := getAccountData(userID)
    if err != nil {
        log.Println(err)
        http.Error(w, "Request failed!", http.StatusUnauthorized)
    }

    w.Write(jsonData)
}

func getAccountData(userID string) ([]byte, error) {
    output := Account{"nikola.breznjak@gmail.com", 3.14, "BTC"}
    json, err := json.Marshal(output)
    if err != nil {
        return nil, err
    }

    return json, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

在`account`函数中，我们首先读取`Authorization`头并取出令牌。然后，我们确保令牌是有效的并且没有被篡改，我们通过使用`jwt.ParseWithClaims`函数解析出声明。

使用`userID`声明，我们获取一些数据(使用`getAccountData`函数),然后以 JSON 格式将数据发送回用户。

#### 运行 Go 应用

您可以使用`go run main.go`在您的电脑上本地运行此应用程序。当然，你需要安装 Go。你可以在本教程中查看如何做到这一点。

### 完成有角的前端

现在让我们切换回我们的 Angular 项目，并向我们的 API 发出实际请求。

#### 认证服务

使用 Angular CLI，在您的终端中执行以下命令:

```
ng g service auth 
```

Enter fullscreen mode Exit fullscreen mode

这现在为我们创建了两个文件:

```
create src/app/auth.service.spec.ts (362 bytes)
create src/app/auth.service.ts (110 bytes) 
```

Enter fullscreen mode Exit fullscreen mode

将下面的代码复制到`auth.service.ts`文件中:

```
import { Injectable } from '@angular/core';
import { RequestOptions, Response } from '@angular/http';

import { HttpClient, HttpHeaders } from '@angular/common/http';
import { Router } from '@angular/router';

@Injectable()
export class AuthService {

    API_URL = 'http://localhost:1337';
    TOKEN_KEY = 'token';

    constructor(private http: HttpClient, private router: Router) { }

    get token() {
        return localStorage.getItem(this.TOKEN_KEY);
    }

    get isAuthenticated() {
        return !!localStorage.getItem(this.TOKEN_KEY);
    }

    logout() {
        localStorage.removeItem(this.TOKEN_KEY);
        this.router.navigateByUrl('/');
    }

    login(email: string, pass: string) {
        const headers = {
            headers: new HttpHeaders({ 'Content-Type': 'application/json', 'Cache-Control': 'no-cache' })
        };

        const data = {
            email: email,
            password: pass
        };

        this.http.post(this.API_URL + '/login', data, headers).subscribe(
            (res: any) => {
                localStorage.setItem(this.TOKEN_KEY, res.token);

                this.router.navigateByUrl('/members');
            }
        );
    }

    getAccount() {
        return this.http.get(this.API_URL + '/account');
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`AuthService`由这些功能组成:

*   `login` -我们将用户输入的电子邮件/密码发送到服务器，成功后，我们将令牌存储在本地存储中。*请注意我在本教程理论部分给出的警告。*
*   `logout` -我们从本地存储中删除令牌，并将用户重定向到登录页面
*   `token` -从本地存储器返回令牌
*   `isAuthenticated` -如果令牌存在于本地存储器中，则返回真/假
*   `getAccount` -请求用户数据并返回承诺

#### 登录组件

```
import { Component, OnInit } from '@angular/core';
import { FormGroup, FormBuilder, Validators, FormsModule, ReactiveFormsModule } from '@angular/forms';
import { AuthService } from '../auth.service';

@Component({
    selector: 'app-login',
    templateUrl: './login.component.html',
    styleUrls: ['./login.component.css']
})
export class LoginComponent implements OnInit {
    form: FormGroup;

    constructor(private fb: FormBuilder, private authService: AuthService) {
        this.form = this.fb.group({
            email: ['', Validators.required],
            password: ['', Validators.required]
        });
    }

    ngOnInit() {
    }

    login() {
        const val = this.form.value;

        if (val.email && val.password) {
            this.authService.login(val.email, val.password);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

最重要的部分是登录函数，它调用`AuthService`登录函数并传递给它`email`和`password`。我们使用 Angular 中的`FormBuilder`来访问 HTML 代码中的表单字段，如下所示:

```
<section class="hero">
  <div class="hero-body has-text-centered">
    <form [formGroup]="form">
      <div class="columns">
        <div class="column"></div>

        <div class="column is-3">
          <div class="field">
            <label class="label is-pulled-left">Email</label>
            <div class="control">
              <input class="input" type="text" placeholder="john@gmail.com" formControlName="email" name="email">
            </div>
          </div>
        </div>

        <div class="column"></div>
      </div>

      <div class="columns">
        <div class="column"></div>

        <div class="column is-3">
          <div class="field">
            <label class="label is-pulled-left">Password:</label>
            <div class="control">
              <input class="input" type="password" formControlName="password" name="password">
            </div>

            <br>
            <br>
            <a class="button is-primary is-medium is-fullwidth" (click)='login()'>Login</a>
          </div>
        </div>

        <div class="column"></div>
      </div>
    </form>
  </div>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

注意`<form [formGroup]="form">`和`formControlName="email"`。在 Angular 中，我们这样注册点击处理程序:`(click)='login()'`。

#### 成员构件

成员组件相当简单:

```
<section class="hero" id="hero">
  <div class="hero-head"></div>
  <div class="hero-body">
    <div class="container has-text-centered">
      <h1 class="is-1 title">
        Members area
      </h1>

      <p>Email:
        <b>{{accountData?.email}}</b>
      </p>
      <p>Balance:
        <b>{{accountData?.balance}} {{accountData?.currency}}</b>
      </p>
    </div>
  </div>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

我们用它来显示我们将从 API 中获得的一些数据。非常重要的部分是使用了`?`——这指示 Angular 在渲染模板时不要抛出错误，以防数据尚不存在(因为我们将从 API 中获取这些数据，所以会出现这种情况)。

控制器代码如下:

```
import { Component, OnInit } from '@angular/core';
import { AuthService } from '../auth.service';
import { Router } from '@angular/router';

@Component({
    selector: 'app-members',
    templateUrl: './members.component.html',
    styleUrls: ['./members.component.css']
})
export class MembersComponent implements OnInit {
    accountData: any;
    constructor(private authService: AuthService, private router: Router) { }

    ngOnInit() {
        this.authService.getAccount().subscribe(
            (res: any) => {
                this.accountData = res;
            }, (err: any) => {
                this.router.navigateByUrl('/login');
            }
        );
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

当组件加载时，我们向 API 发送一个请求，成功后，我们将数据保存在`accountData`变量中，然后像前面看到的那样在模板中使用该变量。如果出现错误，我们会将用户转到登录页面。

#### app.component.html

```
<nav class="navbar">
  <div class="container">
    <div class="navbar-brand">
      <a class="navbar-item">
        JWT Angular Login
      </a>
    </div>

    <div id="navbarMenuHeroA" class="navbar-menu">
      <div class="navbar-end">
        <a class="navbar-item" [routerLink]="['']">
          Home
        </a>
        <a class="navbar-item" [routerLink]="['/login']" *ngIf="!authService.isAuthenticated">
          Login
        </a>
        <a class="navbar-item" [routerLink]="['/members']" *ngIf="authService.isAuthenticated">
          Members
        </a>
        <a class="navbar-item" *ngIf="authService.isAuthenticated" (click)="authService.logout()">
          Logout
        </a>
      </div>
    </div>
  </div>
</nav>

<router-outlet></router-outlet>

<footer class="footer">
  <div class="container has-text-centered">
    <div class="content">
      From Croatia with ❤️
    </div>
  </div>
</footer> 
```

Enter fullscreen mode Exit fullscreen mode

这里需要注意的重要部分是使用`*ngIf="!authService.isAuthenticated"`根据用户是否登录来显示/隐藏导航链接。

在“代码”文件中，我们唯一需要做的就是确保通过构造函数`constructor(private authService: AuthService) { }`导入 AuthService。

#### 应用模块. ts

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { RouterModule } from '@angular/router';
import { FormsModule, ReactiveFormsModule } from '@angular/forms';
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';

import { AppComponent } from './app.component';
import { HomeComponent } from './home/home.component';
import { LoginComponent } from './login/login.component';
import { MembersComponent } from './members/members.component';

import { AuthService } from './auth.service';
import { AuthInterceptorService } from './auth-interceptor.service';
import { CanActivateViaAuthGuard } from './can-activate-via-auth.guard';

const routes = [
    { path: 'login', component: LoginComponent },
    {
        path: 'members',
        component: MembersComponent,
        canActivate: [
            CanActivateViaAuthGuard
        ]
    },
    { path: '', component: HomeComponent },
    { path: '**', redirectTo: '' }
];

@NgModule({
    declarations: [
        AppComponent,
        HomeComponent,
        LoginComponent,
        MembersComponent
    ],
    imports: [
        BrowserModule,
        FormsModule,
        ReactiveFormsModule,
        HttpClientModule,
        RouterModule.forRoot(routes)
    ],
    providers: [
        AuthService,
        {
            provide: HTTP_INTERCEPTORS,
            useClass: AuthInterceptorService,
            multi: true
        },
        CanActivateViaAuthGuard
    ],
    bootstrap: [AppComponent]
})
export class AppModule { } 
```

Enter fullscreen mode Exit fullscreen mode

这个文件导入了我们正在使用的所有组件。如您所见，在“声明”中，我们列出了正在使用的组件，`imports`包含了用于处理表单或发送 HTTP 请求的导入组件。

#### 守卫和拦截者

最后，您可能会注意到`providers`数组中的一些新东西，在通常的`AuthService`中，我们定义了两个额外的东西(拦截器服务和授权保护):

```
{
    provide: HTTP_INTERCEPTORS,
    useClass: AuthInterceptorService,
    multi: true
},
CanActivateViaAuthGuard 
```

Enter fullscreen mode Exit fullscreen mode

拦截器服务有一个任务:拦截来自应用程序的每个请求，并在请求头中添加令牌:

```
import { Injectable, Injector } from '@angular/core';
import { HttpInterceptor } from '@angular/common/http';
import { AuthService } from './auth.service';

@Injectable()
export class AuthInterceptorService implements HttpInterceptor {

    constructor(private injector: Injector) { }

    intercept(req, next) {
        const authService = this.injector.get(AuthService);
        const authRequest = req.clone({
            // tslint:disable-next-line:max-line-length
            headers: req.headers.set('Authorization', 'Bearer ' + authService.token)
        });

        return next.handle(authRequest);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

守卫也很简单，它是这样定义的:

```
import { Injectable } from '@angular/core';
import { CanActivate, ActivatedRouteSnapshot, RouterStateSnapshot } from '@angular/router';
import { Observable } from 'rxjs/Observable';
import { AuthService } from './auth.service';

@Injectable()
export class CanActivateViaAuthGuard implements CanActivate {
    constructor(private authService: AuthService) {

    }

    canActivate() {
        return this.authService.isAuthenticated;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们定义了所谓的“canActivate”保护，当我们希望阻止用户进入他无权访问的应用程序中的某个路线时，我们会使用它。我们在路由定义中使用这种保护措施来防止用户在没有登录的情况下进入`/members`链接:

```
const routes = [
    { path: 'login', component: LoginComponent },
    {
        path: 'members',
        component: MembersComponent,
        canActivate: [
            CanActivateViaAuthGuard
        ]
    },
    { path: '', component: HomeComponent },
    { path: '**', redirectTo: '' }
]; 
```

Enter fullscreen mode Exit fullscreen mode

当您将所有这些连接起来(并在本地运行一个 Go 程序)时，您应该看到:

[![](img/b075c19bb0365fe26c447edaeb5b6355.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XVVmgAUZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/7Dk3EJ7.gif)

## 结论

在这篇长文中，我们已经涉及了很多内容:

*   我们了解了一些 jwt 是如何工作的
*   我们用 JavaScript 和 Go 创建了自己的 JWT
*   我们在前端使用 Angular 5 制作了一个成熟的应用程序，在后端使用 JWTs 进行身份验证
*   我们使用了 angularjs 警卫和截击机

我希望这足以让您开始并建立在这个例子之上。

如果你有任何问题，请在评论中联系我们。