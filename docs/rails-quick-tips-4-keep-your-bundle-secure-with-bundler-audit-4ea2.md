# Rails 快速提示#4:使用 bundler-audit 保护包的安全

> 原文：<https://dev.to/citizen428/rails-quick-tips-4-keep-your-bundle-secure-with-bundler-audit-4ea2>

[`bundler-audit`](https://github.com/rubysec/bundler-audit) 是一个小工具，可以对照[红宝石咨询数据库](https://github.com/rubysec/ruby-advisory-db)检查你`Gemfile`的内容。

你可以简单地通过`bundle audit`运行它，它将报告不安全的 gem 源以及具有已知漏洞的库版本:

```
$ bundle audit
Insecure Source URI found: git://github.com/compass/compass-rails.git
Insecure Source URI found: git://github.com/sinatra/sinatra.git
Name: nokogiri
Version: 1.8.2
Advisory: CVE-2018-8048
Criticality: Unknown
URL: https://github.com/sparklemotion/nokogiri/pull/1746
Title: Revert libxml2 behavior in Nokogiri gem that could cause XSS
Solution: upgrade to >= 1.8.3

Name: paperclip
Version: 4.3.7
Advisory: CVE-2017-0889
Criticality: High
URL: https://github.com/thoughtbot/paperclip/pull/2435
Title: Paperclip ruby gem suffers from a Server-Side Request Forgery (SSRF) vulnerability
in the Paperclip::UriAdapter and Paperclip::HttpUrlProxyAdapter class.
Solution: upgrade to >= 5.2.0

Vulnerabilities found! 
```

Enter fullscreen mode Exit fullscreen mode

要更新 Ruby Advisory DB 的本地副本，可以使用下面的命令:

```
$ bundle audit update
Updating ruby-advisory-db ...
From https://github.com/rubysec/ruby-advisory-db
 * branch            master     -> FETCH_HEAD
Already up to date.
Current branch master is up to date.
Updated ruby-advisory-db
ruby-advisory-db: 317 advisories 
```

Enter fullscreen mode Exit fullscreen mode

您还可以通过`bundle audit check --update`命令将这两种操作结合起来，我们将该命令作为 CI 管道的一部分来执行。

额外提示:当更新你易受攻击的 gem 时，你可能希望保持最小的变化，并且 [`bundle update`](https://bundler.io/man/bundle-update.1.html) 有一个有用的`--conservative`选项，它不会更新任何共享的依赖项。