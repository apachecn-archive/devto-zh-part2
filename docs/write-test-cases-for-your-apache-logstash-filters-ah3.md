# 为 Apache Logstash 过滤器编写测试用例

> 原文：<https://dev.to/oivoodoo/write-test-cases-for-your-apache-logstash-filters-ah3>

大约一年前，我开始为 BI 平台进行 ELK(ElasticSearch，Logstash，Kibana)设置工作。当我在处理传入 Logstash 的事件时，我发现如果你有复杂的正则表达式、结构或条件，那么修改过滤器是很烦人的。

过去几年我在 Ruby On Rails 开发方面有很多经验，为了节省时间，我决定用 RSpec 测试用例来介绍 Logstash 过滤器，因为 logstash 在他们的包和 gem 中使用 jruby 来分发扩展。

此外，在 docker 中包含所有内容以跳过本地机器上的 Logstash 和 gem 安装也很棒。

让我们从 Dockerfile 文件开始，然后我将逐步扩展项目结构。

```
FROM logstash:2.4

# Install Rspec related dependencies
RUN logstash-plugin install --development

# Install prod dependencies
RUN logstash-plugin install logstash-filter-prune

ARG ES_PLUGIN=logstash-output-elasticsearch-6.2.4-java.gem
ARG KAFKA_PLUGIN=logstash-input-kafka-7.0.0.gem

COPY gems/${ES_PLUGIN} /tmp/${ES_PLUGIN}
RUN logstash-plugin install /tmp/${ES_PLUGIN}

COPY gems/${KAFKA_PLUGIN} /tmp/${KAFKA_PLUGIN}
RUN logstash-plugin install /tmp/${KAFKA_PLUGIN} 
```

Enter fullscreen mode Exit fullscreen mode

`gems/`包含用于 logstash 设置的冻结 gem 版本。

运行测试用例的更快方法让我们定义 Makefile:

```
NAME = your_logstash

build:
    docker build -t $(NAME) .
.PHONY: build

clean:
    docker rmi --force $(NAME)
.PHONY: clean

test:
    @docker run --rm -t -i \
        -v `pwd`/../:/app \
        -w /app \
        $(NAME) \
        /bin/bash -c "rspec /app/logstash/spec/$(TEST_CASE)"
.PHONY: test

console:
    @docker run --rm -t -i \
        -v `pwd`/../:/app \
        -w /app \
        $(NAME) \
        /bin/bash
.PHONY: console 
```

Enter fullscreen mode Exit fullscreen mode

`make test` -在你的容器内运行 rspec
`make console`-打开 docker 容器内的交互终端，以防你想要手动运行所有东西并调试(`binding.pry`)你的规范。

现在开始研究 rspec 的根将会很棒。

```
require "logstash/devutils/rspec/spec_helper"
require 'rspec'
require 'rspec/expectations'

require 'ostruct'
require 'erb'
require 'yaml'
require 'json'

# running the grok code outside a logstash package means
# LOGSTASH_HOME will not be defined, so let's set it here
# before requiring the grok filter
# coming from the original examples for logtash specs.
unless LogStash::Environment.const_defined?(:LOGSTASH_HOME)
  LogStash::Environment::LOGSTASH_HOME = File.expand_path("../", __FILE__)
end

module Helpers
  ROOT_PATH = File.dirname(File.expand_path(__FILE__))
  TEMPLATES_PATH = File.join(ROOT_PATH, '..', 'conf.d/')

  def load_fixture(filename, settings = {})
    message = File.read(File.join(ROOT_PATH, 'fixtures', filename))
    settings.merge('message' => message)
  end

  def load_filter(filename, render_vars = {})
    content = File.read(File.join(TEMPLATES_PATH, filename))

    render_vars = OpenStruct.new(render_vars)

    # it's not elegant enough :) but found it as the easiest way to handle
    # jinja2 simple variables replacement.
    template = ERB.new(content.gsub('{{', '<%=').gsub('}}', '%>'))
    template.result(render_vars.instance_eval { binding })
  end
end

require "logstash/filters/grok" 
```

Enter fullscreen mode Exit fullscreen mode

在我的 logstash filters 中，我使用 jinja2 python 库`{{ }}`中的语句，通过使用 ansible 替换 deploy 中的变量。

现在我们准备定义实际的规范来测试我们的 logstash 过滤器。

让我们假设您想要正则表达式类似于`username=<username>`的行。在`spec/filters`文件夹中定义你的`filter_spec.rb`。过滤器不应该处理输入和输出 logstash 语句。相反，我们应该阅读 fixture 和要处理的预期行的示例，然后应用过滤器。

```
require_relative '../spec_helper'

describe 'elb' do
  extend Helpers

  # set config using our defined filter in `conf.d/01-filter.conf` file.
  config load_filter('01-filter.conf')

  # You can define in spec/fixtures/sample1.txt
  # Example: username=oivoodoo
  sample(load_fixture('sample1.txt', 'type' => 'your-source-data')) do
    insist { subject.get('username') } == 'oivoodoo'
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
filter {
  if [type] == "{{ type }}" {
    grok {
      match => {
        "message" => [
          "%{WORD:username}"
        ]
      }
    }

    prune {
      blacklist_names => [
        "@version",
        "message"
      ]
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我相信这应该足以测试你的过滤器。我发现节省时间比部署和等待新的数据非常有用。也很容易添加`ruby`过滤器内的过滤器，并放置`binding.pry`和验证`event`对象。