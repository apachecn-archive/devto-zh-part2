# 用铁锈刮去你的页面浏览量

> 原文：<https://dev.to/deciduously/scrape-your-devto-pageviews-with-rust-2dgc>

这里有一个快速“n”肮脏的方法来使用 Rust 将你的新文章分析转储到 CSV。您必须将页面源保存到`src/page.html`。你知道，为了图表什么的。谁不喜欢图表？

这不是抛光-这是我今天的“工作开始前一小时”项目。为你自己的真实版本抓取正则表达式，或者改进这个并展示给我看！

```
extern crate chrono;
extern crate csv;
#[macro_use]
extern crate lazy_static;
extern crate regex;
extern crate select;
extern crate serde;
#[macro_use]
extern crate serde_derive;

use chrono::prelude::*;
use regex::Regex;
use select::{
    document::Document,
    predicate::{Class, Name},
};
use std::{
    error::Error,
    fs::{File, OpenOptions},
};

lazy_static! {
    static ref NOW: DateTime<Local> = Local::now();
    static ref STAT_RE: Regex = Regex::new(".+?([0-9]+).+//.?([0-9]+).+//.?([0-9]+).+").unwrap();
}

#[derive(Debug, Serialize)]
struct Record {
    time: String,
    title: String,
    views: i32,
    reactions: i32,
    comments: i32,
}

impl Record {
    fn new(time: String, title: String, views: i32, reactions: i32, comments: i32) -> Self {
        Self {
            time,
            title,
            views,
            reactions,
            comments,
        }
    }
}

fn write_entries(rs: Vec<Record>, f: File) -> Result<(), Box<Error>> {
    let mut wtr = csv::Writer::from_writer(f);
    for r in rs {
        wtr.serialize(r)?;
    }
    wtr.flush()?;
    Ok(())
}

fn scrape_page(doc: &Document) -> Result<Vec<Record>, Box<Error>> {
    let mut ret = Vec::new();
    for node in doc.find(Class("dashboard-pageviews-indicator")) {
        let text = node.text();
        if STAT_RE.is_match(&text) {
            let title = node
                .parent()
                .unwrap()
                .parent()
                .unwrap()
                .find(Name("a"))
                .next()
                .unwrap()
                .find(Name("h2"))
                .next()
                .unwrap()
                .text();
            for cap in STAT_RE.captures_iter(&text) {
                let r = Record::new(
                    NOW.to_rfc2822(),
                    title.clone(),
                    cap[1].parse::<i32>()?,
                    cap[2].parse::<i32>()?,
                    cap[3].parse::<i32>()?,
                );
                ret.push(r);
            }
        }
    }
    Ok(ret)
}

fn run() -> Result<(), Box<Error>> {
    let doc = Document::from(include_str!("page.html"));
    let file = OpenOptions::new()
        .write(true)
        .create(true)
        .append(true)
        .open("stats.csv")?;
    let entries = scrape_page(&doc)?;
    write_entries(entries, file)?;
    Ok(())
}

fn main() {
    if let Err(e) = run() {
        eprintln!("Error: {}", e);
        ::std::process::exit(1);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*编辑*完成关闭错误处理