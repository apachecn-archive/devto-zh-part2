# 通过 Re-Frame/actix_web 应用程序跟踪点击

> 原文：<https://dev.to/deciduously/stalk-a-click-through-a-re-frameactixweb-app-55fb>

# 在 20s 上滚动

## 为什么

我没有看到大量关于这两个工具的资料，尤其是在初学者的水平上，这是一个获得所有重要部分的概述的可靠方法。

## 事情

这是一个玩具掷骰子，在这个 [github repo](https://github.com/deciduously/roll) 中托管。用户可以提交常规的旧掷骰子:`1d6, 2d10`，或者定义他们的芒别名。例如，您可以将`3d8`保存到`goblin`，然后只需输入`goblin`。它还接受一个乘数来重复掷骰子:`2 2d6`或`27 goblin`。多个命令按顺序运行:`2d9 goblin`可以作为一个命令运行，也可以同时运行两个命令。

## 工具

后端使用 [actix_web](https://actix.rs) 连接到一个用于存储上述别名的 [SQLite](https://sqlite.org/index.html) 数据库。前端是用建立在[试剂](https://reagent-project.github.io/)之上的 [Re-Frame](https://github.com/Day8/re-frame) 框架实现的，这是一种在 ClojureScript 中定义 React 组件的方法。Re-Frame 提供了一个功能状态管理解决方案——使用普通试剂(与 React.js 没有什么不同)您可以独立完成。

## 旅途

我将只研究一条端到端的路径——单击以提交 roll 命令。我们假设已经定义了上面的别名示例:`goblin: 3d8`。我不会在你第一次去的时候扔 27 个妖精给你，虽然，我不是*撒旦*...让我们试试命令`3 goblin`，看看我们进展如何。

我正在关注 Re-Frame 和 actix_web 的东西，所以为了节省时间，一些函数体将被省略。这篇文章设法变得相当长，无论如何没有深入每一行！我会在全文中加入相关文件的链接。

### 采集输入

Re-Frame 为定义应用程序的不同部分提供了一个严格的结构。我们将跳过其中的一些，但是我们的点击之旅是从一个文本框和一个按钮开始的。所有 UI 代码都位于 [views.cljs](https://github.com/deciduously/roll/blob/master/src/cljs/roll/views.cljs) 中。

试剂，如果你不熟悉的话，就是*牛逼*。说真的，我甚至再次给你[链接](https://reagent-project.github.io/)。它从 React 中去除了许多仪式，并提取了组件的核心。每个组件都是一个函数，它模仿 [hiccup](https://github.com/weavejester/hiccup) 的语法，允许你以简洁的 Clojure 向量的形式定义你的 HTML。

你使用像`:div`这样的关键字——一个`[:div]`向量将扩展到`<div></div>`。其他一切都是该元素的子元素。每个向量可选地采用如上的选项映射，或者更快的简写:`[:span#firstName.name.focus "SPAN!"]`扩展为`<span id=\"firstName\" class=\"name focus\">SPAN!</span>`。Lisps 的所有树都是开放的，这是表示和操作像 DOM 这样的树结构的自然选择。非常适合 React 应用的原型开发！

具体组件如下:

```
(defn  command-input  "Command input"  []  [:div  "Command: "  [:input  {:type  "text"  :id  "field"  :name  "cmd"}]  [:input  {:type  "button"  :value  "Submit"  :on-click  #(re-frame/dispatch  [::events/submit-command  (->  (.getElementById  js/document  "field")  .-value)])}]]) 
```

Enter fullscreen mode Exit fullscreen mode

撇开可能不熟悉的 clo jure-close 不谈，这很容易阅读。当在试剂树中调用时，该函数是一个定义输入文本框和按钮的试剂组件，类似于使用任何前端工具编写的代码。

当用户输入一些东西并点击按钮时，我们的旅程就开始了。行为在点击处理程序:

```
#(re-frame/dispatch  [::events/submit-command  (->  (.getElementById  js/document  "field")  .-value)])` 
```

Enter fullscreen mode Exit fullscreen mode

`#()`在 Clojure 中定义一个匿名函数。在 JavaScript 中，这类似于`() => {/* stuff */}`。任何论点都是`%, %2, %3`等，如果使用:`#(%)` = > `(fn [arg1] (arg1))`。这个没有。

JS interop 在 ClojureScript 中非常简单。我们调用了`document.getElementById('field')`，重新排列后，Clojure-style 函数位于 s 表达式的第一个位置。随后的细分将跟随`"field"`。真的就那么简单。要访问该元素的 value 属性，可以使用`.-value`语法——否则 CLJS 会认为你试图调用方法`value()`。

这个代码片段使用了线程宏`->`，它像管道一样工作。它可以让你编写链式操作而不用嵌套太深，这是 Lisps 臭名昭著的地方。也许仅仅两个操作是不必要的，但是我发现这更具可读性并且一致性总是好的。

### 进入重帧

在 [events.cljs](https://github.com/deciduously/roll/blob/master/src/cljs/roll/events.cljs) 中，这个`submit-command`事件和这个应用程序处理的所有其他事件一起被定义。漂亮整洁。这就是我如此喜欢使用重新框架的原因。一旦你理解了这个模型——它并不像一开始听起来那么复杂——任何新代码应该放在哪里总是很明确的。它也是 GitHub 上最好的读物之一，但那只是我的看法。

请注意，我们在这里实际上并没有调用函数来处理事件——我们将一个包含事件名称的数据结构传递给`re-frame/dispatch`,它将按照 FIFO 的顺序为我们处理该事件。让我们具体看一下这个事件:

```
(re-frame/reg-event-fx  ::submit-command  (fn-traced  [_  [_  cmd]]  {:http-xhrio  {:method  :get  :uri  (str  "http://localhost:8080/roll/"  (clojure.string/replace  cmd  #" "  "/"))  :timeout  8000  :response-format  (ajax/json-response-format  {:keywords?  true})  :on-success  [::save-roll]  :on-failure  [::bad-http-result]}})) 
```

Enter fullscreen mode Exit fullscreen mode

您通过用名副其实的`reg-event-fx`函数为调度程序注册其*效果*来创建一个事件。注意我们只是给它命名，然后立即打开一个`fn`——与`defn`宏没有什么不同。`fn-traced`只是允许这个事件插入到优秀的 [re-frame-10x](https://github.com/Day8/re-frame-10x) devtools 中——否则就是一个 lambda。

`fn`的参数是`[cofx event]`。我们还没有使用任何联合效应。我们会的，不要担心，但是对于这个事件，我用`_`来忽略他们。然后`event`参数被析构。还记得`event`向量吗？刚才我们自己做的:`[::events/submit-command (-> (.getElementById js/document "field") .-value)]`。第一部分只是事件的名称，我们并不需要——还有另一个`_`——我们将用户输入的内容存储为`cmd`。

这个事件利用官方支持的 [http-fx](https://github.com/Day8/re-frame-http-fx) 库来执行 AJAX 请求。这个库提供了`:http-xhrio`效果处理程序。这也很容易使用——您可以通过请求传递一个选项映射。它包含了你预期需要定义的所有部分。

我们`3 goblin`的特定`cmd`出现在 URI，毫无意外。我们用`/` : `http://localhost:8080/roll/3/goblins`代替空格。

这个库让你指定你正在使用的格式——我们将一直使用 JSON。

同样值得注意的是，我们定义了成功(`200`)或失败(其他)时发生的事情。这两个事件只是在同一个源文件中定义的其他事件。一旦响应返回，重新帧调度程序将调用适当的后续程序。

然而，在我们看到它之前，我们必须实际生成响应！让我们前往后端，看看那个`GET /roll`处理程序。

### 后宅

从第 78 行开始，我们服务器的整个轮廓在 [`main.rs`](https://github.com/deciduously/roll/blob/master/src/main.rs) 中定义。

Actix 内置了对 CORS 的支持——任何在初始设置中注册的资源都会自动获得正确的行为。与许多 Rust APIS 一样，我们使用构建器模式来定义应用程序的配置。一旦所有的配置都完成了，我们用`register()`来结束它。有问题的资源在第[行第 89](https://github.com/deciduously/roll/blob/dd747bb59b7d25ebe8a047d2f2d37f42e3f71bae/src/main.rs#L89) :
行

```
.resource("/roll/{tail:.*}", |r| {r.method(http::Method::GET).with(roll)}) 
```

Enter fullscreen mode Exit fullscreen mode

这定义了端点，指定了方法，并调用了特定的处理程序`roll`。`{tail:.*}`意味着 URL 中在`roll/`之后的任何内容都将作为`tail`传递给请求中的处理程序。当请求到达服务器时，它会连续尝试定义的每个资源。如果它匹配这个端点和方法，这个处理程序将从 [`handlers.rs`](https://github.com/deciduously/roll/blob/master/src/handlers.rs) :
被调用

```
// GET /roll/{cmd}
pub fn roll(req: HttpRequest) -> impl Responder {
    let cmd = &req.match_info()["tail"];
    let cmds = ((&cmd)
        .split('/')
        .collect::<Vec<&str>>()
        .iter()
        .map(|s| s.to_string()))
        .collect::<Vec<String>>();
    roll_strs(&cmds)
} 
```

Enter fullscreen mode Exit fullscreen mode

我发现 actix_web 非常符合人体工程学。首先，它是返回类型中花哨语法的早期体现。为了像处理程序一样工作，你的函数只需要返回任何实现了`Responder`特征的类型，`actix_web`提供了许多现成的类型，比如`String`甚至`Json`(它内置了 [`serde`](https://serde.rs/) )。或者，您可以像我们将要做的那样自己实现它。

用`&req.match_info("tail")`得到`3/goblin`后，我们只要把它变成`vec!["3", "goblin"]`传给`roll_strs()`就行了。这是我们对`roll()`的返回值，所以我们知道无论这个函数返回什么都会实现`Responder`。

### 土豆肉

如前所述`roll_strs()`返回自定义类型`Outcomes`，需要手动实现`Responder` :

```
#[derive(Serialize)]
pub struct Outcomes {
    pub outcomes: Vec<Outcome>,
}

pub fn roll_strs(s: &[String]) -> Outcomes {
    validate_input(s).unwrap().run()
} 
```

Enter fullscreen mode Exit fullscreen mode

自定义类型只是一个`Vec<Outcome>`的包装器。这是一个结果:

```
#[derive(Clone, Debug, Serialize)]
pub struct Outcome {
    roll: String,
    rolls: Vec<u32>,
} 
```

Enter fullscreen mode Exit fullscreen mode

这些结构定义了我们的 JSON 响应的形状。对`3 goblin`的响应将如下所示:

```
{"outcomes":  [  {"roll":"3d8","rolls":[6,1,3]},  {"roll":"3d8","rolls":[7,1,8]},  {"roll":"3d8","rolls":[8,1,5]}  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

实现起来并不困难。它只有一个功能，`respond_to` :

```
impl Responder for Outcomes {
    type Item = HttpResponse;
    type Error = Error;

    fn respond_to<S>(self, _req: &HttpRequest<S>) -> Result<HttpResponse, Error> {
        let body = serde_json::to_string(&self)?;

        Ok(HttpResponse::Ok()
            .content_type("application/json")
            .body(body))
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以很容易地从我们的自定义类型中创建`Json`,因为`Serialize`特征是我们自动派生的——所需要的只是`serde_json::to_string(&Outcomes)?`。然后我们构建一个成功的`HttpResponse`，给它预期的`Content-Type`，并包含我们的 JSON 字符串作为响应体。如果我们因为某种原因无法构建 json，那么在`serde_json::to_string()`末尾的`?`将会返回一个`actix_web::Error`——这也将导致一个`HttpResponse`返回给用户，但是带有一个不成功的代码。

为了简洁起见，我将跳过这个机制——从 Rust 中的`3d8`这样的输入中获得`Outcome`没有什么革命性的。这一切都藏在 [`roll.rs`](https://github.com/deciduously/roll/blob/master/src/roll.rs) 供好奇者观赏。

不过，首先，我们必须从`goblin`中抓取`3d8`，并知道滚动它三次。`roll_strs`来电正文首先为我们介绍了 [`command.rs`](https://github.com/deciduously/roll/blob/master/src/command.rs) 中的好东西。让我们来看看。

### 命令解析

首先，我们`validate_input(s)`。下面是签名——正文中没有任何花哨的内容:

```
pub fn validate_input(s: &[String]) -> io::Result<Command> {
   // parsing with regular expressions
} 
```

Enter fullscreen mode Exit fullscreen mode

简而言之，我们查看传入的一系列字符串，并尝试返回一个`Command` :

```
#[derive(Debug, PartialEq)]
pub enum Command {
    Roll(Vec<Roll>),              // One or more XdX args
    Multiplier(u32, Vec<String>), // an integer repeater, and then either rolls or lookups
    Lookup(Vec<String>),          // we get the roll from the db, there shouldn't be anything else
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的`3 goblin`示例被`validate_input()`到`Command::Multiplier(3, vec!["goblin"])`解析，它们将依次运行`Lookup("goblin")`三次。在`roll_strs()`中，我们通过调用返回命令中的`run()`来结束事情。这个方法返回我们的`Outcomes` :

```
impl Command {
    pub fn run(&self) -> Outcomes {
        match self {
            // a branch for each Command variant
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`Multiplier`并不十分有趣——它将在这里运行`Lookup`命令三次，返回的`Outcomes`将包含所有三个结果。让我们直接跳到(重要的部分)`Lookup` :

```
Command::Lookup(ids) => {
                let conn = DB_POOL
                    .get()
                    .expect("Could not get db conn from thread pool");
                let items = get_items(&conn);
                let mut ret = Vec::new();
                for id in ids {
                    // look for each passed in item in the returned db items
                    // if found, get an Outcome from the associated roll and push it to ret
                    // log output
                }
                Outcomes { outcomes: ret } // return an Outcomes struct 
```

Enter fullscreen mode Exit fullscreen mode

### 猎妖精

在我们与数据库交互之前，我们需要获得一个连接。我使用 [`r2d2`](https://github.com/sfackler/r2d2) 箱来维护一个开放数据库连接池，而不是为每个请求创建一个新的连接池。下面是来自 [`db.rs`](https://github.com/deciduously/roll/blob/master/src/db.rs) :
的相关代码

```
lazy_static! {
    pub static ref DB_POOL: Pool = init_pool();
}

pub type Pool = r2d2::Pool<ConnectionManager<SqliteConnection>>;

pub const DATABASE_URL: &str = dotenv!("DATABASE_URL");

pub fn init_pool() -> Pool {
    let manager = ConnectionManager::<SqliteConnection>::new(DATABASE_URL);
    r2d2::Pool::new(manager).expect("failed to create pool")
} 
```

Enter fullscreen mode Exit fullscreen mode

这是标准的 r2d2 样板文件，它使用项目文件夹中的`.env`文件中定义的数据库位置来设置静态的`DB_POOL`。为了抓住联系，我们使用`let conn = DB_POOL.get()`。一件好事是，当`conn`在这个块的末尾超出范围时，连接将自动返回到我们的池中。我们不需要自己做任何事情。

现在我们可以使用这个 db 连接调用`get_items(&conn)`。我用的是 [`diesel`](https://http://diesel.rs) ORM:

```
pub fn get_items(conn: &SqliteConnection) -> Items {
    use schema::items::dsl::*;
    let results = items
        .limit(5)
        .load::<Item>(conn)
        .expect("Error loading items");

    let mut ret = Vec::new();
    for item in results {
        ret.push(item);
    }
    Items { items: ret }
} 
```

Enter fullscreen mode Exit fullscreen mode

`Items`返回类型是`Vec<Item>`的包装器结构。`Item`看起来是这样的:

```
#[derive(Debug, Queryable, Serialize)]
pub struct Item {
    pub id: i32,
    pub title: String,
    pub damage: String,
} 
```

Enter fullscreen mode Exit fullscreen mode

这与我们的数据库模式完全匹配。Diesel 提供了`Queryable`特征，这意味着它可以自动将 SQLite 数据库中的条目整理成这种 Rust 类型。`items`表是用下面的 SQL 创建的:

```
CREATE TABLE items (
       id INTEGER NOT NULL PRIMARY KEY,
       title VARCHAR NOT NULL,
       damage TEXT NOT NULL
) 
```

Enter fullscreen mode Exit fullscreen mode

Diesel 为我们创建了一个 DSL，使用一个质朴的 API 来编写查询。挺好用的。

这个特殊的例子是从数据库中获取所有的条目，因为命令可能需要查找多个字符串。这是相当糟糕的设计(*咳*T5】奔 T7】咳)。如果只有一个:
，我可以优化它使用这样的语法

```
let results = items
        .filter(title.eq(lookup_title))
        .load::<Item>(conn)
        .expect("Error loading items"); 
```

Enter fullscreen mode Exit fullscreen mode

这会运行一个`SELECT * FROM items WHERE title = lookup_title`。

综上所述，我们的`goblin`的`Lookup`返回类似于:

```
Item {
    id: 1,
    title: "goblin",
    damage: "3d8",
} 
```

Enter fullscreen mode Exit fullscreen mode

`Command::run()`中剩余的`Lookup`块只是抓取那个伤害场并执行掷骰，保存结果作为响应。下面是输出的例子:

```
{"outcomes":  [  {"roll":"3d8","rolls":[6,1,3]},  {"roll":"3d8","rolls":[7,1,8]},  {"roll":"3d8","rolls":[8,1,5]}  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

### 回到前面

咻！Rust 的任务完成了——找到了我们的数据库条目，并使用我们的自定义`Responder`实现发回了一个 JSON 响应，我们必须将它显示给用户。

回想一下，在我们的重新帧事件中，我们为`:on-success`和`:on-failure`定义了一个效果。这种滚动是*蓬勃发展*的成功，所以当这个响应返回时，重新帧调度程序将在`events.cljs` :
触发`::save-roll`事件

```
(re-frame/reg-event-fx  ::save-roll  [(re-frame/inject-cofx  :now)  (re-frame/inject-cofx  :temp-id)]  (fn-traced  [{:keys  [db  temp-id  now]}  [_  result]]  {:db  (update  db  :roll-hx  conj  {:id  temp-id  :time  now  :result  result})})) 
```

Enter fullscreen mode Exit fullscreen mode

这又是我们的老朋友，但这次有了更多的变化。还记得我提到然后完全放弃了*协同效应*的概念吗？在我们打开 lambda 之前，我们使用`re-frame/inject-cofx`向`reg-event-fx`可以使用的上下文添加一些数据，而不仅仅是应用数据库。在 Clojure 中，*everything*只是数据。有点像以前传入的`event`只是我们创建的向量，可以被析构，`cofx`只是一个 Clojure 地图。默认情况下，它包含我们的应用程序的`db`，但我们有机会把我们想要的任何东西放在上面。这是一个比概念更花哨的名字，但我不得不承认这是一个相当准确的名字。让我们看看`:now`，我们注入的第一个协同效应:

```
(re-frame/reg-cofx  :now  (fn-traced  [cofx  _data]  (assoc  cofx  :now  (js/Date.)))) 
```

Enter fullscreen mode Exit fullscreen mode

它看起来不完全一样。本质上，它所做的就是用关键字`:now`向我们的`cofx`地图添加一个关键字，并给它一个值的当前日期。

现在，我们不再用下划线把它去掉，而是析构了`cofx`和`event` :

```
[{:keys  [db  temp-id  now]}  [_  result]] 
```

Enter fullscreen mode Exit fullscreen mode

第二部分，`[_ result]`，正是我们之前对`cmd`所做的——向量的第一个元素是事件的名称(`::save-roll`，我们不需要它，`result`将保存我们刚刚在后端生成的 JSON。第一部分是我们新增加的`cofx`地图。我们特别获取指定键值。`db`是已经存在供我们使用，并代表应用程序的状态，而`now`是我们刚刚注入的-这是当前日期。`temp-id`是我注册的另一个 co-effect——可以在`events.cljs`随意查看。它只允许我们通过在每次注入结果时撞击一个`atom`来为每个输入的结果分配会话本地唯一的增量 id。

这个事件的主体只是使用`conj` : `{:id temp-id :time now :result result}`将一个包含这个结果的地图以及我们的协同效应生成的日期和临时 ID 附加到我们的应用程序 db 中的`:roll-hx`键。

### 带回家

其余的会自动发生。这就是我们的调用链的结尾——我没有更多的代码可以遵循。不过，我们改变了数据库。Re-Frame 从这里得到它——它将处理页面重新渲染，选择我们新增加的`:roll-hx`,因为我们有一个组件*订阅了*。

这是我们的主面板:

```
(defn  main-panel  []  (let  [result  (re-frame/subscribe  [::subs/results])  error  (re-frame/subscribe  [::subs/error])  items  (re-frame/subscribe  [::subs/items])]  [:div  [:h1  "ROLL"]  [usage]  "Roll history:  "  [roll-hx  @result]  [:br]  [command-input]  [:br]  "Items: "  [all-items  @items]  [:br]  [add-item]  [:br]  [view-error  @error]  [:hr]  [footer]])) 
```

Enter fullscreen mode Exit fullscreen mode

有问题的组件是`[roll-hx @result]`。这个`result`是使用`re-frame/subscribe`在`let`绑定中创建的。我们所有的订阅都住在 [`subs.cljs`](https://github.com/deciduously/roll/blob/master/src/cljs/roll/subs.cljs) 。这里是`::subs/results` :

```
(re-frame/reg-sub  ::results  (fn  [db]  (:roll-hx  db))) 
```

Enter fullscreen mode Exit fullscreen mode

再简单不过了——它只是从我们的数据库中返回`:roll-hx`键的值。当应用程序启动时，我们按照 [`db.cljs`](https://github.com/deciduously/roll/blob/master/src/cljs/roll/db.cljs) :
中的定义初始化这个`db`

```
(def  default-db  {:name  "re-frame"  :roll-hx  []  :items  []}) 
```

Enter fullscreen mode Exit fullscreen mode

我们的`::save-roll`事件具有将新地图附加到`:roll-hx`的效果。现在它看起来像:

```
{:name  "re-frame"  :roll-hx  [{:id  0  :time  (js/Date.)  :result  {:outcomes  [  {:roll  "3d8"  :rolls  [6,1,3]},  {:roll  "3d8"  :rolls  [7,1,8]},  {:roll  "3d8"  :rolls  [8,1,5]}  ]]}  ]}}]  :items  []}) 
```

Enter fullscreen mode Exit fullscreen mode

因为视图订阅了我们数据库的`:roll-hx`键，所以它会自动重绘以显示新数据。这很好，因为组件不需要知道数据库的结构——它只关心那个特定的键。如果数据库结构随着您的开发而改变，您将改变*订阅*逻辑——您的视图不需要关心。

在实际视图中没有什么太令人惊讶的——它将数据呈现为一个列表。我不会遍历整个树，这是非常琐碎的事情——这里没有状态，它只是反映了应用程序数据库。下面是外层:

```
(defn  roll-hx  "View full roll history"  [hx]  [:ul.hx  (for  [os  (reverse  hx)]  ^{:key  (:id  os)}  [:li  [outcomes  os]])]) 
```

Enter fullscreen mode Exit fullscreen mode

一直向下运行。我们做到了，重塑风格！我将把杀死妖精作为读者的一个练习。