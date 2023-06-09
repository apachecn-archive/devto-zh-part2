# 使用 Actix-Web 使用 rust 验证 Web 微服务-完整教程第 1 部分

> 原文：<https://dev.to/mygnu/auth-web-microservice-with-rust-using-actix-web---complete-tutorial-part-1-2id8>

这个帖子现在已经过时了请阅读更新版本 [Auth Web Microservice with rust 使用 Actix-Web 1.0 -完整教程](https://gill.net.in/posts/auth-microservice-rust-actix-web1.0-diesel-complete-tutorial/)

### 什么？

我们将在`rust`中创建一个网络服务器，只处理用户注册和认证。在我们进行的过程中，我会解释每个文件中的步骤。完整的项目代码在这里[回购](https://gitlab.com/mygnu/rust-auth-server/tree/part_one)。请不要全信，因为我还是一个生锈的新手😉。

##### 事件的流程会是这样的:

*   注册电子邮件地址➡收到一个📨用链接来验证
*   按照链接➡注册相同的电子邮件和密码
*   用电子邮件和密码登录➡得到验证，并收到智威汤逊令牌

##### 我们要用的板条箱

*   [actix](https://crates.io/crates/actix) // Actix 是一个 Rust actors 框架。
*   [actix-web](https://crates.io/crates/actix-web) // Actix web 是一个简单、实用、速度极快的 fast web 框架。
*   [brcypt](https://crates.io/crates/bcrypt) //使用 bcrypt 轻松散列和验证密码。
*   [chrono](https://crates.io/crates/chrono) //锈日期时间库。
*   用于 PostgreSQL、SQLite 和 MySQL 的安全、可扩展的 ORM 和查询生成器。
*   [dotenv](https://crates.io/crates/dotenv)//Rust 的一个 dotenv 实现。
*   [env_logger](https://crates.io/crates/env_logger) //日志的日志实现，通过环境变量配置。
*   [失败](https://crates.io/crates/failure) //实验性错误处理抽象。
*   [jsonwebtoken](https://crates.io/crates/jsonwebtoken) //以强类型方式创建并解析 JWT。
*   [futures](https://crates.io/crates/futures)//futures 和 streams 的实现，具有零分配、可组合性和类似迭代器的接口。
*   [r2d2](https://crates.io/crates/r2d2) //通用连接池。
*   serde //一个通用的序列化/反序列化框架。
*   [serde_json](https://crates.io/crates/serde_json) //一种 json 序列化文件格式。
*   [serde_derive](https://crates.io/crates/serde_derive) //宏 1.1 实现#[derive(Serialize，Deserialize)]。
*   [sparkpost](https://crates.io/crates/sparkpost) //为 sparkpost 电子邮件 api v1 信任绑定。
*   [uuid](https://crates.io/crates/uuid) //生成并解析 uuid 的库。

我已经从他们的官方描述中提供了关于正在使用的板条箱的简要信息。如果您想了解这些板条箱的更多信息，请点击名称前往`crates.io`。**不要脸插:** `sparkpost`是我的板条箱喜欢/不喜欢请留下反馈。

##### 先决条件

这里我假设你有一些编程知识，最好也有一些 rust。需要一个`rust`的工作设置。查看 [https://rustup.rs](https://rustup.rs) 获取 esasy rust 设置。了解更多关于铁锈结帐[T5 本书](https://doc.rust-lang.org/stable/book/2018-edition/index.html)。

我们将使用 diesel 来创建模型，并处理数据库、查询和迁移。请前往 http://diesel.rs/guides/getting-started/的T3 开始设置`diesel_cli`。在本教程中，我们将使用`postgresql`,所以请按照说明来设置 postgres。您需要有一个正在运行的 postgres 服务器，并且可以创建一个数据库来完成本教程。另一个不错的工具是 [Cargo Watch](https://github.com/passcod/cargo-watch) ，它可以让你观察文件系统，并在你做任何改变时重新编译和运行应用程序。

如果您的系统上没有用于本地测试 api 的软件，请安装`Curl`。

##### 我们开始吧

在检查了你的 rust 和 cargo 版本并用
创建了一个新项目之后

```
# at the time of writing this tutorial my setup is 
rustc --version && cargo --version
# rustc 1.29.1 (b801ae664 2018-09-20)
# cargo 1.29.0 (524a578d7 2018-08-05)

cargo new simple-auth-server
# Created binary (application) `simple-auth-server` project

cd simple-auth-server # and then 

# watch for changes re-compile and run
cargo watch -x run 
```

在货物依赖项中填入以下内容，我将在项目中使用时逐一查看。我使用的是板条箱的清晰版本，正如你所知，东西会变老和改变。(如果您在本教程创建很长时间后还在阅读它)。在本教程的第 1 部分中，我们不会用到所有的工具，但是在最终的应用程序中它们都会变得很方便。

```
[dependencies]
actix = "0.7.4"
actix-web = "0.7.8"
bcrypt = "0.2.0"
chrono = { version = "0.4.6", features = ["serde"] }
diesel = { version = "1.3.3", features = ["postgres", "uuid", "r2d2", "chrono"] }
dotenv = "0.13.0"
env_logger = "0.5.13"
failure = "0.1.2"
frank_jwt = "3.0"
futures = "0.1"
r2d2 = "0.8.2"
serde_derive="1.0.79"
serde_json="1.0"
serde="1.0"
sparkpost = "0.4"
uuid = { version = "0.6.5", features = ["serde", "v4"] } 
```

##### 设置基础 APP

创建新文件`src/models.rs` `src/app.rs`。

```
// models.rs
use actix::{Actor, SyncContext};
use diesel::pg::PgConnection;
use diesel::r2d2::{ConnectionManager, Pool};

/// This is db executor actor. can be run in parallel
pub struct DbExecutor(pub Pool<ConnectionManager<PgConnection>>);

// Actors communicate exclusively by exchanging messages. 
// The sending actor can optionally wait for the response. 
// Actors are not referenced directly, but by means of addresses.
// Any rust type can be an actor, it only needs to implement the Actor trait.
impl Actor for DbExecutor {
    type Context = SyncContext<Self>;
} 
```

要使用这个 Actor，我们需要设置`actix-web`服务器。在`src/app.rs`中我们有以下内容。我们暂时让资源构建器为空。这是路由的核心部分要去的地方。

```
// app.rs
use actix::prelude::*;
use actix_web::{http::Method, middleware, App};
use models::DbExecutor;

pub struct AppState {
    pub db: Addr<DbExecutor>,
}

// helper function to create and returns the app after mounting all routes/resources
pub fn create_app(db: Addr<DbExecutor>) -> App<AppState> {
    App::with_state(AppState { db })
        // setup builtin logger to get nice logging for each request
        .middleware(middleware::Logger::new("\"%r\" %s %b %Dms"))

         // routes for authentication
        .resource("/auth", |r| {
        })
        // routes to invitation
        .resource("/invitation/", |r| {
        })
        // routes to register as a user after the
        .resource("/register/", |r| {
        })
} 
```

```
// main.rs
// to avoid the warning from diesel macros
#![allow(proc_macro_derive_resolution_fallback)]

extern crate actix;
extern crate actix_web;
extern crate serde;
extern crate chrono;
extern crate dotenv;
extern crate futures;
extern crate r2d2;
extern crate uuid;
#[macro_use] extern crate diesel;
#[macro_use] extern crate serde_derive;
#[macro_use] extern crate failure;

mod app;
mod models;
mod schema;
// mod errors;
// mod invitation_handler;
// mod invitation_routes;

use models::DbExecutor;
use actix::prelude::*;
use actix_web::server;
use diesel::{r2d2::ConnectionManager, PgConnection};
use dotenv::dotenv;
use std::env;

fn main() {
    dotenv().ok();
    let database_url = env::var("DATABASE_URL").expect("DATABASE_URL must be set");
    let sys = actix::System::new("Actix_Tutorial");

    // create db connection pool
    let manager = ConnectionManager::<PgConnection>::new(database_url);
    let pool = r2d2::Pool::builder()
        .build(manager)
        .expect("Failed to create pool.");

    let address :Addr<DbExecutor>  = SyncArbiter::start(4, move || DbExecutor(pool.clone()));

    server::new(move || app::create_app(address.clone()))
        .bind("127.0.0.1:3000")
        .expect("Can not bind to '127.0.0.1:3000'")
        .start();

    sys.run();
} 
```

在这个阶段，您的服务器应该在`127.0.0.1:3000`上编译并运行。现在它没有做任何有用的事情。让我们创建一些模型。

##### 设置 Diesel 并创建我们的用户模型

我们从为用户创建一个模型开始。根据前面的步骤，假设您已经安装了`postgres`和`diesel-cli`并且正在工作。在您的终端`echo DATABASE_URL=postgres://username:password@localhost/database_name > .env`中，按照您的设置替换数据库名称、用户名和密码。然后我们在终端运行`diesel setup`。这将创建我们的数据库，如果不存在，并设置一个迁移目录等。

让我们写一些`SQL`，好吗？通过`diesel migration generate users`和`diesel migration generate invitations`邀请创建迁移。打开 migrations 文件夹中的 up.sql 和 down.sql 文件，分别添加以下 sql。

```
 --migrations/TIMESTAMP_users/up.sql
CREATE TABLE users (
  email VARCHAR(100) NOT NULL PRIMARY KEY,
  password VARCHAR(64) NOT NULL, --bcrypt hash
  created_at TIMESTAMP NOT NULL
);

--migrations/TIMESTAMP_users/down.sql
DROP TABLE users;

--migrations/TIMESTAMP_invitations/up.sql
CREATE TABLE invitations (
  id UUID NOT NULL PRIMARY KEY,
  email VARCHAR(100) NOT NULL,
  expires_at TIMESTAMP NOT NULL
);

--migrations/TIMESTAMP_invitations/down.sql
DROP TABLE invitations; 
```

命令`diesel migration run`将在 DB 中创建表和文件`src/schema.rs`。这是我将谈到的关于 diesel-cli 和迁移的范围。请阅读他们的文档以了解更多信息。

在这个阶段，我们已经创建了数据库中的表，让我们编写一些代码来创建 rust 中的用户和邀请的表示。在`models.rs`中，我们添加了以下内容。

```
// models.rs
...
// --- snip
use chrono::NaiveDateTime;
use uuid::Uuid;
use schema::{users,invitations};

#[derive(Debug, Serialize, Deserialize, Queryable, Insertable)]
#[table_name = "users"]
pub struct User {
    pub email: String,
    pub password: String,
    pub created_at: NaiveDateTime, // only NaiveDateTime works here due to diesel limitations
}

impl User {
    // this is just a helper function to remove password from user just before we return the value out later
    pub fn remove_pwd(mut self) -> Self {
        self.password = "".to_string();
        self
    }
}

#[derive(Debug, Serialize, Deserialize, Queryable, Insertable)]
#[table_name = "invitations"]
pub struct Invitation {
    pub id: Uuid,
    pub email: String,
    pub expires_at: NaiveDateTime,
} 
```

检查您的实现没有错误/警告，并留意终端中的`cargo watch -x run`命令。

### [阅读更多...](https://hgill.io/posts/auth-microservice-rust-actix-web-diesel-complete-tutorial-part-1/#read-more)