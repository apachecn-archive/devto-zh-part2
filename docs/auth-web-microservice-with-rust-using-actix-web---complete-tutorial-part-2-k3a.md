# 使用 Actix-Web 使用 rust 验证 Web 微服务-完整教程第 2 部分

> 原文：<https://dev.to/mygnu/auth-web-microservice-with-rust-using-actix-web---complete-tutorial-part-2-k3a>

###### 原贴于 [gill.net.in](https://gill.net.in)

这个帖子现在已经过时了请阅读更新版本 [Auth Web Microservice with rust 使用 Actix-Web 1.0 -完整教程](https://gill.net.in/posts/auth-microservice-rust-actix-web1.0-diesel-complete-tutorial/)

##### 欢迎回到教程的第 2 部分

在第一部分中，我们成功地建立了我们的授权微服务的批量基础。如果你的应用程序正在运行，并且到目前为止没有出现错误，那么恭喜你。如果你还没有看第一部分的话，来看看吧。本教程的完整代码在 [Gitlab](https://gitlab.com/mygnu/rust-auth-server/tree/master) master 分支上。

##### 是否验证用户邮箱？

从第一部分开始，我们现在有了一个 as 服务器，它从请求中获取一个电子邮件地址，并发出一个带有邀请对象的 JSON 响应。在第一部分中，我说过我们会给用户发一封电子邮件，经过一些思考和反馈，我们现在将跳过这一部分(注意第三部分)。我使用的服务是 sparkpost，你作为本教程的读者可能没有他们的帐户(少量使用是免费的)。

**警告:未经适当的电子邮件验证，请勿在任何真正的应用程序中使用此变通方法。**

##### 工作区

现在，我们将使用来自服务器的 http 响应来验证电子邮件。创建电子邮件验证的最简单的方法是让我们的服务器使用某种通过电子邮件发送到用户电子邮件的秘密，并让他们点击带有秘密的链接进行验证。我们可以使用来自邀请对象的`UUID`作为秘密。假设客户在用 uuid `67a68837-a059-43e6-a0b8-6e57e6260f0d`输入他们的电子邮件后收到一个邀请。

我们可以发送一个请求，在 url 中用上面的`UUID`注册一个新用户。我们的服务器可以获取该 id 并在数据库中找到邀请对象，然后将到期日期与当前时间进行比较。如果所有这些条件都为真，我们将让用户注册，否则发送一个错误响应回来。**现在，我们将把邀请对象返回给客户端作为一种解决方法。**电子邮件支持将在**第 3 部分**中实现。

##### 错误处理和`From`特质

Rust 提供了非常强大的工具，我们可以用它将一种错误转换成另一种。在这个应用程序中，我们将使用不同的客户端进行一些操作，即使用 diesel 保存数据，使用 bcrypt 散列密码等。这些操作可能会返回错误，但是我们需要将它们转换成我们自定义的错误类型。是一种允许我们转换的特性。点击阅读更多关于`From`特质[的信息。通过实现`From`特征，我们可以使用`?`操作符](https://doc.rust-lang.org/std/convert/trait.From.html)[传播许多不同类型的错误](https://doc.rust-lang.org/book/second-edition/ch09-02-recoverable-errors-with-result.html#a-shortcut-for-propagating-errors-the--operator)，这些错误将被转换为我们的`ServiceError`类型。

我们的错误在`errors.rs`中定义，让我们通过为`From` uuid 和 diesel 错误添加 impls 来实现一些`From`特征，我们还将为我们的 ServiceError 枚举添加一个`Unauthorized`变量。文件看起来如下:

```
// errors.rs
use actix_web::{error::ResponseError, HttpResponse};
use std::convert::From;
use diesel::result::{DatabaseErrorKind, Error};
use uuid::ParseError;

#[derive(Fail, Debug)]
pub enum ServiceError {
    #[fail(display = "Internal Server Error")]
    InternalServerError,

    #[fail(display = "BadRequest: {}", _0)]
    BadRequest(String),

    #[fail(display = "Unauthorized")]
    Unauthorized,
}

// impl ResponseError trait allows to convert our errors into http responses with appropriate data
impl ResponseError for ServiceError {
    fn error_response(&self) -> HttpResponse {
        match *self {
            ServiceError::InternalServerError => HttpResponse::InternalServerError().json("Internal Server Error, Please try later"),
            ServiceError::BadRequest(ref message) => HttpResponse::BadRequest().json(message),
            ServiceError::Unauthorized => HttpResponse::Unauthorized().json("Unauthorized")
        }
    }
}

// we can return early in our handlers if UUID provided by the user is not valid
// and provide a custom message
impl From<ParseError> for ServiceError {
    fn from(_: ParseError) -> ServiceError {
        ServiceError::BadRequest("Invalid UUID".into())
    }
}

impl From<Error> for ServiceError {
    fn from(error: Error) -> ServiceError {
        // Right now we just care about UniqueViolation from diesel
        // But this would be helpful to easily map errors as our app grows
        match error {
            Error::DatabaseError(kind, info) => {
                if let DatabaseErrorKind::UniqueViolation = kind {
                    let message = info.details().unwrap_or_else(|| info.message()).to_string();
                    return ServiceError::BadRequest(message);
                }
                ServiceError::InternalServerError
            }
            _ => ServiceError::InternalServerError
        }
    }
} 
```

太好了！随着我们的发展，这些都会变得很方便。

##### 获得一些帮助

我们有时都需要一些帮助。在将密码存储到数据库之前，我们需要对其进行哈希运算。Reddit rust 社区上有一个关于使用什么算法的建议。`argon2`这里是暗示。我同意这个建议，但是为了简单起见，我决定使用`bcrypt`。顺便说一下`bcrypt`算法在生产中被广泛使用，bcrypt crate 提供了一个非常好的接口来散列和验证密码。

为了将一些关注点分开，我们创建了一个新文件`src/utils.rs`并定义了一个如下的帮助散列函数。

```
//utils.rs
use bcrypt::{hash, DEFAULT_COST};
use errors::ServiceError;
use std::env;

pub fn hash_password(plain: &str) -> Result<String, ServiceError> {
    // get the hashing cost from the env variable or use default
    let hashing_cost: u32 = match env::var("HASH_ROUNDS") {
        Ok(cost) => cost.parse().unwrap_or(DEFAULT_COST),
        _ => DEFAULT_COST,
    };
    hash(plain, hashing_cost).map_err(|_| ServiceError::InternalServerError)
} 
```

您可能已经注意到，我们返回了一个`Result`并使用 map_error()来返回我们的自定义错误。这是为了允许我们稍后调用这个函数时使用`?`操作符(另一种转换错误的方法是为 bcrypt 函数返回的错误实现`From`特征)。

现在，让我们将一个方便的方法添加到上一篇教程的`models.rs`中定义的`User`结构中。我们还移除了 **remove_pwd()** 方法，取而代之的是我们将定义另一个没有密码字段的结构`SlimUser`。我们实现了`From` trait 来从用户那里生成 **SlimUser** 。过一会儿，当我们开始使用它时，一切都会变得很清楚。

```
use chrono::{NaiveDateTime, Local};
use std::convert::From;
//... snip
impl User {
    pub fn with_details(email: String, password: String) -> Self {
        User {
            email,
            password,
            created_at: Local::now().naive_local(),
        }
    }
}
//--snip
#[derive(Debug, Serialize, Deserialize)]
pub struct SlimUser {
    pub email: String,
}

impl From<User> for SlimUser {
    fn from(user: User) -> Self {
        SlimUser {
           email: user.email
        }
    }
} 
```

别忘了在你的`main.rs`里加上`extern crate bcrypt;`和`mod utils`。
另一个我在第一部分忘记的有趣的事情是登录控制台。要启用它，将以下内容添加到`main.rs`

```
extern crate env_logger;
// --snip

fn main(){
    dotenv().ok();
    std::env::set_var("RUST_LOG", "simple-auth-server=debug,actix_web=info");
    env_logger::init();
    //--snip
} 
```

##### 注册用户

如果您还记得上一个教程，我们为`Invitation`创建了一个处理程序，现在让我们为注册用户创建一个处理程序。我们将使用一些数据创建一个 struct `RegisterUser`,允许我们验证邀请，然后从数据库创建并返回一个用户。

创建一个新文件`src/register_handler.rs`并将`mod register_handler;`添加到你的`main.rs`中。

```
// register_handler.rs
use actix::{Handler, Message};
use chrono::Local;
use diesel::prelude::*;
use errors::ServiceError;
use models::{DbExecutor, Invitation, User, SlimUser};
use uuid::Uuid;
use utils::hash_password;

// UserData is used to extract data from a post request by the client
#[derive(Debug, Deserialize)]
pub struct UserData {
    pub password: String,
}

// to be used to send data via the Actix actor system
#[derive(Debug)]
pub struct RegisterUser {
    pub invitation_id: String,
    pub password: String,
}

impl Message for RegisterUser {
    type Result = Result<SlimUser, ServiceError>;
}

impl Handler<RegisterUser> for DbExecutor {
    type Result = Result<SlimUser, ServiceError>;
    fn handle(&mut self, msg: RegisterUser, _: &mut Self::Context) -> Self::Result {
        use schema::invitations::dsl::{invitations, id};
        use schema::users::dsl::users;
        let conn: &PgConnection = &self.0.get().unwrap();

        // try parsing the string provided by the user as url parameter
        // return early with error that will be converted to ServiceError
        let invitation_id = Uuid::parse_str(&msg.invitation_id)?;

        invitations.filter(id.eq(invitation_id))
            .load::<Invitation>(conn)
            .map_err(|_db_error| ServiceError::BadRequest("Invalid Invitation".into()))
            .and_then(|mut result| {
                if let Some(invitation) = result.pop() {
                    // if invitation is not expired
                    if invitation.expires_at > Local::now().naive_local() {
                        // try hashing the password, else return the error that will be converted to ServiceError
                        let password: String = hash_password(&msg.password)?;
                        let user = User::with_details(invitation.email, password);
                        let inserted_user: User = diesel::insert_into(users)
                            .values(&user)
                            .get_result(conn)?;

                        return Ok(inserted_user.into()); // convert User to SlimUser
                    }
                }
                Err(ServiceError::BadRequest("Invalid Invitation".into()))
            })
    }
} 
```

##### 解析 url 参数

actix-web 有许多从请求中提取数据的简单方法。

方法之一是使用路径提取器。

> [Path](https://actix.rs/actix-web/actix_web/struct.Path.html) 提供了可以从请求路径中提取的信息。您可以从路径中反序列化任何变量段。

这将允许我们为每个注册为用户邀请创建一个唯一的路径。

让我们在`app.rs`文件中修改我们的注册路径，并添加一个我们稍后将实现的处理函数。

```
// app.rs
/// creates and returns the app after mounting all routes/resources
// add use statement at the top.
use register_routes::register_user;
//...snip
pub fn create_app(db: Addr<DbExecutor>) -> App<AppState> {
    App::with_state(AppState { db })
        //... snip
        // routes to register as a user
        .resource("/register/{invitation_id}", |r| {
           r.method(Method::POST).with(register_user);
        })

} 
```

你可能想暂时注释掉这些变化，因为事情还没有实现，并保持你的应用程序编译和运行。(我尽可能这样做，以获得持续的反馈)。

我们现在需要做的就是实现 register_user()函数，该函数从客户端发送的请求中提取数据，通过向参与者发送`RegisterUser`消息来调用处理程序。除了 url 参数，我们还需要从客户端提取密码。为此，我们已经在`register_handler.rs`中创建了一个`UserData`结构。我们将使用类型`Json`来创建用户数据结构。

> Json 允许将请求体反序列化为结构。为了从请求体中提取类型化信息，类型 T 必须实现 serde 的反序列化特征。

创建一个新文件`src/register_routes.rs`并将`mod register_routes;`添加到你的`main.rs`中。

```
use actix_web::{AsyncResponder, FutureResponse, HttpResponse, ResponseError, State, Json, Path};
use futures::future::Future;

use app::AppState;
use register_handler::{RegisterUser, UserData};

pub fn register_user((invitation_id, user_data, state): (Path<String>, Json<UserData>, State<AppState>))
                     -> FutureResponse<HttpResponse> {
    let msg = RegisterUser {
        // into_inner() returns the inner string value from Path
        invitation_id: invitation_id.into_inner(),
        password: user_data.password.clone(),
    };

    state.db.send(msg)
        .from_err()
        .and_then(|db_response| match db_response {
            Ok(slim_user) => Ok(HttpResponse::Ok().json(slim_user)),
            Err(service_error) => Ok(service_error.error_response()),
        }).responder()
} 
```

##### 测试你的实现

处理完错误(如果有)后，让我们试一试。
调用

```
curl --request POST \
  --url http://localhost:3000/invitation \
  --header 'content-type: application/json' \
  --data '{
    "email":"name@domain.com"
}' 
```

应该会返回类似
的内容

```
{  "id":  "f87910d7-0e33-4ded-a8d8-2264800d1783",  "email":  "name@domain.com",  "expires_at":  "2018-10-27T13:02:00.909757"  } 
```

假设我们通过创建一个链接向用户发送了一封电子邮件，该链接指向一个供用户填写的表单。从那里，我们将让我们的客户端向[http://localhost:3000/register/f 87910d 7-0e 33-4d ed-a8d 8-2264800d 1783](http://localhost:3000/register/f87910d7-0e33-4ded-a8d8-2264800d1783)发送请求。出于演示的目的，您可以使用下面的测试命令测试您的应用程序。

```
curl --request POST \
  --url http://localhost:3000/register/f87910d7-0e33-4ded-a8d8-2264800d1783 \
  --header 'content-type: application/json' \
  --data '{"password":"password"}' 
```

它应该会返回类似于
的内容

```
{  "email":  "name@domain.com"  } 
```

再次运行该命令将导致错误

```
 "Key (email)=(name@domain.com) already exists." 
```

恭喜你，现在你有了一个 web 服务，它可以邀请、验证和创建一个用户，甚至向你发送一个半有用的错误消息。🎉🎉

##### 让我们做验证吧

根据 w3.org:

> 基于令牌的身份验证系统背后的一般概念很简单。允许用户输入他们的用户名和密码，以获得一个令牌，允许他们获取特定的资源-而不使用他们的用户名和密码。一旦获得了令牌，用户就可以向远程站点提供令牌——该令牌提供一段时间内对特定资源的访问。

现在，您选择如何交换令牌可能会涉及安全问题。你会发现围绕互联网的许多讨论/辩论，以及人们使用的许多方式。我对在客户端存储可以被客户端 JavaScript 访问的东西非常谨慎。不幸的是，这种方法在各地成千上万的教程中都有提及。这里有一个很好的阅读[停止使用 JWT 会议](http://cryto.net/%7Ejoepie91/blog/2016/06/13/stop-using-jwt-for-sessions/)。

除了`don't follow online tutorials blindly and do your own research`之外，我不知道该给读者什么建议。本教程的目的是了解 Actix-web 和 rust not 如何防止您的服务器出现漏洞。出于本教程的考虑，我们将使用 http 专用 cookies 来交换令牌。

**请勿在生产中使用。**

现在这已经不碍事了😰，让我们看看我们能做些什么。actix-web 为我们提供了一种简洁的中间件方式来处理这里记录的会话 cookie[actix _ web::中间件::身份](https://actix.rs/actix-web/actix_web/middleware/identity/index.html)。为了启用这个功能，我们修改我们的`app.rs`文件如下。

```
use actix_web::middleware::identity::{CookieIdentityPolicy, IdentityService};
use chrono::Duration;
//--snip
pub fn create_app(db: Addr<DbExecutor>) -> App<AppState> {
    // secret is a random 32 character long base 64 string
    let secret: String = std::env::var("SECRET_KEY").unwrap_or_else(|_| "0".repeat(32));
    let domain: String = std::env::var("DOMAIN").unwrap_or_else(|_| "localhost".to_string());

    App::with_state(AppState { db })
        .middleware(Logger::default())
        .middleware(IdentityService::new(
            CookieIdentityPolicy::new(secret.as_bytes())
                .name("auth")
                .path("/")
                .domain(domain.as_str())
                .max_age(Duration::days(1)) // just for testing
                .secure(false),
        ))
        //--snip
} 
```

这给了我们非常方便的方法，像`req.remember(data)`、`req.identity()`和`req.forget()`等等。这将依次设置和删除客户端的身份验证 cookie。

##### JWT？

在写这篇教程的时候，我碰到了一些关于使用什么 JWT 库的讨论。通过简单的搜索，我找到了一些，并决定使用 [frank_jwt](https://crates.io/crates/frank_jwt) 但随后 [Vincent Prouillet](https://github.com/Keats) [指出了不完整的内容](https://github.com/GildedHonour/frank_jwt/blob/master/src/lib.rs#L296-L323)并建议使用 [jsonwebtoken](https://crates.io/crates/jsonwebtoken) 。在使用 lib 遇到麻烦后，我从他们那里得到了很大的回应。现在回购已经有了工作示例，我能够实现下面的默认解决方案。请注意，这不是最安全的 JWT 实现，您可能需要查找参考资料以使其更好地满足您的需求。

在我们创建 auth handler 和 route 函数之前，让我们为 jwt 编码和解码添加一些助手函数。别忘了在你的`main.rs`里加上`extern crate jsonwebtoken as jwt;`。

如果有人有更好的实现方式，我会很乐意接受 PR。

```
// utils.rs
use models::SlimUser;
use std::convert::From;
use jwt::{decode, encode, Header, Validation};
use chrono::{Local, Duration};
//--snip

#[derive(Debug, Serialize, Deserialize)]
struct Claims {
    // issuer
    iss: String,
    // subject
    sub: String,
    //issued at
    iat: i64,
    // expiry
    exp: i64,
    // user email
    email: String,
}

// struct to get converted to token and back
impl Claims {
    fn with_email(email: &str) -> Self {
        Claims {
            iss: "localhost".into(),
            sub: "auth".into(),
            email: email.to_owned(),
            iat: Local::now().timestamp(),
            exp: (Local::now() + Duration::hours(24)).timestamp(),
        }
    }
}

impl From<Claims> for SlimUser {
    fn from(claims: Claims) -> Self {
        SlimUser { email: claims.email }
    }
}

pub fn create_token(data: &SlimUser) -> Result<String, ServiceError> {
    let claims = Claims::with_email(data.email.as_str());
    encode(&Header::default(), &claims, get_secret().as_ref())
        .map_err(|_err| ServiceError::InternalServerError)
}

pub fn decode_token(token: &str) -> Result<SlimUser, ServiceError> {
    decode::<Claims>(token, get_secret().as_ref(), &Validation::default())
        .map(|data| Ok(data.claims.into()))
        .map_err(|_err| ServiceError::Unauthorized)?
}

// take a string from env variable
fn get_secret() -> String {
    env::var("JWT_SECRET").unwrap_or("my secret".into())
} 
```

##### 授权处理

你现在知道程序了😉，让我们创建一个新文件`src/auth_handler.rs`并将`mod auth_handler;`添加到您的`main.rs`中。

```
//auth_handler.rs
use actix::{Handler, Message};
use diesel::prelude::*;
use errors::ServiceError;
use models::{DbExecutor, User, SlimUser};
use bcrypt::verify;
use actix_web::{FromRequest, HttpRequest, middleware::identity::RequestIdentity};

#[derive(Debug, Deserialize)]
pub struct AuthData {
    pub email: String,
    pub password: String,
}

impl Message for AuthData {
    type Result = Result<SlimUser, ServiceError>;
}

impl Handler<AuthData> for DbExecutor {
    type Result = Result<SlimUser, ServiceError>;
    fn handle(&mut self, msg: AuthData, _: &mut Self::Context) -> Self::Result {
        use schema::users::dsl::{users, email};
        let conn: &PgConnection = &self.0.get().unwrap();
        let mismatch_error = Err(ServiceError::BadRequest("Username and Password don't match".into()));

        let mut items = users
            .filter(email.eq(&msg.email))
            .load::<User>(conn)?;

        if let Some(user) = items.pop() {
            match verify(&msg.password, &user.password) {
                Ok(matching) => {
                    if matching { return Ok(user.into()); } else { return mismatch_error; }
                }
                Err(_) => { return mismatch_error; }
            }
        }
        mismatch_error
    }
} 
```

上面的处理程序采用了包含客户端发送的电子邮件和密码的`AuthData`结构。我们使用电子邮件从数据库中提取用户，并使用 bcrypt 验证函数来匹配密码。如果一切顺利，我们返回用户或者返回`BadRequest`错误。

现在让我们用下面的内容创建`src/auth_routes.rs`:

```
// auth_routes.rs
use actix_web::{AsyncResponder, FutureResponse, HttpResponse, HttpRequest, ResponseError, Json};
use actix_web::middleware::identity::RequestIdentity;
use futures::future::Future;
use utils::create_token;

use app::AppState;
use auth_handler::AuthData;

pub fn login((auth_data, req): (Json<AuthData>, HttpRequest<AppState>))
             -> FutureResponse<HttpResponse> {
    req.state()
        .db
        .send(auth_data.into_inner())
        .from_err()
        .and_then(move |res| match res {
            Ok(slim_user) => {
                let token = create_token(&slim_user)?;
                req.remember(token);
                Ok(HttpResponse::Ok().into())
            }
            Err(err) => Ok(err.error_response()),
        }).responder()
}

pub fn logout(req: HttpRequest<AppState>) -> HttpResponse {
    req.forget();
    HttpResponse::Ok().into()
} 
```

我们的登录方法从请求中提取`AuthData`,并将消息发送给我们在 auth_handler.rs 中实现的`DbEexcutor` Actor handler。在这里，如果一切正常，我们得到一个返回给我们的用户，我们使用我们之前在 utils.rs 中定义的 helper 函数来创建一个令牌并调用`req.remember(token)`。这又为客户端设置了一个带有令牌的 cookie 头来保存。

我们现在需要做的最后一件事是在我们的`app.rs`中使用登录/注销功能。将`.rsource("/auth")`关闭更改为:

```
.resource("/auth", |r| {
            r.method(Method::POST).with(login);
            r.method(Method::DELETE).with(logout);
        }) 
```

但是不要忘记在文件的顶部添加`use auth_routes::{login, logout};`。

##### 测试运行授权

如果您一直按照教程操作，那么您已经创建了一个具有电子邮件和密码的用户。使用下面的 curl 命令来测试我们的服务器。

```
curl -i --request POST \
  --url http://localhost:3000/auth \
  --header 'content-type: application/json' \
  --data '{
        "email": "name@domain.com",
        "password":"password"
}'

## response
HTTP/1.1 200 OK
set-cookie: auth=iqsB4KUUjXUjnNRl1dVx9lKiRfH24itiNdJjTAJsU4CcaetPpaSWfrNq6IIoVR5+qKPEVTrUeg==; HttpOnly; Path=/; Domain=localhost; Max-Age=86400
content-length: 0
date: Sun, 28 Oct 2018 12:36:43 GMT 
```

如果你收到了一个 200 的响应，如上所示，带有一个 set-cookie 头，那么恭喜你已经成功登录了。

为了测试注销，我们向`/auth`发送一个删除请求，确保您得到的 set-cookie 头包含空数据和立即到期日期。

```
curl -i --request DELETE \
  --url http://localhost:3000/auth

## response
HTTP/1.1 200 OK
set-cookie: auth=; HttpOnly; Path=/; Domain=localhost; Max-Age=0; Expires=Fri, 27 Oct 2017 13:01:52 GMT
content-length: 0
date: Sat, 27 Oct 2018 13:01:52 GMT 
```

##### 实现受保护的路由

使用 Auth 的全部目的是有办法验证请求是否来自经过身份验证的客户端。Actix-web 有一个特性`FromRequest`,我们可以在任何类型上实现它，然后用它从请求中提取数据。在这里看文档[。我们将在`auth_handler.rs`的底部添加以下内容。](https://actix.rs/actix-web/actix_web/trait.FromRequest.html) 

```
//auth_handler.rs
//--snip
use actix_web::FromRequest;
use utils::decode_token;
//--snip

// we need the same data as SlimUser
// simple aliasing makes the intentions clear and its more readable
pub type LoggedUser = SlimUser;

impl<S> FromRequest<S> for LoggedUser {
    type Config = ();
    type Result = Result<LoggedUser, ServiceError>;
    fn from_request(req: &HttpRequest<S>, _: &Self::Config) -> Self::Result {
        if let Some(identity) = req.identity() {
            let user: SlimUser = decode_token(&identity)?;
            return Ok(user as LoggedUser);
        }
        Err(ServiceError::Unauthorized)
    }
} 
```

我们选择使用类型别名，而不是创建一个全新的类型。当我们从请求中提取`LoggedUser`时，读者会知道它是经过身份验证的用户。FromRequest 特征只是试图将 cookie 中的字符串反序列化到我们的结构中，如果失败，它只是返回未授权的错误。为了测试这一点，我们需要将实际路线添加到或应用程序。我们简单地给`auth_routes.rs`
添加另一个函数

```
//auth_routes.rs
//--snip

pub fn get_me(logged_user: LoggedUser) -> HttpResponse {
    HttpResponse::Ok().json(logged_user)
} 
```

为了调用它，我们在`app.rs`资源中注册了这个方法。它看起来像下面的。

```
//app.rs
use auth_routes::{login, logout, get_me};
//--snip

.resource("/auth", |r| {
    r.method(Method::POST).with(login);
    r.method(Method::DELETE).with(logout);
    r.method(Method::GET).with(get_me);
})
//--snip 
```

##### 测试登录用户

在终端中尝试以下 Curl 命令。

```
curl -i --request POST \
  --url http://localhost:3000/auth \
  --header 'content-type: application/json' \
  --data '{
        "email": "name@domain.com",
        "password":"password"
}'
# result would be something like
HTTP/1.1 200 OK
set-cookie: auth=HdS0iPKTBL/4MpTmoUKQ5H7wft5kP7OjP6vbyd05Ex5flLvAkKd+P2GchG1jpvV6p9GQtzPEcg==; HttpOnly; Path=/; Domain=localhost; Max-Age=86400
content-length: 0
date: Sun, 28 Oct 2018 19:16:12 GMT

## and then pass the cookie back for a get request
curl -i --request GET \
  --url http://localhost:3000/auth \
  --cookie auth=HdS0iPKTBL/4MpTmoUKQ5H7wft5kP7OjP6vbyd05Ex5flLvAkKd+P2GchG1jpvV6p9GQtzPEcg==
## result
HTTP/1.1 200 OK
content-length: 27
content-type: application/json
date: Sun, 28 Oct 2018 19:21:04 GMT

{"email":"name@domain.com"} 
```

它应该成功地返回你的电子邮件作为 json 回来。只有拥有有效的 auth cookie 和令牌的登录用户或请求才会通过您提取的路由`LoggedUser`。

##### 接下来是什么？

在本教程的第 3 部分，我们将为这个应用程序创建**电子邮件验证**和**前端**。为此，我想使用某种 rust html 模板系统。与此同时，我正在学习 angular，所以我可能会在它的前面安装一个小的 Angular 应用程序。
如果你有任何问题或建议，请通过 [twitter](https://twitter.com/mygnu_) 联系我。