# AlloyCI v0.7.0 发布！

> 原文：<https://dev.to/supernova32/alloyci-v070-released-360a>

AlloyCI v0.7.0 已经发布！这个版本包括一些*重大*变化。继续阅读以获得更好的描述，或者去[这里](https://github.com/AlloyCI/alloy_ci/releases/tag/v0.7.0)查看代码。

## 折旧

这个版本最大的变化是 JSON 配置文件已经被弃用。你将不能再用它们来配置你的合金建造。从现在开始，你将需要一个 YAML 配置文件。

这个决定背后的主要原因是 YAML 比 JSON 灵活得多。它允许我们使用别名以避免重复，添加注释以使配置文件更容易理解，并且它是市场上几乎所有 CI 系统的事实上的标准配置文件。

当我第一次开始编写 AlloyCI 时，我最初的意图是将 YAML 用于配置文件，但是在那个时候，YamlElixir 库并不支持所有现代 YAML 特性。几个月前，这些特性被添加到库中，但它仍然缺乏对别名的适当支持。我决定看看添加这个功能有多难。幸运的是它非常简单，所以现在 AlloyCI 使用了自己的 YamlEixir 库的[分支，并带有对别名](https://github.com/AlloyCI/yaml-elixir)的[适当支持。](https://github.com/AlloyCI/yaml-elixir/commit/9086dbb4c46adbb5e2e140b00a3bb16c656586aa)

此外，YAML 使配置文件更容易编写和阅读。例如，下面是用于 AlloyCI 的 JSON 格式的配置文件:

```
{  "image":  "elixir:latest",  "services":  [  "postgres:9.6"  ],  "cache":  {  "paths":  [  "_build/",  "deps/"  ]  },  "variables":  {  "MIX_ENV":  "test",  "GITHUB_CLIENT_ID":  "fake-id",  "GITHUB_CLIENT_SECRET":  "fake-secret",  "GITHUB_SECRET_TOKEN":  "fake-token",  "SECRET_KEY_BASE":  "NULr4xlNDNzEwE77UHdId7cQU+vuaPJ+Q5x3l+7dppQngBsL5EkjEaMu0S9cCGbk",  "DATABASE_URL":  "postgres://postgres@postgres:5432/alloy_ci_test"  },  "before_script":  [  "mix local.hex --force",  "mix local.rebar --force",  "mix deps.get",  "mix ecto.setup"  ],  "mix + coveralls":  {  "stage":  "test",  "tags":  [  "elixir",  "postgres"  ],  "script":  [  "mix coveralls.post --branch \"$CI_COMMIT_REF_SLUG\" --name \"$CI_SERVER_NAME\" --sha \"$CI_COMMIT_SHA\" --committer \"$CI_COMMIT_PUSHER\" --message \"$CI_COMMIT_MESSAGE\""  ]  },  "credo + formatter":  {  "stage":  "test",  "tags":  [  "elixir"  ],  "script":  [  "mix credo",  "mix format --check-formatted"  ]  }  } 
```

这是在 YAML 完全相同的配置:

```
image: elixir:latest

services:
- postgres:9.6

cache:
  paths:
  - _build/
  - deps/

variables:
  MIX_ENV: test
  GITHUB_CLIENT_ID: fake-id
  GITHUB_CLIENT_SECRET: fake-secret
  GITHUB_SECRET_TOKEN: fake-token
  SECRET_KEY_BASE: NULr4xlNDNzEwE77UHdId7cQU+vuaPJ+Q5x3l+7dppQngBsL5EkjEaMu0S9cCGbk
  DATABASE_URL: postgres://postgres@postgres:5432/alloy_ci_test

before_script:
- mix local.hex --force
- mix local.rebar --force
- mix deps.get
- mix ecto.setup

mix + coveralls:
  stage: test
  tags:
  - elixir
  - postgres
  script:
  - mix coveralls.post --branch "$CI_COMMIT_REF_SLUG" --name "$CI_SERVER_NAME" --sha
    "$CI_COMMIT_SHA" --committer "$CI_COMMIT_PUSHER" --message "$CI_COMMIT_MESSAGE"

credo + formatter:
  stage: test
  tags:
  - elixir
  script:
  - mix credo
  - mix format --check-formatted 
```

干净多了，对吧？

此外，YAML 允许您在文件中使用注释，这意味着如果您有一个特别复杂的设置，您可以在其中留下注释，以便项目新手更容易理解。

除此之外，YAML 允许你在文件中声明别名，就像一个通用部件，可以在其他声明中重用，以避免额外的输入，例如:

```
image: elixir:latest

# This is the generic part we want to reuse.
.docker: &docker
  image: elixir:1.5
  entrypoint:
  - "/bin/bash"

mix:
  # In here he use that generic part, so everything declared there, will be added here
  <<: *docker
  services:
  - postgres:9.6
  stage: test
  tags:
  - elixir
  - postgres
  script:
  - mix test

credo:
  # Same goes for here
  <<: *docker
  services:
  - postgres:latest
  - redis:latest
  stage: test
  tags:
  - elixir
  script:
  - mix credo

# This one does not use the generic part, so it will use the `image` declared in the first line
distillery:
  tags:
  - elixir
  - postgres
  variables:
    MIX_ENV: prod
  script:
  - mix docker.build --tag latest
  artifacts:
    paths:
    - alloy_ci.tar.gz
    - _build/prod/lib/alloy_ci 
```

AlloyCI 很聪明，不会将以`.`开头的元素视为构建指令，因此不会创建名为`.docker`的构建。

我确信使用 YAML 将会使 AlloyCI 更加友好和易于使用。

## 新增功能

此版本不包括许多新功能。最臭名昭著的一个是管道和构建的状态将通过 websockets 在各自的页面中自动更新。

这意味着，无论您何时在项目视图(查看管道列表)或管道视图(查看构建作业列表)中，只要后端的状态发生变化，每个视图的状态都会自动更新。这是通过凤凰频道完成的。你可以在这里看到为使其工作[所必需的改变。](https://github.com/AlloyCI/alloy_ci/commit/0f2fd7f0ae29254bec1e69019f9c66295afa61a4)

## Bug 修复

*   修复了身份验证令牌过期时会发生的重定向循环。
*   回复到煤油 v0.7.0，因为更新的 v0.8.0 有分页 bug。

* * *

感谢您对 AlloyCI 的关注，希望本次更新对您有用。一如既往，不要犹豫提出任何问题，或通过 GitHub 上的问题跟踪器报告任何错误。

查看 AlloyCI v0.7.0 版！[https://github.com/AlloyCI/alloy_ci/releases/tag/v0.7.0](https://github.com/AlloyCI/alloy_ci/releases/tag/v0.7.0)T3】

🎉🐣