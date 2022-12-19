# 构建我的第一个 Rust 项目:Wercker 构建状态

> 原文：<https://dev.to/thermatix/building-my-first-rust-project-wercker-build-status-3fkl>

# 简介

警告这是我的第一篇科技博客，所以我希望你能原谅任何错误或写得不好的部分。

我选择的主要语言是 ruby，我喜欢它的易用性，它的简单性和表现力，它的不完善性使它成为使用的梦想，但是它很慢，缺乏清晰的类型，如果你没有意识到 ruby 是通过值和引用传递的，甚至可能会出现微妙的错误，正如你在这里看到的:

```
a = 'hello '
b = a
b << 'world'
puts a
=> 'hello world' 
```

Enter fullscreen mode Exit fullscreen mode

虽然它的所有优点使快速构建某个东西变得容易，但它会使长期维护变得困难。

输入 Rust！

就我而言，Rust 是 ruby 的完美搭档，它在 ruby 慢的地方快，在 ruby 慢的地方类型化，由于它的性质，你永远不需要担心未定义的行为或微妙的错误。

为此，我决定学习 rust 来衬托 ruby。学习生锈很难，但我觉得很值得。

# 工具- [Werker 构建状态](https://github.com/thermatix/wercker_build_status)

这个工具之所以存在，是因为在我的工作中，我们使用 Oracle 的 Wercker(我一直称它为“破坏者”)来处理持续集成，但我倾向于不注意构建失败的时间，所以我想我应该构建一个工具来在 Tmux 状态行中显示我最后一次构建的状态。

在我开始使用这个工具之前，当我使用这种语言时，我总是会感到迷失，我可以有把握地说，这是我第一次使用 rust，并且感觉我知道我在做什么(或多或少)。

最初我打算围绕 Wercker CLI 工具构建一个 bash 脚本，但后来我意识到这不是一个大型项目，它的范围也很小，这对于我的第一个 Rust 项目来说不是很完美吗？所以我做了，我从计划开始。

# 规划

我意识到，我的工具可以被分解成几个步骤，然后我可以用它们来计算出我可以用什么样的板条箱来使它变得更简单(我承认我通常不这么做)。

