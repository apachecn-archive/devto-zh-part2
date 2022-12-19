# 推出“sparkpost ”,这是一个针对电子邮件 API 的 Rust Crate

> 原文：<https://dev.to/mygnu/introducing-sparkpost-a-rust-crate-for-email-api-3l92>

Rust 是一种有趣的语言，我已经尝试了一段时间。我开始使用包括 rocket 和 actix 在内的不同机箱创建一些 web 服务器。我需要使用电子邮件 api 从后端向自己或用户发送消息。

在我的一些`nodejs`应用程序中，我已经将 [Sparkpost](https://sparkpost.com) 用作电子邮件服务。没有为它找到任何`rust` API 库，我决定为自己创建一个。在 https://crates.io/crates/sparkpost 的[有售。](https://crates.io/crates/sparkpost)

当前实现仅包括传输 api。它允许你发送带有元数据、模板和其他功能的电子邮件。

##### 这里是一个如何使用 lib 的完整例子

```
[dependencies]
sparkpost="0.4"
serde_json="1.0"
serde_derive = "1.0"
serde = "1.0"
chrono = { version = "0.4", features = ["serde"] }
dotenv = "0.13" 
```

```
// main.rs

#[macro_use]
extern crate serde_derive;
extern crate chrono; // for setting dateTime option to an email
extern crate dotenv; // optional- to read api key from .env file
extern crate serde;
extern crate sparkpost;

use chrono::prelude::*;
use sparkpost::transmission::{
  Attachment, EmailAddress, Message, Options, Recipient, Transmission, TransmissionResponse,
};

// this could be any Type to replace vlaues in sparkpost template
// only requirement is that it implements Serialize from serde
#[derive(Debug, Serialize)]
struct Data {
  name: String,
}

#[allow(unused)]
fn get_api_key() -> String {
  use dotenv::dotenv;
  use std::env;
  dotenv().ok();
  env::var("SPARKPOST_API_KEY").expect("SPARKPOST_API_KEY must be set")
}

fn main() {
  // for eu servers use 
  // let tm = Transmission::new_eu(get_api_key());
  let tm = Transmission::new(get_api_key());

  // new email message with sender name and email
  let mut email = Message::new(EmailAddress::new(
    "marketing@example.sink.sparkpostmail.com",
    "Example Company",
  ));

  let options = Options {
    open_tracking: true,
    click_tracking: true,
    transactional: false,
    sandbox: false,
    inline_css: false,
    start_time: Some(Utc.ymd(2019, 1, 1).and_hms(0, 0, 0)),
  };

  // recipient with substitute data for the template
  let recipient = Recipient::with_substitution(
    EmailAddress::new("bob@company.com", "Bob"),
    Data { name: "Bob".into() },
  );

  let attachment = Attachment {
       file_type: "image/png".into(),
       name: "AnImage.png".into(),
       // data is base64 encoded string
       data: "iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAAAAXNSR0IArs4c6QAAAAlwSFlzAAAmCC".into(),
   };

  // complete the email message with details
  email
    .add_recipient(recipient)
    .add_attachment(attachment)
    .options(options)
    .campaign_id("marketing_blitz")
    .subject("My Awesome email 😎")
    .html("<h1>hello {name}</h1>")
    .text("hello {name}");

  let result = tm.send(&email);

  match result {
    Ok(res) => {
      match res {
        TransmissionResponse::ApiResponse(api_res) => {
          println!("API Response: \n {:#?}", api_res);
            assert_eq!(1, api_res.total_accepted_recipients);
            assert_eq!(0, api_res.total_rejected_recipients);
        }
        TransmissionResponse::ApiError(errors) => {
          println!("Response Errors: \n {:#?}", &errors);
        }
      }
    }
    Err(error) => {
      println!("error \n {:#?}", error);
    }
  }
} 
```

正如我上面提到的，有许多功能是缺乏的，即保存和检索模板和收件人名单等。原因是我现在不使用这些功能。如果有人感兴趣，我将非常乐意帮忙。

[推特](https://Twitter.com/mygnu_)

### 原载于 [hgill.io](https://hgill.io/posts/introducing-rust-sparkpost/)