1)加载用户配置- [配置](https://crates.io/crates/config)
2)获取运行列表- [curl](https://crates.io/crates/curl)
3)反序列化 json - [serde_json](https://crates.io/crates/serde_json)

我承认，通常我会跳进 ruby，开始敲打代码，但是由于 rust 的本质，以及我对它不是很了解的事实，我认为计划它是一个好主意，它最终对我有利。

# 加载用户配置

老实说，我发现这是所有步骤中最简单的，因为文档很容易理解，对我来说，我只是简单地添加了一个函数来加载配置:

```
use config_rs;

use std::collections::HashMap;
type Config = HashMap<String, String>;

fn load(config_file: String) -> Config {
    // create config object
    let mut settings = config_rs::Config::default();
    // check if config_file was supplied, if it was then load it 
    if config_file != "" {
        settings.merge(config_rs::File::with_name(config_file.as_str())).unwrap();
    }
    //check for and add env variables
    settings.merge(config_rs::Environment::with_prefix(::CONFIG_PREFIX)).unwrap();
    settings.try_into::<Config>().unwrap()
} 
```

Enter fullscreen mode Exit fullscreen mode

最初，我用 if 语句检查每个键是否存在，但最终我发现这是最简单的方法，但仍然保持了灵活性:

```
 // check if config options exists
let keys: [&str;3] = ["token", "author", "pipeline_id"];

match keys.iter().position( |key| {
    !settings.contains_key(&key.to_string())
}) {
    Some(i) => println!( "No `{}` detected in config or env ({}_{}) variables",
            keys[i], CONFIG_PREFIX, keys[i].to_uppercase()),
    None    => {
} 
```

Enter fullscreen mode Exit fullscreen mode

# 获取运行列表

这就是事情开始变得棘手的地方，设置 Curl 客户端相当简单:

```
use curl::easy::Easy;

mod urls;

pub fn set_up(token: &String) -> Easy {

    use curl::easy::List;

    let mut easy = Easy::new();
    let mut list = List::new();

    // add authorisation header to curl client
    list.append(&format!("Authorization: Bearer {}",token)).unwrap();
    easy.http_headers(list).unwrap();
    easy
} 
```

Enter fullscreen mode Exit fullscreen mode

我创建了一个函数来处理 get 请求:

```
fn get(curl: &mut Easy, url: String) -> String {
    use std::str;

    let mut data = Vec::new();

    curl.url(url.as_str()).unwrap();
    // add mechanism to transfer data recived data into a vec
    {
        let mut transfer = curl.transfer();
        transfer.write_function(|new_data| {
            data.extend_from_slice(new_data);
            Ok(new_data.len())
        }).unwrap();
        transfer.perform().unwrap();
    }
    // Return Data!
    data
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，这是我遇到的第一个问题，我期待一个 JSON 字符串，但我得到了一个`Vec[u8]`它让我有点困惑，但后来我意识到 curl 输出的是原始字节，有点挖掘，答案是将最后一行改为这个:

```
// convert byte result into utf_8 string
str::from_utf8(&data).unwrap().to_string() 
```

Enter fullscreen mode Exit fullscreen mode

现在我得到了一个 JSON 字符串！

# 反序列化 json

起初，我满足于按作者过滤构建运行，然后获取第一个反序列化的对象:

```
let mut client = set_up_client(&settings["token"]);
let runs: Value = serde_json::from_str(get_runs(&mut client,&settings["author"],
       &settings["pipeline_id"]).as_str()).unwrap();
println!("{}",runs[FIRST]); 
```

Enter fullscreen mode Exit fullscreen mode

然而，由于 Wercker API 上的一个 bug，这是不可能的。问题是，无论我将作者设置为什么(用户名，随机字母)，它总是会返回同一个错误用户的最后一次构建。

这意味着我不得不取而代之获取最后 20 次运行，并手动找到第一次运行的匹配用户名，为此我必须创建结构来表示我想要的数据:

```
pub type Runs = Vec<Run>;

// only define the structs for the data I want to pull out
#[derive(Debug,Serialize, Deserialize)]
pub struct Run {
    pub status: String,
    pub result: String,
    pub user: User,
}

#[derive(Debug,Serialize, Deserialize)]
pub struct User {
    pub meta: Meta,
}

#[derive(Debug,Serialize, Deserialize)]
pub struct Meta {
    pub username: String
} 
```

Enter fullscreen mode Exit fullscreen mode

然后反序列化到我的结构:

```
let runs: Runs = serde_json::from_str(client::get_runs(&mut client,&settings["pipeline_id"]).as_str()).unwrap(); 
```

Enter fullscreen mode Exit fullscreen mode

老实说，我不想这样做，因为基本的反序列化很好地满足了我的需求，因为我不打算存储结果，但是`Serde`不会合作得很好，我尝试这样做:

```
runs.as_array().iter().find() 
```

Enter fullscreen mode Exit fullscreen mode

然而，我发现 serde 只返回一个包含整个 JSON 字符串的单个元素的`Vec[String]`,所以手动创建反序列化的数据是唯一的方法。

我发现的另一个问题是，遵循 serde 文档给了我一个令人困惑的结果:

```
let runs: Value = serde_json::from_str(client::get_runs(&mut client,&settings["pipeline_id"]).as_str())?; 
```

Enter fullscreen mode Exit fullscreen mode

它不喜欢我使用`?`操作符，事实证明我完全忽略了这样一个事实:你不能在 main 中使用它，也不能在 main 中使用`try!`，因为它们都返回，而你不能在 main 中返回，将`?`改为`unwrap()`解决了这个问题。

# 显示结果

最后，我遍历`Vec[Runs]`并显示第一个匹配的用户名:

```
match runs.iter().find( |run| {
    &run.user.meta.username.to_lowercase() == &settings["author"].to_lowercase()
}) {
    // print out status and result
    Some(run) => {
        if settings.contains_key("tmux") && &settings["tmux"] == &"true".to_string() {

            match run.result.as_ref() {
                "failed"    => println!("##[fg=red,bold]{}",&run.result),
                "passed"    => println!("#[fg=green,bold,bright]{}",&run.result),
                "aborted"    => println!("#[fg=yellow,bold,bright]{}",&run.result),
                _           => println!("#[fg=blue,bright]{}",&run.status)
            }
        } else {
            match run.result.as_ref() {
                "failed"    => println!("{}",&run.result.red()),
                "passed"    =>  println!("{}",&run.result.green()),
                "aborted"    =>  println!("{}",&run.result.yellow()),
                _           =>  println!("{}",&run.status.blue(),)
            }
        }
    },
    None => print!("{}","None Found")

} 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

学习 Rust 是值得的，我用 basic 开发的这个工具向我展示了我可以用这种语言开发东西。有些人有健忘的倾向，不会注意到什么时候事情稍微有点不对劲，rust 在这方面帮助很大，因为当事情不对劲时它就不会编译，所以如果它能编译就很好了(当然除了任何错误)。

有几件事我在这里没有涉及，因为重点不是展示整个程序，如果你想看到它的全部，你可以看看回购工具存储在这里可以找到[这里](https://github.com/thermatix/wercker_build_status)。

我真的很高兴我花时间学习 rust，我觉得它对我未来的努力会有帮助。

感谢您花时间阅读这个回顾展，我希望它对您有所帮助。